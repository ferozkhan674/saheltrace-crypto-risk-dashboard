import { useState, useEffect, useRef, useCallback } from "react";

// ─── Palette & Fonts ────────────────────────────────────────────────────────
const STYLE = `
  @import url('https://fonts.googleapis.com/css2?family=Share+Tech+Mono&family=Barlow+Condensed:wght@300;400;600;700;900&family=Barlow:wght@300;400;500&display=swap');

  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --bg:       #080c10;
    --surface:  #0d1520;
    --panel:    #111c2a;
    --border:   #1e3048;
    --accent:   #00e5ff;
    --warn:     #ffb300;
    --danger:   #ff3d57;
    --safe:     #00e676;
    --muted:    #3a5068;
    --text:     #c8daea;
    --mono:     'Share Tech Mono', monospace;
    --sans:     'Barlow', sans-serif;
    --cond:     'Barlow Condensed', sans-serif;
  }

  body { background: var(--bg); color: var(--text); font-family: var(--sans); }

  ::-webkit-scrollbar { width: 4px; }
  ::-webkit-scrollbar-track { background: var(--bg); }
  ::-webkit-scrollbar-thumb { background: var(--border); border-radius: 2px; }

  @keyframes scanline {
    0% { transform: translateY(-100%); }
    100% { transform: translateY(100vh); }
  }
  @keyframes pulse-ring {
    0% { box-shadow: 0 0 0 0 rgba(0,229,255,0.4); }
    70% { box-shadow: 0 0 0 8px rgba(0,229,255,0); }
    100% { box-shadow: 0 0 0 0 rgba(0,229,255,0); }
  }
  @keyframes blink { 50% { opacity: 0; } }
  @keyframes fade-in { from { opacity:0; transform:translateY(6px); } to { opacity:1; transform:none; } }
  @keyframes shimmer {
    0%   { background-position: -200% center; }
    100% { background-position:  200% center; }
  }
`;

// ─── Helpers ─────────────────────────────────────────────────────────────────
function rnd(a, b) { return Math.floor(Math.random() * (b - a + 1)) + a; }
function fmtAddr(a) { return a.slice(0, 6) + "…" + a.slice(-4); }
function riskColor(score) {
  if (score >= 75) return "var(--danger)";
  if (score >= 40) return "var(--warn)";
  return "var(--safe)";
}
function riskLabel(score) {
  if (score >= 75) return "HIGH";
  if (score >= 40) return "MEDIUM";
  return "LOW";
}

// Simulate blockchain data pull
function mockAnalyse(address) {
  const seed = address.length + address.charCodeAt(2);
  const vol = (seed * 1.37) % 10000000;
  const mixer = (seed % 7) < 2;
  const sanctioned = (seed % 11) < 1;
  const freq = rnd(2, 340);
  const score = Math.min(99,
    (mixer ? 35 : 0) +
    (sanctioned ? 45 : 0) +
    (vol > 5000000 ? 15 : vol > 1000000 ? 8 : 0) +
    (freq > 200 ? 12 : freq > 100 ? 6 : 0) +
    rnd(0, 8)
  );
  const txCount = rnd(12, 1800);
  const chain = ["ETH", "BTC", "USDT-TRC20", "BNB"][seed % 4];
  const flags = [];
  if (mixer) flags.push({ label: "Mixer/Tumbler Interaction", sev: "high" });
  if (sanctioned) flags.push({ label: "Sanctioned Address Contact", sev: "critical" });
  if (vol > 3000000) flags.push({ label: "High-Volume Activity", sev: "medium" });
  if (freq > 150) flags.push({ label: "High Transaction Frequency", sev: "medium" });
  flags.push({ label: "P2P Exchange Activity Detected", sev: "low" });

  // network nodes
  const peers = Array.from({ length: rnd(4, 9) }, (_, i) => ({
    id: `0x${Math.random().toString(16).slice(2, 8)}…${Math.random().toString(16).slice(2, 6)}`,
    vol: rnd(100, 800000),
    broker: i < 2,
    mobile: i >= 2 && i < 5,
    cashout: i >= 5,
  }));

  return { address, score, vol, freq, txCount, chain, mixer, sanctioned, flags, peers };
}

// ─── Sub-components ───────────────────────────────────────────────────────────

