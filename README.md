import { useState, useEffect, useRef, useCallback } from "react";

const QUESTIONS = [
  { cat: "Teamwork", q: "Tell me about a time you had to work closely with someone whose personality was very different from yours." },
  { cat: "Problem Solving", q: "Describe a situation where you had to solve a problem with limited information or resources." },
  { cat: "Leadership", q: "Give me an example of a time you took the initiative on a project or task without being asked." },
  { cat: "Adaptability", q: "Tell me about a time when you had to adapt to a significant change at work or school." },
  { cat: "Conflict", q: "Describe a disagreement you had with a coworker, classmate, or supervisor. How did you handle it?" },
  { cat: "Failure", q: "Tell me about a time you made a mistake. What did you do to fix it?" },
  { cat: "Communication", q: "Give an example of a time you had to explain something complex to someone unfamiliar with the topic." },
  { cat: "Time Management", q: "Describe a time you had to juggle multiple priorities with tight deadlines." },
  { cat: "Customer Service", q: "Tell me about a time you went above and beyond to help someone." },
  { cat: "Creativity", q: "Describe a situation where you came up with a new or unconventional approach to solve a problem." },
  { cat: "Pressure", q: "Tell me about a time you had to perform well under significant pressure." },
  { cat: "Growth", q: "Give an example of a skill you taught yourself. What motivated you and how did you go about it?" },
  { cat: "Teamwork", q: "Tell me about a time a group project didn't go as planned. What role did you play in getting it back on track?" },
  { cat: "Accountability", q: "Describe a situation where you had to own up to something that went wrong." },
  { cat: "Problem Solving", q: "Tell me about a time you identified a problem before it became urgent. What did you do?" },
  { cat: "Adaptability", q: "Give an example of a time you had to learn something new very quickly to accomplish a goal." },
  { cat: "Leadership", q: "Describe a time you motivated others to complete a task or reach a goal." },
  { cat: "Communication", q: "Tell me about a time you had to deliver difficult news or feedback to someone." },
];

const STEPS = [
  { key: "situation", letter: "S", label: "Situation", color: "#D94F30", prompt: "Set the scene. Where were you? What was happening?", placeholder: "During my junior year, our group project partner dropped the class two days before a major presentation...", tip: "Keep it to 1–2 sentences. Just enough context for the interviewer to follow." },
  { key: "task", letter: "T", label: "Task", color: "#C49A2A", prompt: "What was YOUR specific responsibility or challenge?", placeholder: "I needed to take over their section on market analysis while still completing my own research portion...", tip: "Focus on YOUR role. What was specifically on your shoulders?" },
  { key: "action", letter: "A", label: "Action", color: "#2A7D6C", prompt: "What did YOU do? Walk through the specific steps you took.", placeholder: "I reorganized our timeline, divided the dropped section into smaller tasks, scheduled two extra team meetings...", tip: "Use 'I' not 'we.' Be specific about YOUR individual actions." },
  { key: "result", letter: "R", label: "Result", color: "#1B5E8A", prompt: "What was the outcome? Quantify if possible. What did you learn?", placeholder: "We delivered on time and received an A-. The professor praised the depth of analysis. I learned that proactive communication...", tip: "Numbers make results memorable. Even approximate figures beat vague outcomes." },
];

const ICONS = { "Teamwork": "👥", "Problem Solving": "🧩", "Leadership": "🚀", "Adaptability": "🔄", "Conflict": "⚡", "Failure": "📈", "Communication": "💬", "Time Management": "⏰", "Customer Service": "🤝", "Creativity": "💡", "Pressure": "🎯", "Growth": "🌱", "Accountability": "✊" };

function shuffle(a) { const b = [...a]; for (let i = b.length - 1; i > 0; i--) { const j = Math.floor(Math.random() * (i + 1)); [b[i], b[j]] = [b[j], b[i]]; } return b; }

function wc(s) { return s.trim() ? s.trim().split(/\s+/).length : 0; }