function TopBar() {
  const [time, setTime] = useState(new Date());
  useEffect(() => { const t = setInterval(() => setTime(new Date()), 1000); return () => clearInterval(t); }, []);
  return (
    <div style={{
      display: "flex", alignItems: "center", justifyContent: "space-between",
      padding: "10px 24px", borderBottom: `1px solid var(--border)`,
      background: "linear-gradient(90deg, #0a1520 0%, #0d1928 100%)",
      position: "sticky", top: 0, zIndex: 100,
    }}>
      <div style={{ display: "flex", alignItems: "center", gap: 12 }}>
        <svg width="28" height="28" viewBox="0 0 28 28" fill="none">
          <polygon points="14,2 26,8 26,20 14,26 2,20 2,8" stroke="var(--accent)" strokeWidth="1.5" fill="none"/>
          <polygon points="14,7 21,11 21,17 14,21 7,17 7,11" stroke="var(--accent)" strokeWidth="0.8" fill="rgba(0,229,255,0.07)"/>
          <circle cx="14" cy="14" r="2.5" fill="var(--accent)"/>
        </svg>
        <span style={{ fontFamily: "var(--cond)", fontWeight: 900, fontSize: 20, letterSpacing: 4, color: "#fff" }}>
          SAHEL<span style={{ color: "var(--accent)" }}>TRACE</span>
        </span>
        <span style={{ fontFamily: "var(--mono)", fontSize: 10, color: "var(--muted)", marginLeft: 4 }}>v2.4.1 · ANALYST BUILD</span>
      </div>
      <div style={{ display: "flex", gap: 24, alignItems: "center" }}>
        <div style={{ fontFamily: "var(--mono)", fontSize: 11, color: "var(--muted)" }}>
          {time.toUTCString().replace("GMT", "UTC")}
        </div>
        <div style={{ display: "flex", gap: 6 }}>
          {["MODULE-1","MODULE-2","MODULE-3"].map((m, i) => (
            <span key={m} style={{
              fontFamily: "var(--cond)", fontSize: 10, fontWeight: 700, letterSpacing: 2,
              padding: "2px 8px", border: `1px solid var(--border)`,
              color: "var(--accent)", background: "rgba(0,229,255,0.06)",
            }}>{m}</span>
          ))}
        </div>
      </div>
    </div>
  );
}

function RiskGauge({ score }) {
  const color = riskColor(score);
  const label = riskLabel(score);
  const pct = score;
  return (
    <div style={{ display: "flex", flexDirection: "column", alignItems: "center", gap: 8 }}>
      <svg width="120" height="66" viewBox="0 0 120 66">
        <path d="M10,60 A50,50 0 0,1 110,60" stroke="var(--border)" strokeWidth="8" fill="none" strokeLinecap="round"/>
        <path
          d="M10,60 A50,50 0 0,1 110,60"
          stroke={color}
          strokeWidth="8"
          fill="none"
          strokeLinecap="round"
          strokeDasharray={`${(pct / 100) * 157} 157`}
          style={{ filter: `drop-shadow(0 0 6px ${color})`, transition: "stroke-dasharray 1s ease" }}
        />
        <text x="60" y="55" textAnchor="middle" fontFamily="var(--mono)" fontSize="22" fill={color} fontWeight="bold">
          {score}
        </text>
      </svg>
      <span style={{
        fontFamily: "var(--cond)", fontWeight: 900, letterSpacing: 4, fontSize: 13,
        color, padding: "2px 12px", border: `1px solid ${color}`,
        background: `${color}18`,
        animation: score >= 75 ? "pulse-ring 1.8s infinite" : "none",
      }}>{label} RISK</span>
    </div>
  );
}

function FlagBadge({ label, sev }) {
  const colors = { critical: "var(--danger)", high: "#ff6e40", medium: "var(--warn)", low: "var(--muted)" };
  const c = colors[sev] || "var(--muted)";
  return (
    <div style={{
      display: "flex", alignItems: "center", gap: 8, padding: "6px 10px",
      border: `1px solid ${c}44`, background: `${c}0d`,
      animation: "fade-in 0.3s ease both",
    }}>
      <div style={{ width: 6, height: 6, borderRadius: "50%", background: c, flexShrink: 0, boxShadow: `0 0 6px ${c}` }}/>
      <span style={{ fontFamily: "var(--mono)", fontSize: 11, color: "var(--text)" }}>{label}</span>
      <span style={{ marginLeft: "auto", fontFamily: "var(--cond)", fontWeight: 700, fontSize: 10, letterSpacing: 2, color: c }}>{sev.toUpperCase()}</span>
    </div>
  );
}

function StatBox({ label, value, accent }) {
  return (
    <div style={{ padding: "12px 14px", border: "1px solid var(--border)", background: "var(--surface)", flex: 1 }}>
      <div style={{ fontFamily: "var(--cond)", fontSize: 10, letterSpacing: 3, color: "var(--muted)", marginBottom: 4 }}>{label}</div>
      <div style={{ fontFamily: "var(--mono)", fontSize: 18, color: accent || "var(--accent)" }}>{value}</div>
    </div>
  );
}

// ─── Module 1: Address Input + Risk Scoring ───────────────────────────────────
function Module1({ onAnalyse }) {
  const [addr, setAddr] = useState("");
  const [loading, setLoading] = useState(false);
  const [result, setResult] = useState(null);
  const [dots, setDots] = useState("");

  useEffect(() => {
    if (!loading) return;
    const t = setInterval(() => setDots(d => d.length < 3 ? d + "." : ""), 400);
    return () => clearInterval(t);
  }, [loading]);

  const analyse = () => {
    if (!addr.trim()) return;
    setLoading(true);
    setResult(null);
    setTimeout(() => {
      const r = mockAnalyse(addr.trim());
      setResult(r);
      setLoading(false);
      onAnalyse(r);
    }, 1800);
  };

  const presets = [
    "0x3f5CE5FBFe3E9af3971dD833D26bA9b5C936f0bE",
    "bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh",
    "TDqiqXQkMr5snJMbWHQpMJFJcJRTBxkTRm",
  ];

  return (
    <div style={{ display: "flex", flexDirection: "column", gap: 16 }}>
      <SectionHeader icon="◈" label="MODULE 01" title="Address Input & Risk Scoring" />

      <div style={{ display: "flex", gap: 8 }}>
        <input
          value={addr}
          onChange={e => setAddr(e.target.value)}
          onKeyDown={e => e.key === "Enter" && analyse()}
          placeholder="Paste wallet address (ETH / BTC / TRC-20 / BNB)…"
          style={{
            flex: 1, background: "var(--surface)", border: "1px solid var(--border)",
            color: "var(--text)", fontFamily: "var(--mono)", fontSize: 13, padding: "10px 14px",
            outline: "none", transition: "border-color 0.2s",
          }}
          onFocus={e => e.target.style.borderColor = "var(--accent)"}
          onBlur={e => e.target.style.borderColor = "var(--border)"}
        />
        <button
          onClick={analyse}
          disabled={loading || !addr.trim()}
          style={{
            background: loading ? "var(--muted)" : "var(--accent)", color: "#000",
            border: "none", padding: "10px 22px", fontFamily: "var(--cond)",
            fontWeight: 700, fontSize: 13, letterSpacing: 3, cursor: loading ? "wait" : "pointer",
            transition: "background 0.2s",
          }}
        >
          {loading ? `SCANNING${dots}` : "ANALYSE"}
        </button>
      </div>

      <div style={{ display: "flex", gap: 8, flexWrap: "wrap" }}>
        <span style={{ fontFamily: "var(--mono)", fontSize: 10, color: "var(--muted)", alignSelf: "center" }}>PRESETS →</span>
        {presets.map(p => (
          <button key={p} onClick={() => setAddr(p)} style={{
            background: "none", border: "1px solid var(--border)", color: "var(--muted)",
            fontFamily: "var(--mono)", fontSize: 10, padding: "3px 10px", cursor: "pointer",
          }}>{fmtAddr(p)}</button>
        ))}
      </div>

      {loading && (
        <div style={{ padding: 20, border: "1px solid var(--border)", background: "var(--surface)" }}>
          <div style={{ fontFamily: "var(--mono)", fontSize: 11, color: "var(--accent)", marginBottom: 12 }}>
            ◈ QUERYING CHAIN DATA{dots}
          </div>
          {["Resolving address on-chain","Fetching transaction history","Checking OFAC/sanctions lists","Detecting mixer interactions","Running heuristic scoring"].map((s, i) => (
            <div key={s} style={{
              fontFamily: "var(--mono)", fontSize: 10, color: "var(--muted)",
              marginBottom: 4, animation: `fade-in 0.3s ${i * 0.25}s ease both`,
            }}>
              <span style={{ color: "var(--accent)", marginRight: 8 }}>›</span>{s}
            </div>
          ))}
        </div>
      )}

      {result && (
        <div style={{ display: "flex", flexDirection: "column", gap: 12, animation: "fade-in 0.4s ease" }}>
          <div style={{ display: "flex", gap: 12, alignItems: "flex-start", flexWrap: "wrap" }}>
            <div style={{ padding: 20, border: `1px solid ${riskColor(result.score)}44`, background: "var(--surface)", display: "flex", flexDirection: "column", alignItems: "center", gap: 8 }}>
              <RiskGauge score={result.score} />
              <div style={{ fontFamily: "var(--mono)", fontSize: 10, color: "var(--muted)", textAlign: "center" }}>
                COMPOSITE RISK INDEX<br/>
                <span style={{ color: "var(--text)" }}>NOT LEGAL PROOF</span>
              </div>
            </div>
            <div style={{ flex: 1, display: "flex", flexDirection: "column", gap: 8 }}>
              <div style={{ display: "flex", gap: 8, flexWrap: "wrap" }}>
                <StatBox label="CHAIN" value={result.chain} />
                <StatBox label="TX COUNT" value={result.txCount.toLocaleString()} />
                <StatBox label="VOLUME (USD)" value={"$" + result.vol.toLocaleString(undefined, { maximumFractionDigits: 0 })} accent="var(--warn)" />
                <StatBox label="TX / MONTH" value={result.freq} />
              </div>
              <div style={{ display: "flex", gap: 8 }}>
                <StatBox label="MIXER USE" value={result.mixer ? "⚠ DETECTED" : "NONE"} accent={result.mixer ? "var(--danger)" : "var(--safe)"} />
                <StatBox label="SANCTIONS" value={result.sanctioned ? "⚠ HIT" : "CLEAR"} accent={result.sanctioned ? "var(--danger)" : "var(--safe)"} />
              </div>
            </div>
          </div>

          <div style={{ padding: "12px 14px", border: "1px solid var(--border)", background: "var(--surface)" }}>
            <div style={{ fontFamily: "var(--cond)", fontSize: 10, letterSpacing: 3, color: "var(--muted)", marginBottom: 10 }}>RISK FLAGS</div>
            <div style={{ display: "flex", flexDirection: "column", gap: 6 }}>
              {result.flags.map((f, i) => <FlagBadge key={i} {...f} />)}
            </div>
          </div>

          <div style={{
            padding: "8px 14px", border: "1px solid var(--border)", background: "#0a0f16",
            fontFamily: "var(--mono)", fontSize: 10, color: "var(--muted)", lineHeight: 1.7,
          }}>
            ⚠ Risk scores are probabilistic indicators derived from on-chain heuristics. They do not constitute legal evidence of illicit activity. All findings must be corroborated through additional investigative steps.
          </div>
        </div>
      )}
    </div>
  );
}