export default function STARCoach() {
  const [queue, setQueue] = useState(() => shuffle(QUESTIONS));
  const [qi, setQi] = useState(0);
  const [step, setStep] = useState(0);
  const [ans, setAns] = useState({ situation: "", task: "", action: "", result: "" });
  const [phase, setPhase] = useState("write"); // write | review | feedback
  const [feedback, setFeedback] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  const [done, setDone] = useState(0);
  const [tipOpen, setTipOpen] = useState(false);
  const [animQ, setAnimQ] = useState(true);
  const [history, setHistory] = useState([]);
  const taRef = useRef(null);

  const curQ = queue[qi % queue.length];

  useEffect(() => { if (taRef.current && phase === "write") taRef.current.focus(); }, [step, qi, phase]);
  useEffect(() => { setAnimQ(true); const t = setTimeout(() => setAnimQ(false), 500); return () => clearTimeout(t); }, [qi]);

  const nextStep = () => { if (step < 3) { setStep(step + 1); setTipOpen(false); } else setPhase("review"); };
  const prevStep = () => { if (step > 0) { setStep(step - 1); setTipOpen(false); } };

  const getFeedback = useCallback(async () => {
    setLoading(true); setError(null);
    const sysPrompt = `You are a supportive career coach helping young adults (ages 18-25) practice behavioral interview responses using the STAR method. Your tone is warm, encouraging, and direct — like a mentor who genuinely wants them to succeed. Never be harsh or discouraging. Always start with something they did well.

Evaluate this STAR response to a behavioral interview question. Provide your coaching feedback in this exact JSON format (no markdown, no backticks, just raw JSON):
{
  "overall_score": <number 1-10>,
  "strengths": ["<strength 1>", "<strength 2>"],
  "situation_feedback": { "score": <1-10>, "comment": "<1-2 sentences>" },
  "task_feedback": { "score": <1-10>, "comment": "<1-2 sentences>" },
  "action_feedback": { "score": <1-10>, "comment": "<1-2 sentences>" },
  "result_feedback": { "score": <1-10>, "comment": "<1-2 sentences>" },
  "improved_example": "<A polished 3-4 sentence version of their response that keeps their story but strengthens the structure>",
  "one_thing_to_try": "<A single, specific, actionable tip they can apply to their very next practice attempt>"
}

If a section is blank or has fewer than 5 words, score it 1 and coach them on what belongs there. Be specific in your feedback — reference their actual words. Keep all comments concise.`;

    const userMsg = `Interview Question: "${curQ.q}"

STAR Response:
Situation: ${ans.situation || "(left blank)"}
Task: ${ans.task || "(left blank)"}
Action: ${ans.action || "(left blank)"}
Result: ${ans.result || "(left blank)"}`;

    try {
      const res = await fetch("https://api.anthropic.com/v1/messages", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          model: "claude-sonnet-4-20250514",
          max_tokens: 1000,
          system: sysPrompt,
          messages: [{ role: "user", content: userMsg }],
        }),
      });
      const data = await res.json();
      const text = data.content?.map(b => b.type === "text" ? b.text : "").join("") || "";
      const clean = text.replace(/```json|```/g, "").trim();
      const parsed = JSON.parse(clean);
      setFeedback(parsed);
      setPhase("feedback");
    } catch (e) {
      console.error(e);
      setError("Couldn't get feedback right now. Review your response using the self-check below, then try again.");
      setPhase("feedback");
    } finally { setLoading(false); }
  }, [ans, curQ]);

  const nextQuestion = () => {
    setHistory(h => [...h, { q: curQ, ans: { ...ans }, feedback }]);
    setDone(d => d + 1);
    const ni = (qi + 1) % queue.length;
    if (ni === 0) setQueue(shuffle(QUESTIONS));
    setQi(ni); setStep(0); setAns({ situation: "", task: "", action: "", result: "" });
    setPhase("write"); setFeedback(null); setError(null); setTipOpen(false);
  };

  const editResponse = () => { setPhase("write"); setStep(0); setFeedback(null); setError(null); };
  const skipQ = () => { const ni = (qi + 1) % queue.length; if (ni === 0) setQueue(shuffle(QUESTIONS)); setQi(ni); setStep(0); setAns({ situation: "", task: "", action: "", result: "" }); setPhase("write"); setFeedback(null); setError(null); setTipOpen(false); };

  const filled = STEPS.filter(s => ans[s.key].trim().length > 0).length;
  const cs = STEPS[step];

  // Score badge color
  const scoreColor = (s) => s >= 8 ? "#2A7D6C" : s >= 5 ? "#C49A2A" : "#D94F30";
  const scoreLabel = (s) => s >= 8 ? "Strong" : s >= 5 ? "Getting There" : "Needs Work";

  // ─── FEEDBACK VIEW ───
  if (phase === "feedback") {
    return (
      <div style={S.page}>
        <div style={S.wrap}>
          <div style={S.hdr}>
            <div style={S.logo}>STAR<span style={S.logoDot}>Coach</span></div>
            {done > 0 && <span style={S.counter}>{done} practiced</span>}
          </div>

          {/* Question reminder */}
          <div style={S.qMini}>
            <span style={S.catPill}>{ICONS[curQ.cat] || "📋"} {curQ.cat}</span>
            <p style={S.qMiniText}>"{curQ.q}"</p>
          </div>

          {feedback ? (
            <div style={S.fbCard}>
              {/* Overall score */}
              <div style={S.fbTop}>
                <div style={{ ...S.scoreBig, borderColor: scoreColor(feedback.overall_score) }}>
                  <span style={{ ...S.scoreNum, color: scoreColor(feedback.overall_score) }}>{feedback.overall_score}</span>
                  <span style={S.scoreOf}>/10</span>
                </div>
                <div style={S.fbTopRight}>
                  <h2 style={{ ...S.fbTitle, color: scoreColor(feedback.overall_score) }}>{scoreLabel(feedback.overall_score)}</h2>
                  <div style={S.strengths}>
                    {feedback.strengths?.map((s, i) => <span key={i} style={S.strengthPill}>✓ {s}</span>)}
                  </div>
                </div>
              </div>

              {/* Per-section feedback */}
              <div style={S.fbSections}>
                {STEPS.map(st => {
                  const fb = feedback[`${st.key}_feedback`];
                  if (!fb) return null;
                  return (
                    <div key={st.key} style={S.fbSect}>
                      <div style={S.fbSectHdr}>
                        <span style={{ ...S.fbLetter, backgroundColor: st.color }}>{st.letter}</span>
                        <span style={S.fbSectLabel}>{st.label}</span>
                        <span style={{ ...S.fbSectScore, color: scoreColor(fb.score) }}>{fb.score}/10</span>
                      </div>
                      <p style={S.fbSectYou}>{ans[st.key].trim() || "(left blank)"}</p>
                      <p style={S.fbSectComment}>{fb.comment}</p>
                    </div>
                  );
                })}
              </div>

              {/* Improved example */}
              {feedback.improved_example && (
                <div style={S.improvedBox}>
                  <h3 style={S.improvedTitle}>💎 Here's how it could sound polished:</h3>
                  <p style={S.improvedText}>{feedback.improved_example}</p>
                </div>
              )}

              {/* One thing to try */}
              {feedback.one_thing_to_try && (
                <div style={S.tipBox}>
                  <h3 style={S.tipBoxTitle}>🎯 One thing to try next time:</h3>
                  <p style={S.tipBoxText}>{feedback.one_thing_to_try}</p>
                </div>
              )}
            </div>
          ) : error ? (
            <div style={S.errorBox}>
              <p style={S.errorText}>{error}</p>
              <div style={S.selfCheck}>
                <h3 style={S.selfCheckTitle}>Self-Check Your Response:</h3>
                {["My Situation is brief (1–2 sentences)", "My Task clarifies MY specific role", "My Action uses 'I' and describes specific steps", "My Result includes a measurable outcome or lesson learned", "The full response takes about 1–2 minutes to say aloud"].map((c, i) => (
                  <label key={i} style={S.checkLabel}><input type="checkbox" style={S.checkBox} /> {c}</label>
                ))}
              </div>
            </div>
          ) : null}

          <div style={S.fbActions}>
            <button onClick={editResponse} style={S.btnSec}>✏️ Edit & Resubmit</button>
            <button onClick={nextQuestion} style={S.btnPri}>Next Question →</button>
          </div>
        </div>
      </div>
    );
  }

  // ─── REVIEW VIEW ───
  if (phase === "review") {
    return (
      <div style={S.page}>
        <div style={S.wrap}>
          <div style={S.hdr}>
            <div style={S.logo}>STAR<span style={S.logoDot}>Coach</span></div>
          </div>
          <div style={S.qMini}>
            <span style={S.catPill}>{ICONS[curQ.cat] || "📋"} {curQ.cat}</span>
            <p style={S.qMiniText}>"{curQ.q}"</p>
          </div>
          <div style={S.revCard}>
            <h2 style={S.revTitle}>Review Your Response</h2>
            <p style={S.revSub}>Read through your full answer below. When you're ready, submit it for coaching feedback.</p>
            {STEPS.map(st => (
              <div key={st.key} style={S.revSect}>
                <div style={S.revSectHdr}>
                  <span style={{ ...S.fbLetter, backgroundColor: st.color }}>{st.letter}</span>
                  <span style={S.fbSectLabel}>{st.label}</span>
                  <span style={S.wc}>{wc(ans[st.key])} words</span>
                </div>
                <p style={S.revText}>{ans[st.key].trim() || <em style={{ color: "#555" }}>No response entered</em>}</p>
              </div>
            ))}
          </div>
          <div style={S.fbActions}>
            <button onClick={() => { setPhase("write"); setStep(0); }} style={S.btnSec}>✏️ Edit</button>
            <button onClick={getFeedback} disabled={loading} style={{ ...S.btnCoach, opacity: loading ? 0.6 : 1 }}>
              {loading ? (
                <span style={S.loadingWrap}>
                  <span style={S.spinner} />
                  Analyzing your response...
                </span>
              ) : "🎤 Get Coaching Feedback"}
            </button>
          </div>
        </div>
      </div>
    );
  }

  // ─── WRITE VIEW ───
  return (
    <div style={S.page}>
      <div style={S.wrap}>
        <div style={S.hdr}>
          <div style={S.logo}>STAR<span style={S.logoDot}>Coach</span></div>
          <div style={S.hdrRight}>
            {done > 0 && <span style={S.counter}>{done} practiced</span>}
          </div>
        </div>

        {/* Question */}
        <div style={{ ...S.qCard, opacity: animQ ? 0 : 1, transform: animQ ? "translateY(-8px)" : "none", transition: "all 0.45s cubic-bezier(0.22,1,0.36,1)" }}>
          <div style={S.qTop}>
            <span style={S.catPill}>{ICONS[curQ.cat] || "📋"} {curQ.cat}</span>
            <button onClick={skipQ} style={S.skipBtn}>Skip ↻</button>
          </div>
          <h2 style={S.qText}>"{curQ.q}"</h2>
        </div>

        {/* Progress steps */}
        <div style={S.prog}>
          {STEPS.map((st, i) => {
            const active = i === step;
            const has = ans[st.key].trim().length > 0;
            return (
              <button key={st.key} onClick={() => { setStep(i); setTipOpen(false); }}
                style={{ ...S.progStep, borderColor: active ? st.color : has ? st.color + "55" : "#252528", backgroundColor: active ? st.color + "12" : "transparent" }}>
                <span style={{ ...S.progLetter, backgroundColor: active || has ? st.color : "#252528", color: active || has ? "#fff" : "#555", transform: active ? "scale(1.12)" : "scale(1)", transition: "all 0.25s" }}>
                  {has && !active ? "✓" : st.letter}
                </span>
                <span style={{ ...S.progLabel, color: active ? st.color : has ? "#bbb" : "#555", fontWeight: active ? 700 : 500 }}>{st.label}</span>
              </button>
            );
          })}
        </div>

        {/* Input card */}
        <div style={{ ...S.inCard, borderColor: cs.color + "40" }}>
          <div style={S.inHdr}>
            <div style={S.inTitleRow}>
              <span style={{ ...S.inLetter, backgroundColor: cs.color }}>{cs.letter}</span>
              <h3 style={{ ...S.inTitle, color: cs.color }}>{cs.label}</h3>
            </div>
            <button onClick={() => setTipOpen(!tipOpen)}
              style={{ ...S.tipBtn, backgroundColor: tipOpen ? cs.color + "18" : "transparent", color: tipOpen ? cs.color : "#777", borderColor: tipOpen ? cs.color + "40" : "#333" }}>
              💡 Tip
            </button>
          </div>
          <p style={S.inPrompt}>{cs.prompt}</p>
          {tipOpen && (
            <div style={{ ...S.tipPopup, backgroundColor: cs.color + "0C", borderColor: cs.color + "30" }}>
              <span>💡</span>
              <p style={S.tipPopText}>{cs.tip}</p>
            </div>
          )}
          <textarea ref={taRef} value={ans[cs.key]}
            onChange={e => setAns({ ...ans, [cs.key]: e.target.value })}
            placeholder={cs.placeholder} style={S.ta} rows={5} />
          <div style={S.inFoot}>
            <span style={S.wcLabel}>
              {wc(ans[cs.key])} words
              {wc(ans[cs.key]) > 0 && wc(ans[cs.key]) < 8 && " — add more detail"}
              {wc(ans[cs.key]) >= 75 && " — consider tightening up"}
            </span>
            <div style={S.navBtns}>
              {step > 0 && <button onClick={prevStep} style={S.btnBack}>← Back</button>}
              <button onClick={nextStep}
                style={{ ...S.btnNext, backgroundColor: cs.color, opacity: ans[cs.key].trim() ? 1 : 0.45 }}>
                {step < 3 ? "Next →" : "Review & Get Feedback ✓"}
              </button>
            </div>
          </div>
        </div>

        <p style={S.footer}>Step {step + 1} of 4 · {filled}/4 filled · Practice makes interviews feel natural</p>
      </div>
    </div>
  );
}