// ─── Module 2: Context Linking Panel ─────────────────────────────────────────
function Module2({ analysisResult }) {
  const [links, setLinks] = useState([]);
  const [form, setForm] = useState({ type: "exchange", value: "", label: "" });
  const [selectedNode, setSelectedNode] = useState(null);

  const typeOptions = [
    { value: "exchange", label: "Exchange", icon: "⬡" },
    { value: "kyc", label: "KYC Doc", icon: "◻" },
    { value: "phone", label: "Phone #", icon: "◈" },
    { value: "email", label: "Email", icon: "▷" },
    { value: "mobile_wallet", label: "Mobile Wallet", icon: "◆" },
    { value: "comms", label: "Comms Record", icon: "▣" },
    { value: "name", label: "Real Name", icon: "◉" },
  ];

  const typeColors = {
    exchange: "#00b0ff", kyc: "#e040fb", phone: "#ffb300",
    email: "#69f0ae", mobile_wallet: "#ff6e40", comms: "#40c4ff", name: "var(--danger)",
  };

  const addLink = () => {
    if (!form.value.trim()) return;
    setLinks(prev => [...prev, { ...form, id: Date.now(), connectedTo: analysisResult?.address || "—" }]);
    setForm(f => ({ ...f, value: "", label: "" }));
  };

  const chainNodes = analysisResult
    ? [{ id: "root", type: "address", value: fmtAddr(analysisResult.address), color: riskColor(analysisResult.score) }, ...links]
    : links;

  return (
    <div style={{ display: "flex", flexDirection: "column", gap: 16 }}>
      <SectionHeader icon="◉" label="MODULE 02" title="Context Linking Panel" />

      {!analysisResult && (
        <div style={{ padding: "14px 16px", border: "1px dashed var(--border)", fontFamily: "var(--mono)", fontSize: 11, color: "var(--muted)" }}>
          ◈ Analyse an address in Module 01 first to anchor context links.
        </div>
      )}

      {analysisResult && (
        <div style={{ padding: "10px 14px", border: "1px solid var(--accent)22", background: "rgba(0,229,255,0.04)", display: "flex", gap: 12, alignItems: "center" }}>
          <div style={{ width: 8, height: 8, borderRadius: "50%", background: riskColor(analysisResult.score), boxShadow: `0 0 8px ${riskColor(analysisResult.score)}` }}/>
          <div style={{ fontFamily: "var(--mono)", fontSize: 11, color: "var(--text)" }}>
            ANCHOR: <span style={{ color: "var(--accent)" }}>{analysisResult.address}</span>
          </div>
          <span style={{ marginLeft: "auto", fontFamily: "var(--cond)", fontWeight: 700, fontSize: 10, letterSpacing: 2, color: riskColor(analysisResult.score) }}>
            RISK {analysisResult.score}
          </span>
        </div>
      )}

      {/* Add link form */}
      <div style={{ display: "flex", gap: 8, flexWrap: "wrap" }}>
        <select
          value={form.type}
          onChange={e => setForm(f => ({ ...f, type: e.target.value }))}
          style={{
            background: "var(--surface)", border: "1px solid var(--border)", color: "var(--text)",
            fontFamily: "var(--mono)", fontSize: 12, padding: "8px 10px",
          }}
        >
          {typeOptions.map(o => <option key={o.value} value={o.value}>{o.icon} {o.label}</option>)}
        </select>
        <input
          value={form.value}
          onChange={e => setForm(f => ({ ...f, value: e.target.value }))}
          placeholder="Value (e.g. Binance, +223 XX XX, kyc@email.com…)"
          onKeyDown={e => e.key === "Enter" && addLink()}
          style={{
            flex: 1, background: "var(--surface)", border: "1px solid var(--border)",
            color: "var(--text)", fontFamily: "var(--mono)", fontSize: 12, padding: "8px 10px", outline: "none",
          }}
        />
        <input
          value={form.label}
          onChange={e => setForm(f => ({ ...f, label: e.target.value }))}
          placeholder="Note (optional)"
          style={{
            width: 160, background: "var(--surface)", border: "1px solid var(--border)",
            color: "var(--text)", fontFamily: "var(--mono)", fontSize: 12, padding: "8px 10px", outline: "none",
          }}
        />
        <button onClick={addLink} style={{
          background: "var(--accent)", color: "#000", border: "none", padding: "8px 18px",
          fontFamily: "var(--cond)", fontWeight: 700, fontSize: 13, letterSpacing: 2, cursor: "pointer",
        }}>+ LINK</button>
      </div>

      {/* Chain visual */}
      {links.length > 0 && (
        <div style={{ padding: "16px", border: "1px solid var(--border)", background: "var(--surface)" }}>
          <div style={{ fontFamily: "var(--cond)", fontSize: 10, letterSpacing: 3, color: "var(--muted)", marginBottom: 14 }}>IDENTITY CHAIN</div>
          <div style={{ display: "flex", alignItems: "center", flexWrap: "wrap", gap: 0 }}>
            {/* Root node */}
            {analysisResult && (
              <>
                <ChainNode
                  label={fmtAddr(analysisResult.address)}
                  sublabel={analysisResult.chain}
                  color={riskColor(analysisResult.score)}
                  icon="◈"
                />
                <Arrow />
              </>
            )}
            {links.map((lnk, i) => {
              const tc = typeOptions.find(t => t.value === lnk.type);
              const color = typeColors[lnk.type] || "var(--muted)";
              return (
                <div key={lnk.id} style={{ display: "flex", alignItems: "center" }}>
                  <ChainNode label={lnk.value} sublabel={tc?.label} color={color} icon={tc?.icon} note={lnk.label} onClick={() => setSelectedNode(lnk)} />
                  {i < links.length - 1 && <Arrow />}
                </div>
              );
            })}
          </div>
        </div>
      )}

      {/* Link table */}
      {links.length > 0 && (
        <div style={{ border: "1px solid var(--border)" }}>
          <div style={{
            display: "grid", gridTemplateColumns: "100px 1fr 1fr 100px 40px",
            padding: "6px 12px", background: "#0a1018",
            fontFamily: "var(--cond)", fontSize: 10, letterSpacing: 3, color: "var(--muted)",
            borderBottom: "1px solid var(--border)",
          }}>
            <span>TYPE</span><span>VALUE</span><span>NOTE</span><span>ANCHOR</span><span/>
          </div>
          {links.map(lnk => {
            const tc = typeOptions.find(t => t.value === lnk.type);
            const color = typeColors[lnk.type] || "var(--muted)";
            return (
              <div key={lnk.id} style={{
                display: "grid", gridTemplateColumns: "100px 1fr 1fr 100px 40px",
                padding: "8px 12px", borderBottom: "1px solid var(--border)11",
                fontFamily: "var(--mono)", fontSize: 11, alignItems: "center",
                animation: "fade-in 0.3s ease",
              }}>
                <span style={{ color, fontFamily: "var(--cond)", fontWeight: 700, letterSpacing: 2, fontSize: 10 }}>{tc?.icon} {tc?.label?.toUpperCase()}</span>
                <span style={{ color: "var(--text)" }}>{lnk.value}</span>
                <span style={{ color: "var(--muted)" }}>{lnk.label || "—"}</span>
                <span style={{ color: "var(--muted)", fontSize: 10 }}>{fmtAddr(lnk.connectedTo)}</span>
                <button onClick={() => setLinks(l => l.filter(x => x.id !== lnk.id))} style={{
                  background: "none", border: "none", color: "var(--muted)", cursor: "pointer", fontSize: 14,
                }}>×</button>
              </div>
            );
          })}
        </div>
      )}
    </div>
  );
}

function ChainNode({ label, sublabel, color, icon, note, onClick }) {
  return (
    <div onClick={onClick} style={{
      display: "flex", flexDirection: "column", alignItems: "center", gap: 4,
      padding: "8px 12px", border: `1px solid ${color}55`, background: `${color}0d`,
      cursor: onClick ? "pointer" : "default", minWidth: 90, textAlign: "center",
      transition: "background 0.2s",
    }}>
      <span style={{ fontFamily: "var(--mono)", fontSize: 14, color }}>{icon}</span>
      <span style={{ fontFamily: "var(--mono)", fontSize: 10, color: "var(--text)", wordBreak: "break-all" }}>{label}</span>
      {sublabel && <span style={{ fontFamily: "var(--cond)", fontSize: 9, letterSpacing: 2, color }}>{sublabel.toUpperCase()}</span>}
      {note && <span style={{ fontFamily: "var(--sans)", fontSize: 9, color: "var(--muted)", fontStyle: "italic" }}>{note}</span>}
    </div>
  );
}

function Arrow() {
  return (
    <div style={{ display: "flex", alignItems: "center", color: "var(--muted)", padding: "0 4px", fontSize: 16 }}>→</div>
  );
}

// ─── Module 3: Network Graph ──────────────────────────────────────────────────
function Module3({ analysisResult }) {
  const svgRef = useRef(null);
  const [hovered, setHovered] = useState(null);

  if (!analysisResult) {
    return (
      <div style={{ display: "flex", flexDirection: "column", gap: 16 }}>
        <SectionHeader icon="▣" label="MODULE 03" title="Network Graph" />
        <div style={{ padding: "14px 16px", border: "1px dashed var(--border)", fontFamily: "var(--mono)", fontSize: 11, color: "var(--muted)" }}>
          ◈ Analyse an address in Module 01 to generate a network graph.
        </div>
      </div>
    );
  }

  const { peers, address, score } = analysisResult;
  const W = 560, H = 320;
  const cx = W / 2, cy = H / 2 - 10;

  // Root node
  const root = { x: cx, y: cy, id: fmtAddr(address), type: "root", vol: analysisResult.vol };

  // Arrange peers in arc
  const nodes = peers.map((p, i) => {
    const angle = (-Math.PI * 0.75) + (i / (peers.length - 1)) * Math.PI * 1.5;
    const r = 120 + (p.broker ? -20 : p.cashout ? 20 : 0);
    return {
      x: cx + Math.cos(angle) * r,
      y: cy + Math.sin(angle) * r * 0.7,
      id: p.id, type: p.broker ? "broker" : p.mobile ? "mobile" : "cashout",
      vol: p.vol,
    };
  });

  // Cashout cluster: add a few "broker" second-hop nodes
  const brokerNodes = peers.filter(p => p.cashout).slice(0, 2).map((p, i) => {
    const baseNode = nodes.find(n => n.id === p.id);
    return baseNode ? {
      x: baseNode.x + (i === 0 ? 60 : -60),
      y: baseNode.y + 60,
      id: `broker-${i}`, type: "final",
      vol: rnd(10000, 200000),
    } : null;
  }).filter(Boolean);

  const allNodes = [root, ...nodes, ...brokerNodes];

  const typeStyle = {
    root:    { fill: riskColor(score), r: 18, label: "TARGET" },
    broker:  { fill: "#00b0ff", r: 12, label: "BROKER" },
    mobile:  { fill: "#ffb300", r: 10, label: "MOBILE WALLET" },
    cashout: { fill: "#ff6e40", r: 11, label: "CASHOUT" },
    final:   { fill: "var(--danger)", r: 8, label: "INFORMAL BROKER" },
  };

  const edges = [
    ...nodes.map(n => ({ from: root, to: n })),
    ...brokerNodes.map(bn => {
      const cashoutNode = nodes.find(n => n.type === "cashout");
      return cashoutNode ? { from: cashoutNode, to: bn, dashed: true } : null;
    }).filter(Boolean),
  ];

  // Pattern detection
  const pattern = peers.filter(p => p.cashout).length >= 2
    ? "⚠ Multi-wallet cashout via shared broker detected"
    : peers.filter(p => p.mobile).length >= 3
    ? "◈ High mobile wallet dispersion — informal broker pattern"
    : "◈ Standard peer cluster — monitor for convergence";

  return (
    <div style={{ display: "flex", flexDirection: "column", gap: 16 }}>
      <SectionHeader icon="▣" label="MODULE 03" title="Network Graph" />

      <div style={{ padding: "8px 14px", background: "var(--surface)", border: "1px solid var(--border)", fontFamily: "var(--mono)", fontSize: 11, color: "var(--warn)" }}>
        {pattern}
      </div>

      <div style={{ border: "1px solid var(--border)", background: "var(--surface)", position: "relative", overflow: "hidden" }}>
        {/* Grid background */}
        <svg width="100%" height={H} viewBox={`0 0 ${W} ${H}`} ref={svgRef} style={{ display: "block" }}>
          <defs>
            <pattern id="grid" width="30" height="30" patternUnits="userSpaceOnUse">
              <path d="M 30 0 L 0 0 0 30" fill="none" stroke="var(--border)" strokeWidth="0.4" opacity="0.5"/>
            </pattern>
            <filter id="glow">
              <feGaussianBlur stdDeviation="3" result="blur"/>
              <feComposite in="SourceGraphic" in2="blur" operator="over"/>
            </filter>
          </defs>
          <rect width={W} height={H} fill="url(#grid)"/>

          {/* Edges */}
          {edges.map((e, i) => e && (
            <line key={i}
              x1={e.from.x} y1={e.from.y} x2={e.to.x} y2={e.to.y}
              stroke={e.dashed ? "var(--danger)" : "var(--border)"}
              strokeWidth={e.dashed ? 1 : 1.5}
              strokeDasharray={e.dashed ? "4 4" : "none"}
              opacity={0.7}
            />
          ))}

          {/* Nodes */}
          {allNodes.map((n) => {
            const s = typeStyle[n.type] || typeStyle.mobile;
            const isHov = hovered === n.id;
            return (
              <g key={n.id} onMouseEnter={() => setHovered(n.id)} onMouseLeave={() => setHovered(null)}>
                <circle cx={n.x} cy={n.y} r={s.r + 6} fill={s.fill} opacity={0.08} />
                <circle cx={n.x} cy={n.y} r={s.r} fill={s.fill} opacity={isHov ? 1 : 0.85} filter="url(#glow)" />
                <text x={n.x} y={n.y + s.r + 12} textAnchor="middle" fontFamily="var(--mono)" fontSize="8" fill="var(--muted)">
                  {n.id.length > 14 ? n.id.slice(0, 14) + "…" : n.id}
                </text>
                <text x={n.x} y={n.y + s.r + 21} textAnchor="middle" fontFamily="var(--cond)" fontSize="7" fill={s.fill} letterSpacing="1">
                  {s.label}
                </text>
                {isHov && (
                  <text x={n.x} y={n.y + 4} textAnchor="middle" fontFamily="var(--mono)" fontSize="8" fill="#000" fontWeight="bold">
                    {(n.vol / 1000).toFixed(0)}k
                  </text>
                )}
              </g>
            );
          })}
        </svg>
      </div>

      {/* Legend */}
      <div style={{ display: "flex", gap: 16, flexWrap: "wrap" }}>
        {Object.entries(typeStyle).map(([k, v]) => (
          <div key={k} style={{ display: "flex", alignItems: "center", gap: 6 }}>
            <div style={{ width: 10, height: 10, borderRadius: "50%", background: v.fill, boxShadow: `0 0 6px ${v.fill}` }}/>
            <span style={{ fontFamily: "var(--mono)", fontSize: 10, color: "var(--muted)" }}>{v.label}</span>
          </div>
        ))}
      </div>

      {/* Peer table */}
      <div style={{ border: "1px solid var(--border)" }}>
        <div style={{
          display: "grid", gridTemplateColumns: "1fr 80px 100px 100px",
          padding: "6px 12px", background: "#0a1018",
          fontFamily: "var(--cond)", fontSize: 10, letterSpacing: 3, color: "var(--muted)",
          borderBottom: "1px solid var(--border)",
        }}>
          <span>PEER ADDRESS</span><span>ROLE</span><span>VOL (USD)</span><span>FLAG</span>
        </div>
        {peers.map((p, i) => (
          <div key={i} style={{
            display: "grid", gridTemplateColumns: "1fr 80px 100px 100px",
            padding: "7px 12px", borderBottom: "1px solid var(--border)22",
            fontFamily: "var(--mono)", fontSize: 11, alignItems: "center",
          }}>
            <span style={{ color: "var(--text)" }}>{p.id}</span>
            <span style={{ fontFamily: "var(--cond)", fontSize: 10, letterSpacing: 2, color: p.broker ? "#00b0ff" : p.mobile ? "var(--warn)" : "var(--danger)" }}>
              {p.broker ? "BROKER" : p.mobile ? "MOBILE" : "CASHOUT"}
            </span>
            <span style={{ color: "var(--muted)" }}>${p.vol.toLocaleString()}</span>
            <span style={{ fontFamily: "var(--cond)", fontSize: 10, color: p.cashout ? "var(--danger)" : "var(--muted)" }}>
              {p.cashout ? "⚠ CASHOUT" : "MONITOR"}
            </span>
          </div>
        ))}
      </div>
    </div>
  );
}