// ─── STYLES ───
const S = {
  page: { minHeight: "100vh", backgroundColor: "#0e0e10", color: "#e8e8e8", fontFamily: "'Outfit', 'DM Sans', system-ui, sans-serif", padding: "20px 14px", boxSizing: "border-box" },
  wrap: { maxWidth: 740, margin: "0 auto" },
  hdr: { display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 22 },
  logo: { fontSize: 22, fontWeight: 800, letterSpacing: "0.15em", color: "#e8e8e8", fontFamily: "'JetBrains Mono', 'DM Mono', monospace" },
  logoDot: { fontWeight: 400, fontSize: 15, color: "#2A7D6C", marginLeft: 4, letterSpacing: "0.05em" },
  hdrRight: { display: "flex", gap: 10, alignItems: "center" },
  counter: { fontSize: 11, fontWeight: 600, color: "#2A7D6C", backgroundColor: "#2A7D6C16", padding: "4px 12px", borderRadius: 16, border: "1px solid #2A7D6C30" },

  qCard: { backgroundColor: "#18181c", border: "1px solid #252528", borderRadius: 14, padding: "20px 24px", marginBottom: 16 },
  qTop: { display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 12 },
  catPill: { fontSize: 11, fontWeight: 600, color: "#aaa", backgroundColor: "#ffffff08", padding: "4px 11px", borderRadius: 16, border: "1px solid #ffffff10" },
  skipBtn: { fontSize: 11, fontWeight: 600, color: "#777", backgroundColor: "transparent", border: "1px solid #333", borderRadius: 16, padding: "4px 13px", cursor: "pointer" },
  qText: { fontSize: 18, fontWeight: 500, lineHeight: 1.5, color: "#f0f0f0", margin: 0, fontStyle: "italic" },

  qMini: { backgroundColor: "#18181c", border: "1px solid #252528", borderRadius: 12, padding: "14px 20px", marginBottom: 16 },
  qMiniText: { fontSize: 15, fontStyle: "italic", color: "#ccc", lineHeight: 1.45, margin: "8px 0 0" },

  prog: { display: "flex", gap: 6, marginBottom: 16 },
  progStep: { flex: 1, display: "flex", flexDirection: "column", alignItems: "center", gap: 5, padding: "10px 6px", borderRadius: 10, border: "1.5px solid #252528", backgroundColor: "transparent", cursor: "pointer", transition: "all 0.25s" },
  progLetter: { width: 30, height: 30, borderRadius: "50%", display: "flex", alignItems: "center", justifyContent: "center", fontSize: 13, fontWeight: 700, fontFamily: "'JetBrains Mono', monospace" },
  progLabel: { fontSize: 10, fontWeight: 500, letterSpacing: "0.02em" },

  inCard: { backgroundColor: "#18181c", border: "1.5px solid #252528", borderRadius: 14, padding: "20px 24px", marginBottom: 14, transition: "border-color 0.3s" },
  inHdr: { display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 8 },
  inTitleRow: { display: "flex", alignItems: "center", gap: 10 },
  inLetter: { width: 34, height: 34, borderRadius: "50%", display: "flex", alignItems: "center", justifyContent: "center", fontSize: 15, fontWeight: 700, color: "#fff", fontFamily: "'JetBrains Mono', monospace" },
  inTitle: { fontSize: 19, fontWeight: 700, margin: 0 },
  tipBtn: { fontSize: 11, fontWeight: 600, padding: "4px 13px", borderRadius: 16, border: "1px solid #333", cursor: "pointer", transition: "all 0.2s" },
  inPrompt: { fontSize: 13, color: "#999", lineHeight: 1.55, margin: "0 0 12px" },
  tipPopup: { display: "flex", alignItems: "flex-start", gap: 8, padding: "10px 14px", borderRadius: 8, border: "1px solid", marginBottom: 12 },
  tipPopText: { fontSize: 12, color: "#ccc", lineHeight: 1.45, margin: 0 },
  ta: { width: "100%", backgroundColor: "#0e0e10", border: "1px solid #252528", borderRadius: 10, padding: "14px", fontSize: 14, lineHeight: 1.65, color: "#e8e8e8", resize: "vertical", outline: "none", fontFamily: "'Outfit', system-ui, sans-serif", boxSizing: "border-box", minHeight: 110 },
  inFoot: { display: "flex", justifyContent: "space-between", alignItems: "center", marginTop: 12, flexWrap: "wrap", gap: 8 },
  wcLabel: { fontSize: 11, color: "#555" },
  navBtns: { display: "flex", gap: 8 },
  btnBack: { fontSize: 12, fontWeight: 600, color: "#999", backgroundColor: "transparent", border: "1px solid #333", borderRadius: 8, padding: "9px 18px", cursor: "pointer" },
  btnNext: { fontSize: 12, fontWeight: 600, color: "#fff", border: "none", borderRadius: 8, padding: "9px 22px", cursor: "pointer", transition: "opacity 0.2s" },

  footer: { textAlign: "center", fontSize: 11, color: "#444", margin: 0 },

  // Review
  revCard: { backgroundColor: "#18181c", border: "1px solid #252528", borderRadius: 14, padding: "24px" },
  revTitle: { fontSize: 20, fontWeight: 700, color: "#f0f0f0", margin: "0 0 4px" },
  revSub: { fontSize: 13, color: "#888", margin: "0 0 20px" },
  revSect: { marginBottom: 18, paddingBottom: 18, borderBottom: "1px solid #1f1f22" },
  revText: { fontSize: 13, color: "#bbb", lineHeight: 1.6, margin: 0, paddingLeft: 36, whiteSpace: "pre-wrap" },
  wc: { fontSize: 10, color: "#555", marginLeft: "auto" },

  // Feedback
  fbCard: { backgroundColor: "#18181c", border: "1px solid #252528", borderRadius: 14, padding: "24px" },
  fbTop: { display: "flex", alignItems: "center", gap: 20, marginBottom: 24, paddingBottom: 20, borderBottom: "1px solid #1f1f22" },
  scoreBig: { width: 72, height: 72, borderRadius: "50%", border: "3px solid", display: "flex", flexDirection: "column", alignItems: "center", justifyContent: "center", flexShrink: 0 },
  scoreNum: { fontSize: 26, fontWeight: 800, fontFamily: "'JetBrains Mono', monospace", lineHeight: 1 },
  scoreOf: { fontSize: 11, color: "#666", fontWeight: 500 },
  fbTopRight: { flex: 1 },
  fbTitle: { fontSize: 20, fontWeight: 700, margin: "0 0 8px" },
  strengths: { display: "flex", flexWrap: "wrap", gap: 6 },
  strengthPill: { fontSize: 11, fontWeight: 500, color: "#2A7D6C", backgroundColor: "#2A7D6C14", padding: "3px 10px", borderRadius: 12, border: "1px solid #2A7D6C28" },

  fbSections: { display: "flex", flexDirection: "column", gap: 16, marginBottom: 24 },
  fbSect: { paddingBottom: 16, borderBottom: "1px solid #1f1f22" },
  fbSectHdr: { display: "flex", alignItems: "center", gap: 8, marginBottom: 6 },
  fbLetter: { width: 26, height: 26, borderRadius: "50%", display: "flex", alignItems: "center", justifyContent: "center", fontSize: 12, fontWeight: 700, color: "#fff", fontFamily: "'JetBrains Mono', monospace", flexShrink: 0 },
  fbSectLabel: { fontSize: 13, fontWeight: 700, color: "#ddd" },
  fbSectScore: { fontSize: 12, fontWeight: 700, marginLeft: "auto", fontFamily: "'JetBrains Mono', monospace" },
  fbSectYou: { fontSize: 12, color: "#777", lineHeight: 1.5, margin: "0 0 6px", paddingLeft: 34, fontStyle: "italic", whiteSpace: "pre-wrap" },
  fbSectComment: { fontSize: 13, color: "#bbb", lineHeight: 1.55, margin: 0, paddingLeft: 34 },

  improvedBox: { backgroundColor: "#0e1a14", border: "1px solid #2A7D6C30", borderRadius: 10, padding: "16px 18px", marginBottom: 16 },
  improvedTitle: { fontSize: 13, fontWeight: 700, color: "#2A7D6C", margin: "0 0 8px" },
  improvedText: { fontSize: 13, color: "#bbb", lineHeight: 1.6, margin: 0, fontStyle: "italic" },

  tipBox: { backgroundColor: "#1a1610", border: "1px solid #C49A2A30", borderRadius: 10, padding: "16px 18px", marginBottom: 8 },
  tipBoxTitle: { fontSize: 13, fontWeight: 700, color: "#C49A2A", margin: "0 0 6px" },
  tipBoxText: { fontSize: 13, color: "#bbb", lineHeight: 1.55, margin: 0 },

  fbActions: { display: "flex", justifyContent: "flex-end", gap: 10, marginTop: 20 },
  btnSec: { fontSize: 12, fontWeight: 600, color: "#999", backgroundColor: "transparent", border: "1px solid #333", borderRadius: 8, padding: "11px 20px", cursor: "pointer" },
  btnPri: { fontSize: 12, fontWeight: 600, color: "#fff", backgroundColor: "#2A7D6C", border: "none", borderRadius: 8, padding: "11px 22px", cursor: "pointer" },
  btnCoach: { fontSize: 13, fontWeight: 700, color: "#fff", backgroundColor: "#D94F30", border: "none", borderRadius: 8, padding: "12px 28px", cursor: "pointer", transition: "opacity 0.2s" },

  loadingWrap: { display: "flex", alignItems: "center", gap: 10 },
  spinner: { display: "inline-block", width: 16, height: 16, border: "2px solid #ffffff44", borderTop: "2px solid #fff", borderRadius: "50%", animation: "spin 0.8s linear infinite" },

  errorBox: { backgroundColor: "#18181c", border: "1px solid #252528", borderRadius: 14, padding: "24px" },
  errorText: { fontSize: 14, color: "#D94F30", margin: "0 0 16px" },
  selfCheck: { backgroundColor: "#0e0e10", borderRadius: 10, padding: "16px" },
  selfCheckTitle: { fontSize: 14, fontWeight: 700, color: "#e8e8e8", margin: "0 0 12px" },
  checkLabel: { display: "flex", alignItems: "center", gap: 8, fontSize: 13, color: "#aaa", marginBottom: 8, cursor: "pointer" },
  checkBox: { accentColor: "#2A7D6C" },
};