// ─── Section Header ────────────────────────────────────────────────────────────
function SectionHeader({ icon, label, title }) {
  return (
    <div style={{ display: "flex", alignItems: "center", gap: 12, paddingBottom: 12, borderBottom: "1px solid var(--border)" }}>
      <span style={{ fontFamily: "var(--mono)", fontSize: 18, color: "var(--accent)" }}>{icon}</span>
      <div>
        <div style={{ fontFamily: "var(--mono)", fontSize: 9, letterSpacing: 4, color: "var(--muted)" }}>{label}</div>
        <div style={{ fontFamily: "var(--cond)", fontWeight: 700, fontSize: 17, letterSpacing: 2, color: "#fff" }}>{title}</div>
      </div>
    </div>
  );
}

// ─── Main App ─────────────────────────────────────────────────────────────────
export default function SahelTrace() {
  const [activeModule, setActiveModule] = useState(0);
  const [analysisResult, setAnalysisResult] = useState(null);

  const modules = ["ADDRESS SCORING", "CONTEXT LINKING", "NETWORK GRAPH"];

  return (
    <>
      <style>{STYLE}</style>
      <div style={{ minHeight: "100vh", background: "var(--bg)" }}>
        <TopBar />

        {/* Module tabs */}
        <div style={{ display: "flex", borderBottom: "1px solid var(--border)", padding: "0 24px", gap: 0 }}>
          {modules.map((m, i) => (
            <button key={m} onClick={() => setActiveModule(i)} style={{
              background: "none", border: "none",
              borderBottom: activeModule === i ? "2px solid var(--accent)" : "2px solid transparent",
              color: activeModule === i ? "var(--accent)" : "var(--muted)",
              fontFamily: "var(--cond)", fontWeight: 700, fontSize: 12, letterSpacing: 3,
              padding: "14px 24px", cursor: "pointer", transition: "color 0.2s",
              display: "flex", alignItems: "center", gap: 8,
            }}>
              <span style={{
                width: 18, height: 18, borderRadius: "50%",
                background: activeModule === i ? "var(--accent)" : "var(--border)",
                color: activeModule === i ? "#000" : "var(--muted)",
                display: "flex", alignItems: "center", justifyContent: "center",
                fontFamily: "var(--mono)", fontSize: 9, fontWeight: "bold",
              }}>{i + 1}</span>
              {m}
            </button>
          ))}
          {analysisResult && (
            <div style={{ marginLeft: "auto", display: "flex", alignItems: "center", gap: 8, padding: "0 8px" }}>
              <div style={{ width: 6, height: 6, borderRadius: "50%", background: riskColor(analysisResult.score), animation: "pulse-ring 2s infinite" }}/>
              <span style={{ fontFamily: "var(--mono)", fontSize: 10, color: "var(--muted)" }}>
                {fmtAddr(analysisResult.address)} · RISK <span style={{ color: riskColor(analysisResult.score) }}>{analysisResult.score}</span>
              </span>
            </div>
          )}
        </div>

        {/* Content */}
        <div style={{ maxWidth: 860, margin: "0 auto", padding: "28px 24px" }}>
          {activeModule === 0 && <Module1 onAnalyse={r => { setAnalysisResult(r); }} />}
          {activeModule === 1 && <Module2 analysisResult={analysisResult} />}
          {activeModule === 2 && <Module3 analysisResult={analysisResult} />}
        </div>

        {/* Footer */}
        <div style={{
          borderTop: "1px solid var(--border)", padding: "10px 24px",
          display: "flex", justifyContent: "space-between", alignItems: "center",
          fontFamily: "var(--mono)", fontSize: 10, color: "var(--muted)",
        }}>
          <span>SAHELTRACE · ANALYST TOOL · PROTOTYPE BUILD</span>
          <span>FOR LAW ENFORCEMENT & FINANCIAL INTELLIGENCE USE ONLY</span>
        </div>
      </div>
    </>
  );
}
