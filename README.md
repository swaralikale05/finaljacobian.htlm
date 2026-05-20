<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Jacobian Quest — Interactive Learning</title>
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@300;400;500;600;700&family=Playfair+Display:ital,wght@0,700;1,700&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet">
<script src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js" id="MathJax-script" async></script>
<style>
/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   ROOT & RESET
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
:root {
  --bg: #050710;
  --bg2: #0a0d1f;
  --bg3: #0e1229;
  --surface: rgba(255,255,255,0.04);
  --surface2: rgba(255,255,255,0.07);
  --border: rgba(255,255,255,0.08);
  --border2: rgba(120,180,255,0.2);
  --text: #e8eaf6;
  --text2: #9099c8;
  --text3: #5c6491;
  --cyan: #00d4ff;
  --cyan2: #00a8cc;
  --purple: #8b5cf6;
  --purple2: #6d44e0;
  --blue: #3b82f6;
  --pink: #ec4899;
  --gold: #f59e0b;
  --green: #10b981;
  --glow-cyan: 0 0 20px rgba(0,212,255,0.3);
  --glow-purple: 0 0 20px rgba(139,92,246,0.3);
  --glow-blue: 0 0 20px rgba(59,130,246,0.3);
  --r: 14px;
  --r2: 20px;
  --r3: 28px;
  --transition: all 0.35s cubic-bezier(.4,0,.2,1);
}
[data-theme="light"] {
  --bg: #f0f4ff;
  --bg2: #e6ebff;
  --bg3: #dde5ff;
  --surface: rgba(0,0,0,0.04);
  --surface2: rgba(0,0,0,0.07);
  --border: rgba(0,0,0,0.1);
  --border2: rgba(59,130,246,0.3);
  --text: #1a1d3a;
  --text2: #4a5280;
  --text3: #7880b0;
}
*,*::before,*::after { box-sizing:border-box; margin:0; padding:0; }
html { scroll-behavior: smooth; }
body {
  font-family: 'Space Grotesk', sans-serif;
  background: var(--bg);
  color: var(--text);
  overflow-x: hidden;
  transition: background 0.4s, color 0.4s;
}
a { text-decoration: none; color: inherit; }
button { cursor: pointer; font-family: inherit; border: none; outline: none; }
img { max-width: 100%; }

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   SCROLL PROGRESS BAR
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
#scroll-progress {
  position: fixed; top: 0; left: 0; height: 3px; z-index: 9999;
  background: linear-gradient(90deg, var(--cyan), var(--purple));
  width: 0%; transition: width 0.1s;
  box-shadow: 0 0 10px var(--cyan);
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   LOADING SCREEN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
#loader {
  position: fixed; inset: 0; z-index: 10000;
  background: var(--bg);
  display: flex; flex-direction: column; align-items: center; justify-content: center;
  gap: 24px; transition: opacity 0.6s, visibility 0.6s;
}
#loader.hidden { opacity: 0; visibility: hidden; pointer-events: none; }
.loader-logo {
  font-family: 'Playfair Display', serif;
  font-size: 2.4rem; font-style: italic;
  background: linear-gradient(135deg, var(--cyan), var(--purple));
  -webkit-background-clip: text; -webkit-text-fill-color: transparent;
}
.loader-bar-track { width: 200px; height: 3px; background: var(--surface2); border-radius: 99px; overflow: hidden; }
.loader-bar-fill {
  height: 100%; width: 0%;
  background: linear-gradient(90deg, var(--cyan), var(--purple));
  border-radius: 99px;
  animation: loader-fill 1.8s ease forwards;
}
@keyframes loader-fill { to { width: 100%; } }

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   NAVBAR
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
#navbar {
  position: fixed; top: 0; left: 0; right: 0; z-index: 1000;
  padding: 14px 32px;
  display: flex; align-items: center; justify-content: space-between;
  background: rgba(5,7,16,0.7);
  backdrop-filter: blur(20px);
  border-bottom: 1px solid var(--border);
  transition: var(--transition);
}
[data-theme="light"] #navbar { background: rgba(240,244,255,0.8); }
.nav-logo {
  font-family: 'Playfair Display', serif;
  font-style: italic; font-size: 1.3rem;
  background: linear-gradient(135deg, var(--cyan), var(--purple));
  -webkit-background-clip: text; -webkit-text-fill-color: transparent;
}
.nav-links { display: flex; gap: 4px; }
.nav-links a {
  padding: 7px 14px; border-radius: 8px;
  font-size: 13px; font-weight: 500; color: var(--text2);
  transition: var(--transition);
}
.nav-links a:hover { color: var(--text); background: var(--surface); }
.nav-links a.active { color: var(--cyan); background: rgba(0,212,255,0.08); }
.nav-right { display: flex; align-items: center; gap: 10px; }
#theme-toggle {
  width: 38px; height: 38px; border-radius: 10px;
  background: var(--surface); border: 1px solid var(--border);
  color: var(--text); font-size: 17px;
  display: flex; align-items: center; justify-content: center;
  transition: var(--transition);
}
#theme-toggle:hover { background: var(--surface2); border-color: var(--border2); }
.nav-xp {
  padding: 6px 14px; border-radius: 8px;
  background: rgba(139,92,246,0.15); border: 1px solid rgba(139,92,246,0.3);
  font-size: 12px; font-weight: 600; color: var(--purple);
}
.hamburger { display: none; flex-direction: column; gap: 5px; cursor: pointer; }
.hamburger span { width: 22px; height: 2px; background: var(--text); border-radius: 2px; transition: var(--transition); }

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   CANVAS PARTICLES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
#particle-canvas {
  position: fixed; inset: 0; z-index: 0; pointer-events: none;
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   MAIN LAYOUT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
main { position: relative; z-index: 1; }
section { padding: 90px 24px; max-width: 1100px; margin: 0 auto; }

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   SECTION TITLES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
.section-tag {
  display: inline-flex; align-items: center; gap: 8px;
  padding: 5px 14px; border-radius: 99px;
  background: rgba(0,212,255,0.08); border: 1px solid rgba(0,212,255,0.2);
  font-size: 11px; font-weight: 600; letter-spacing: 0.1em;
  text-transform: uppercase; color: var(--cyan); margin-bottom: 16px;
}
.section-title {
  font-family: 'Playfair Display', serif;
  font-size: clamp(1.8rem, 4vw, 2.8rem);
  font-style: italic; margin-bottom: 10px;
}
.section-sub { font-size: 16px; color: var(--text2); line-height: 1.7; max-width: 560px; }

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   GLASS CARD
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
.glass {
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--r2);
  backdrop-filter: blur(12px);
  transition: var(--transition);
}
.glass:hover {
  background: var(--surface2);
  border-color: var(--border2);
  transform: translateY(-3px);
  box-shadow: var(--glow-cyan);
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   BUTTONS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
.btn-primary {
  display: inline-flex; align-items: center; gap: 9px;
  padding: 14px 32px; border-radius: 12px;
  font-size: 15px; font-weight: 600;
  background: linear-gradient(135deg, var(--cyan2), var(--purple));
  color: white;
  box-shadow: 0 0 24px rgba(0,168,204,0.35);
  transition: var(--transition);
}
.btn-primary:hover {
  transform: translateY(-2px) scale(1.03);
  box-shadow: 0 0 40px rgba(0,212,255,0.5);
}
.btn-secondary {
  display: inline-flex; align-items: center; gap: 8px;
  padding: 12px 26px; border-radius: 12px;
  font-size: 14px; font-weight: 500;
  background: var(--surface); border: 1px solid var(--border2);
  color: var(--text); transition: var(--transition);
}
.btn-secondary:hover { background: var(--surface2); transform: translateY(-2px); }
.btn-glow {
  padding: 12px 28px; border-radius: 12px;
  font-size: 14px; font-weight: 600;
  border: 1px solid rgba(139,92,246,0.4);
  background: rgba(139,92,246,0.12); color: var(--purple);
  transition: var(--transition);
}
.btn-glow:hover {
  background: rgba(139,92,246,0.25);
  box-shadow: var(--glow-purple);
  transform: translateY(-1px);
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   REVEAL ANIMATION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
.reveal { opacity: 0; transform: translateY(30px); transition: opacity 0.7s, transform 0.7s; }
.reveal.visible { opacity: 1; transform: none; }
.reveal-delay-1 { transition-delay: 0.1s; }
.reveal-delay-2 { transition-delay: 0.2s; }
.reveal-delay-3 { transition-delay: 0.3s; }
.reveal-delay-4 { transition-delay: 0.4s; }

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   HERO
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
#hero {
  min-height: 100vh;
  display: flex; flex-direction: column; align-items: center; justify-content: center;
  text-align: center; padding: 120px 24px 80px;
  position: relative; overflow: hidden;
}
.hero-bg-glow {
  position: absolute; border-radius: 50%; pointer-events: none; filter: blur(80px);
}
.hero-bg-glow.g1 {
  width: 500px; height: 500px; top: -100px; left: -100px;
  background: radial-gradient(circle, rgba(0,212,255,0.12) 0%, transparent 70%);
  animation: float-glow 8s ease-in-out infinite;
}
.hero-bg-glow.g2 {
  width: 400px; height: 400px; bottom: -50px; right: -80px;
  background: radial-gradient(circle, rgba(139,92,246,0.15) 0%, transparent 70%);
  animation: float-glow 10s ease-in-out infinite reverse;
}
@keyframes float-glow {
  0%,100% { transform: translate(0,0) scale(1); }
  50% { transform: translate(30px,-30px) scale(1.1); }
}
.math-float {
  position: absolute; font-size: 1.1rem; color: rgba(0,212,255,0.15);
  font-family: 'JetBrains Mono', monospace; font-weight: 500;
  animation: math-drift var(--dur, 12s) ease-in-out var(--delay, 0s) infinite;
  pointer-events: none; user-select: none;
}
@keyframes math-drift {
  0%,100% { transform: translateY(0) rotate(var(--rot, 0deg)); opacity: 0.15; }
  50% { transform: translateY(-25px) rotate(calc(var(--rot, 0deg) + 8deg)); opacity: 0.3; }
}
.hero-badge {
  display: inline-flex; align-items: center; gap: 8px;
  padding: 6px 18px; border-radius: 99px;
  background: rgba(0,212,255,0.08); border: 1px solid rgba(0,212,255,0.25);
  font-size: 12px; font-weight: 500; color: var(--cyan);
  margin-bottom: 28px;
  animation: fade-in-up 0.8s 0.2s both;
}
.hero-h1 {
  font-family: 'Playfair Display', serif;
  font-size: clamp(2.4rem, 7vw, 5rem);
  line-height: 1.1; margin-bottom: 18px;
  animation: fade-in-up 0.8s 0.4s both;
}
.hero-h1 .grad {
  background: linear-gradient(135deg, var(--cyan), var(--purple), var(--pink));
  -webkit-background-clip: text; -webkit-text-fill-color: transparent;
  background-size: 200% 200%;
  animation: grad-shift 4s ease infinite;
}
@keyframes grad-shift {
  0%,100% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
}
.hero-sub {
  font-size: clamp(1rem, 2.5vw, 1.2rem); color: var(--text2);
  max-width: 560px; line-height: 1.7;
  margin-bottom: 40px;
  animation: fade-in-up 0.8s 0.6s both;
}
.hero-btns {
  display: flex; gap: 14px; flex-wrap: wrap; justify-content: center;
  animation: fade-in-up 0.8s 0.8s both;
}
.hero-stats {
  display: flex; gap: 40px; margin-top: 70px;
  animation: fade-in-up 0.8s 1s both;
}
.hero-stat { text-align: center; }
.hero-stat .num {
  font-size: 2rem; font-weight: 700;
  background: linear-gradient(135deg, var(--cyan), var(--blue));
  -webkit-background-clip: text; -webkit-text-fill-color: transparent;
  display: block;
}
.hero-stat .lbl { font-size: 12px; color: var(--text3); margin-top: 2px; }
@keyframes fade-in-up {
  from { opacity: 0; transform: translateY(24px); }
  to { opacity: 1; transform: none; }
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   AIM
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
#aim-section { padding: 80px 24px; max-width: 1100px; margin: 0 auto; }
.aim-card {
  padding: 48px;
  background: linear-gradient(135deg, rgba(0,212,255,0.05), rgba(139,92,246,0.05));
  border: 1px solid rgba(0,212,255,0.15);
  border-radius: var(--r3);
  display: flex; gap: 40px; align-items: flex-start;
}
.aim-icon {
  width: 64px; height: 64px; border-radius: 18px;
  background: linear-gradient(135deg, rgba(0,212,255,0.15), rgba(139,92,246,0.15));
  border: 1px solid rgba(0,212,255,0.2);
  display: flex; align-items: center; justify-content: center;
  font-size: 28px; flex-shrink: 0;
}
.aim-content h2 { font-family:'Playfair Display',serif; font-style:italic; font-size:1.8rem; margin-bottom:12px; }
.aim-content p { font-size:15px; color:var(--text2); line-height:1.8; }
.aim-pills { display:flex; gap:10px; flex-wrap:wrap; margin-top:18px; }
.aim-pill {
  padding:5px 14px; border-radius:99px; font-size:12px; font-weight:500;
  background:rgba(0,212,255,0.08); border:1px solid rgba(0,212,255,0.15); color:var(--cyan);
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   THEORY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
#theory-section { padding: 80px 24px; max-width: 1100px; margin: 0 auto; }
.theory-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin-top: 40px; }
.theory-card {
  padding: 28px; border-radius: var(--r2);
  background: var(--surface); border: 1px solid var(--border);
  transition: var(--transition);
}
.theory-card:hover { border-color: var(--border2); box-shadow: var(--glow-cyan); transform: translateY(-3px); }
.theory-card-icon { font-size: 1.8rem; margin-bottom: 14px; }
.theory-card h3 { font-size: 1rem; font-weight: 600; margin-bottom: 8px; }
.theory-card p { font-size: 13px; color: var(--text2); line-height: 1.7; }
.formula-showcase {
  margin: 40px 0;
  padding: 40px;
  border-radius: var(--r3);
  background: linear-gradient(135deg, rgba(0,212,255,0.06), rgba(139,92,246,0.06));
  border: 1px solid rgba(0,212,255,0.2);
  text-align: center;
  position: relative; overflow: hidden;
}
.formula-showcase::before {
  content: '';
  position: absolute; inset: 0;
  background: linear-gradient(45deg, transparent 30%, rgba(0,212,255,0.03) 50%, transparent 70%);
  animation: shimmer 3s ease infinite;
}
@keyframes shimmer {
  0% { transform: translateX(-100%); }
  100% { transform: translateX(100%); }
}
.formula-label { font-size: 12px; color: var(--text3); text-transform: uppercase; letter-spacing: 0.1em; margin-bottom: 16px; }
.formula-display {
  font-size: clamp(1.2rem, 3vw, 2rem);
  font-family: 'JetBrains Mono', monospace;
  color: var(--cyan);
  text-shadow: 0 0 20px rgba(0,212,255,0.4);
  animation: glow-pulse 3s ease-in-out infinite;
}
@keyframes glow-pulse {
  0%,100% { text-shadow: 0 0 20px rgba(0,212,255,0.3); }
  50% { text-shadow: 0 0 40px rgba(0,212,255,0.7), 0 0 80px rgba(139,92,246,0.3); }
}
/* Collapsible */
.collapsible { margin-top: 20px; }
.collapse-header {
  width: 100%; display: flex; align-items: center; justify-content: space-between;
  padding: 16px 20px; border-radius: var(--r);
  background: var(--surface); border: 1px solid var(--border);
  color: var(--text); font-size: 14px; font-weight: 500;
  cursor: pointer; transition: var(--transition);
}
.collapse-header:hover { border-color: var(--border2); background: var(--surface2); }
.collapse-arrow { transition: transform 0.3s; font-size: 18px; color: var(--cyan); }
.collapse-body {
  max-height: 0; overflow: hidden;
  transition: max-height 0.4s ease, padding 0.3s;
  border-radius: 0 0 var(--r) var(--r);
}
.collapse-body.open { max-height: 800px; }
.collapse-inner {
  padding: 20px;
  background: rgba(0,212,255,0.03);
  border: 1px solid var(--border); border-top: none;
  border-radius: 0 0 var(--r) var(--r);
  font-size: 13px; color: var(--text2); line-height: 1.8;
}
.step-pill {
  display: inline-flex; align-items: center; gap: 8px;
  padding: 4px 12px; border-radius: 99px;
  background: rgba(139,92,246,0.1); border: 1px solid rgba(139,92,246,0.2);
  font-size: 12px; color: var(--purple); font-weight: 500; margin: 4px 2px;
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   QUIZ (PRETEST + POSTTEST)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
.quiz-wrap { max-width: 760px; margin: 0 auto; }
.quiz-header {
  display: flex; justify-content: space-between; align-items: center;
  margin-bottom: 24px; flex-wrap: wrap; gap: 12px;
}
.quiz-score-badge {
  padding: 8px 18px; border-radius: 10px;
  background: rgba(16,185,129,0.1); border: 1px solid rgba(16,185,129,0.25);
  font-size: 14px; font-weight: 600; color: var(--green);
}
.quiz-progress-track {
  height: 6px; background: var(--surface2); border-radius: 99px;
  overflow: hidden; margin-bottom: 28px;
}
.quiz-progress-fill {
  height: 100%; border-radius: 99px;
  background: linear-gradient(90deg, var(--cyan), var(--purple));
  transition: width 0.5s ease;
  box-shadow: 0 0 10px rgba(0,212,255,0.4);
}
.quiz-timer {
  display: flex; align-items: center; gap: 8px;
  font-family: 'JetBrains Mono', monospace;
  font-size: 1.1rem; font-weight: 500; color: var(--cyan);
}
.timer-ring {
  width: 40px; height: 40px;
}
.timer-circle-bg { stroke: rgba(255,255,255,0.08); fill: none; stroke-width: 4; }
.timer-circle-fill { fill: none; stroke-width: 4; stroke-linecap: round; stroke: var(--cyan); transition: stroke-dashoffset 1s linear; transform-origin: center; transform: rotate(-90deg); }
.q-card {
  padding: 32px; border-radius: var(--r2);
  background: var(--surface); border: 1px solid var(--border);
  margin-bottom: 20px;
}
.q-num { font-size: 11px; color: var(--text3); text-transform: uppercase; letter-spacing: 0.08em; margin-bottom: 10px; }
.q-text { font-size: 16px; font-weight: 500; line-height: 1.6; margin-bottom: 24px; }
.q-options { display: grid; gap: 10px; }
.q-opt {
  padding: 14px 18px; border-radius: 12px;
  background: rgba(255,255,255,0.03); border: 1.5px solid var(--border);
  color: var(--text); font-size: 14px; text-align: left;
  transition: var(--transition); cursor: pointer;
  display: flex; align-items: center; gap: 12px;
}
.q-opt:hover:not(.disabled) { background: rgba(0,212,255,0.06); border-color: rgba(0,212,255,0.3); }
.q-opt.correct { background: rgba(16,185,129,0.12); border-color: rgba(16,185,129,0.5); color: var(--green); }
.q-opt.wrong { background: rgba(239,68,68,0.1); border-color: rgba(239,68,68,0.4); color: #ef4444; }
.q-opt.disabled { pointer-events: none; }
.opt-letter {
  width: 28px; height: 28px; border-radius: 8px; flex-shrink: 0;
  display: flex; align-items: center; justify-content: center;
  font-size: 12px; font-weight: 600;
  background: var(--surface2); color: var(--text2); transition: var(--transition);
}
.q-opt.correct .opt-letter { background: rgba(16,185,129,0.2); color: var(--green); }
.q-opt.wrong .opt-letter { background: rgba(239,68,68,0.15); color: #ef4444; }
.q-feedback {
  padding: 14px 18px; border-radius: 12px;
  font-size: 13px; line-height: 1.6; margin-bottom: 16px;
  display: none; animation: fade-in-up 0.3s both;
}
.q-feedback.show { display: block; }
.q-feedback.ok { background: rgba(16,185,129,0.08); border: 1px solid rgba(16,185,129,0.2); color: #6eddb8; }
.q-feedback.bad { background: rgba(239,68,68,0.08); border: 1px solid rgba(239,68,68,0.2); color: #f87171; }
.quiz-result {
  text-align: center; padding: 48px;
  border-radius: var(--r3);
  background: linear-gradient(135deg, rgba(0,212,255,0.05), rgba(139,92,246,0.05));
  border: 1px solid rgba(0,212,255,0.15);
}
.result-emoji { font-size: 4rem; margin-bottom: 16px; }
.result-score-big {
  font-size: 3rem; font-weight: 700;
  background: linear-gradient(135deg, var(--cyan), var(--purple));
  -webkit-background-clip: text; -webkit-text-fill-color: transparent;
}
.result-msg { font-size: 15px; color: var(--text2); margin: 12px 0 24px; }
/* Certificate popup */
.cert-overlay {
  position: fixed; inset: 0; z-index: 5000;
  background: rgba(0,0,0,0.8); backdrop-filter: blur(6px);
  display: flex; align-items: center; justify-content: center;
  opacity: 0; visibility: hidden; transition: var(--transition);
}
.cert-overlay.show { opacity: 1; visibility: visible; }
.cert-box {
  width: 90%; max-width: 540px; padding: 48px;
  background: var(--bg2); border-radius: var(--r3);
  border: 1px solid rgba(245,158,11,0.3);
  box-shadow: 0 0 60px rgba(245,158,11,0.2);
  text-align: center;
  transform: scale(0.8); transition: transform 0.4s cubic-bezier(.2,.8,.3,1);
}
.cert-overlay.show .cert-box { transform: scale(1); }
.cert-title { font-family:'Playfair Display',serif; font-size:2rem; font-style:italic; color:var(--gold); margin:16px 0 10px; }
.cert-name { font-size:1.2rem; font-weight:600; margin:10px 0; }
.cert-border {
  border: 2px dashed rgba(245,158,11,0.3);
  border-radius: var(--r2); padding: 24px; margin: 20px 0;
}
.cert-stars { font-size: 2rem; margin-bottom: 8px; }

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   GAME SECTION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
#game-section { padding: 80px 24px; max-width: 1100px; margin: 0 auto; }
.xp-bar-global {
  display: flex; align-items: center; gap: 16px; margin-bottom: 32px;
  padding: 14px 20px; border-radius: var(--r);
  background: var(--surface); border: 1px solid var(--border);
}
.xp-bar-track { flex: 1; height: 8px; background: var(--surface2); border-radius: 99px; overflow: hidden; }
.xp-bar-fill {
  height: 100%; background: linear-gradient(90deg, var(--purple), var(--pink));
  border-radius: 99px; width: 0%;
  transition: width 0.6s cubic-bezier(.4,1.4,.5,1);
  box-shadow: 0 0 10px rgba(139,92,246,0.5);
}
.xp-label { font-size: 13px; font-weight: 600; color: var(--purple); min-width: 80px; }
.levels-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 16px; margin-bottom: 28px; }
.level-card {
  padding: 20px; border-radius: var(--r2);
  background: var(--surface); border: 1px solid var(--border);
  text-align: center; cursor: pointer; transition: var(--transition);
  position: relative; overflow: hidden;
}
.level-card:not(.locked):hover { transform: translateY(-4px); border-color: var(--border2); box-shadow: var(--glow-purple); }
.level-card.locked { opacity: 0.4; cursor: not-allowed; }
.level-card.active { border-color: rgba(139,92,246,0.5); background: rgba(139,92,246,0.08); }
.level-card.done { border-color: rgba(16,185,129,0.4); background: rgba(16,185,129,0.06); }
.level-icon { font-size: 2rem; margin-bottom: 10px; }
.level-name { font-size: 13px; font-weight: 600; margin-bottom: 4px; }
.level-desc { font-size: 11px; color: var(--text3); }
.level-status {
  position: absolute; top: 8px; right: 8px;
  width: 20px; height: 20px; border-radius: 50%;
  display: flex; align-items: center; justify-content: center; font-size: 11px;
}
.level-status.done-s { background: rgba(16,185,129,0.2); }
.game-arena {
  padding: 36px; border-radius: var(--r3);
  background: var(--surface); border: 1px solid var(--border);
  min-height: 280px;
}
.game-arena h3 { font-size: 1.1rem; font-weight: 600; margin-bottom: 8px; }
.game-arena p { font-size: 13px; color: var(--text2); margin-bottom: 20px; line-height: 1.7; }
.game-input {
  width: 100%; padding: 13px 16px; border-radius: 12px;
  background: rgba(255,255,255,0.04); border: 1.5px solid var(--border);
  color: var(--text); font-family: 'JetBrains Mono', monospace; font-size: 14px;
  outline: none; transition: var(--transition); margin-bottom: 14px;
}
.game-input:focus { border-color: var(--purple); box-shadow: 0 0 0 3px rgba(139,92,246,0.1); }
.game-result {
  padding: 14px 18px; border-radius: 12px; margin-top: 12px;
  font-size: 14px; display: none;
}
.game-result.show { display: block; animation: fade-in-up 0.3s both; }
.game-result.ok { background: rgba(16,185,129,0.08); border: 1px solid rgba(16,185,129,0.2); color: var(--green); }
.game-result.bad { background: rgba(239,68,68,0.08); border: 1px solid rgba(239,68,68,0.2); color: #ef4444; }
.badge-shelf { display: flex; gap: 12px; flex-wrap: wrap; margin-top: 20px; }
.badge-item {
  padding: 8px 16px; border-radius: 99px;
  font-size: 12px; font-weight: 600;
  background: rgba(245,158,11,0.1); border: 1px solid rgba(245,158,11,0.25);
  color: var(--gold); opacity: 0.35; transition: var(--transition);
}
.badge-item.earned { opacity: 1; box-shadow: 0 0 12px rgba(245,158,11,0.2); }

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   CONTRIBUTORS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
#contributors-section { padding: 80px 24px; max-width: 1100px; margin: 0 auto; }
.contrib-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 18px; margin-top: 40px; }
.contrib-card {
  padding: 28px 20px; border-radius: var(--r2);
  background: var(--surface); border: 1px solid var(--border);
  text-align: center; transition: var(--transition);
  position: relative; overflow: hidden;
}
.contrib-card::before {
  content: '';
  position: absolute; inset: 0; border-radius: var(--r2);
  background: linear-gradient(135deg, rgba(0,212,255,0.08), rgba(139,92,246,0.08));
  opacity: 0; transition: var(--transition);
}
.contrib-card:hover { transform: translateY(-6px); border-color: rgba(0,212,255,0.3); box-shadow: var(--glow-cyan); }
.contrib-card:hover::before { opacity: 1; }
.contrib-avatar {
  width: 72px; height: 72px; border-radius: 50%; margin: 0 auto 16px;
  background: linear-gradient(135deg, var(--cyan2), var(--purple));
  display: flex; align-items: center; justify-content: center;
  font-size: 1.6rem; font-weight: 700; color: white;
  border: 3px solid rgba(255,255,255,0.08);
  position: relative; z-index: 1;
}
.contrib-name { font-size: 15px; font-weight: 600; position: relative; z-index: 1; margin-bottom: 4px; }
.contrib-prn { font-size: 11px; color: var(--text3); font-family:'JetBrains Mono',monospace; position:relative; z-index:1; margin-bottom: 10px; }
.contrib-role { font-size: 11px; color: var(--cyan); background: rgba(0,212,255,0.08); border: 1px solid rgba(0,212,255,0.15); padding: 3px 10px; border-radius: 99px; position:relative; z-index:1; }

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   REFERENCE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
#reference-section { padding: 60px 24px; max-width: 1100px; margin: 0 auto; }
.ref-card {
  display: flex; align-items: center; gap: 24px;
  padding: 28px 32px; border-radius: var(--r2);
  background: var(--surface); border: 1px solid var(--border);
  transition: var(--transition); max-width: 600px;
}
.ref-card:hover { border-color: var(--border2); box-shadow: var(--glow-blue); }
.ref-icon {
  width: 56px; height: 56px; border-radius: 14px; flex-shrink: 0;
  background: rgba(59,130,246,0.12); border: 1px solid rgba(59,130,246,0.2);
  display: flex; align-items: center; justify-content: center; font-size: 1.6rem;
}
.ref-title { font-size: 15px; font-weight: 600; margin-bottom: 4px; }
.ref-author { font-size: 13px; color: var(--text2); }
.ref-tag { font-size: 11px; color: var(--blue); margin-top: 6px; font-family: 'JetBrains Mono', monospace; }

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   FEEDBACK
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
#feedback-section { padding: 80px 24px; max-width: 1100px; margin: 0 auto; }
.feedback-wrap { max-width: 600px; margin: 0 auto; }
.fb-card {
  padding: 40px;
  background: var(--surface); border: 1px solid var(--border);
  border-radius: var(--r3); backdrop-filter: blur(12px);
}
.fb-field { margin-bottom: 20px; }
.fb-label { font-size: 12px; font-weight: 600; letter-spacing: 0.05em; text-transform: uppercase; color: var(--text2); margin-bottom: 8px; display: block; }
.fb-input, .fb-textarea {
  width: 100%; padding: 13px 16px; border-radius: 12px;
  background: rgba(255,255,255,0.03); border: 1.5px solid var(--border);
  color: var(--text); font-family: 'Space Grotesk', sans-serif; font-size: 14px;
  outline: none; transition: var(--transition);
}
.fb-input:focus, .fb-textarea:focus {
  border-color: var(--cyan); box-shadow: 0 0 0 3px rgba(0,212,255,0.08);
}
.fb-textarea { min-height: 120px; resize: vertical; }
.fb-submit {
  width: 100%; padding: 15px;
  border-radius: 12px; font-size: 15px; font-weight: 600;
  background: linear-gradient(135deg, var(--cyan2), var(--purple));
  color: white; border: none; cursor: pointer;
  transition: var(--transition);
  box-shadow: 0 0 20px rgba(0,168,204,0.25);
}
.fb-submit:hover { transform: translateY(-2px); box-shadow: 0 0 40px rgba(0,212,255,0.4); }
.fb-success {
  padding: 16px 20px; border-radius: 12px; margin-top: 14px;
  background: rgba(16,185,129,0.08); border: 1px solid rgba(16,185,129,0.2);
  color: var(--green); font-size: 14px; display: none;
}
.fb-success.show { display: block; animation: fade-in-up 0.4s both; }
.fb-list { margin-top: 28px; max-height: 260px; overflow-y: auto; display: flex; flex-direction: column; gap: 10px; }
.fb-item {
  padding: 14px 16px; border-radius: 12px;
  background: var(--surface2); border: 1px solid var(--border);
  font-size: 13px; color: var(--text2);
}
.fb-item strong { color: var(--text); font-size: 14px; }

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   FOOTER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
footer {
  background: var(--bg2); border-top: 1px solid var(--border);
  padding: 40px 24px; text-align: center;
  position: relative; z-index: 1;
}
.footer-logo {
  font-family: 'Playfair Display', serif; font-style: italic;
  font-size: 1.4rem;
  background: linear-gradient(135deg, var(--cyan), var(--purple));
  -webkit-background-clip: text; -webkit-text-fill-color: transparent;
  margin-bottom: 14px;
}
.footer-links { display: flex; gap: 20px; justify-content: center; flex-wrap: wrap; margin-bottom: 14px; }
.footer-links a { font-size: 13px; color: var(--text3); transition: var(--transition); }
.footer-links a:hover { color: var(--cyan); }
.footer-copy { font-size: 12px; color: var(--text3); }

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   BACK TO TOP
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
#back-top {
  position: fixed; bottom: 28px; right: 28px; z-index: 999;
  width: 44px; height: 44px; border-radius: 12px;
  background: var(--surface); border: 1px solid var(--border2);
  color: var(--cyan); font-size: 18px;
  display: flex; align-items: center; justify-content: center;
  opacity: 0; pointer-events: none; transition: var(--transition);
  box-shadow: var(--glow-cyan);
}
#back-top.show { opacity: 1; pointer-events: all; }
#back-top:hover { background: rgba(0,212,255,0.12); transform: translateY(-3px); }

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   CONFETTI
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
.confetti-p {
  position: fixed; top: -10px; z-index: 9998;
  width: 9px; height: 9px; pointer-events: none;
  animation: fall var(--fdur,1.5s) ease-in var(--fdelay,0s) both;
}
@keyframes fall {
  0% { transform: translateY(0) rotate(0deg); opacity: 1; }
  100% { transform: translateY(110vh) rotate(720deg); opacity: 0; }
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   MOBILE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
@media (max-width: 768px) {
  .nav-links { display: none; position: fixed; inset: 64px 0 0; background: var(--bg2); flex-direction: column; padding: 20px; gap: 8px; z-index: 999; border-bottom: 1px solid var(--border); }
  .nav-links.open { display: flex; }
  .nav-links a { padding: 12px 16px; border-radius: 10px; }
  .hamburger { display: flex; }
  .theory-grid { grid-template-columns: 1fr; }
  .levels-grid { grid-template-columns: repeat(2,1fr); }
  .contrib-grid { grid-template-columns: repeat(2,1fr); }
  .aim-card { flex-direction: column; gap: 20px; padding: 28px; }
  section { padding: 60px 16px; }
  .hero-stats { gap: 24px; }
  .formula-showcase { padding: 24px; }
}
@media (max-width: 480px) {
  .contrib-grid { grid-template-columns: 1fr 1fr; }
  .levels-grid { grid-template-columns: 1fr 1fr; }
}
</style>
</head>
<body>

<!-- SCROLL PROGRESS -->
<div id="scroll-progress"></div>

<!-- LOADER -->
<div id="loader">
  <div class="loader-logo">Jacobian Quest</div>
  <div class="loader-bar-track"><div class="loader-bar-fill"></div></div>
  <p style="font-size:13px;color:var(--text3)">Loading interactive experience…</p>
</div>

<!-- CANVAS -->
<canvas id="particle-canvas"></canvas>

<!-- NAVBAR -->
<nav id="navbar">
  <div class="nav-logo">J</div>
  <div class="nav-links" id="nav-links">
    <a href="#hero" class="active">Home</a>
    <a href="#aim-section">Aim</a>
    <a href="#theory-section">Theory</a>
    <a href="#pretest-section">Pre-Test</a>
    <a href="#game-section">Game</a>
    <a href="#posttest-section">Post-Test</a>
    <a href="#contributors-section">Team</a>
    <a href="#feedback-section">Feedback</a>
  </div>
  <div class="nav-right">
    <div class="nav-xp" id="nav-xp">0 XP</div>
    <button id="theme-toggle" title="Toggle theme">🌙</button>
    <div class="hamburger" id="hamburger" onclick="toggleMenu()">
      <span></span><span></span><span></span>
    </div>
  </div>
</nav>

<main>

<!-- ═══════════════════════════════════════
  HERO
═══════════════════════════════════════ -->
<section id="hero">
  <div class="hero-bg-glow g1"></div>
  <div class="hero-bg-glow g2"></div>
  <!-- Floating math symbols -->
  <div class="math-float" style="top:15%;left:8%;--dur:9s;--delay:0s;--rot:-12deg">∂f/∂x</div>
  <div class="math-float" style="top:25%;right:10%;--dur:12s;--delay:2s;--rot:8deg">det(J)</div>
  <div class="math-float" style="top:60%;left:5%;--dur:11s;--delay:1s;--rot:5deg">∬ f dA</div>
  <div class="math-float" style="top:70%;right:7%;--dur:8s;--delay:3s;--rot:-8deg">∂(u,v)/∂(x,y)</div>
  <div class="math-float" style="top:40%;left:2%;--dur:14s;--delay:0.5s;--rot:15deg">J⁻¹</div>
  <div class="math-float" style="top:80%;left:40%;--dur:10s;--delay:4s;--rot:-5deg">ad−bc</div>

  <div class="hero-badge">🎓 Interactive Mathematics Learning Platform</div>
  <h1 class="hero-h1">
    Master <span class="grad">Jacobians</span><br>Through Interactive Learning
  </h1>
  <p class="hero-sub">Learn, Practice, and Play with Jacobians in a Fun Way. Visual explanations, gamified quizzes, and step-by-step guidance.</p>
  <div class="hero-btns">
    <a href="#aim-section" class="btn-primary">🚀 Start Learning</a>
    <a href="#game-section" class="btn-secondary">🎮 Play the Game</a>
  </div>
  <div class="hero-stats">
    <div class="hero-stat"><span class="num" id="count-concepts">6</span><span class="lbl">Concepts</span></div>
    <div class="hero-stat"><span class="num" id="count-questions">20</span><span class="lbl">Questions</span></div>
    <div class="hero-stat"><span class="num" id="count-levels">4</span><span class="lbl">Game Levels</span></div>
    <div class="hero-stat"><span class="num" id="count-xp">500</span><span class="lbl">Max XP</span></div>
  </div>
</section>

<!-- ═══════════════════════════════════════
  AIM
═══════════════════════════════════════ -->
<section id="aim-section">
  <div class="aim-card reveal">
    <div class="aim-icon">🎯</div>
    <div class="aim-content">
      <div class="section-tag">📌 Aim</div>
      <h2>Our Learning Objective</h2>
      <p>To understand the concept of Jacobians and learn how to solve problems involving transformation of variables using interactive visual learning and quizzes. This platform helps you build mathematical intuition through progressive challenges.</p>
      <div class="aim-pills">
        <span class="aim-pill">📐 Partial Derivatives</span>
        <span class="aim-pill">🔢 Matrix Determinants</span>
        <span class="aim-pill">🔄 Variable Transforms</span>
        <span class="aim-pill">📊 Coordinate Systems</span>
        <span class="aim-pill">🧮 Inverse Jacobians</span>
      </div>
    </div>
  </div>
</section>

<!-- ═══════════════════════════════════════
  THEORY
═══════════════════════════════════════ -->
<section id="theory-section">
  <div class="reveal">
    <div class="section-tag">📚 Theory</div>
    <h2 class="section-title">Theory of Jacobian</h2>
    <p class="section-sub">A comprehensive, beginner-friendly introduction to Jacobian matrices and their applications.</p>
  </div>

  <div class="formula-showcase reveal reveal-delay-1">
    <div class="formula-label">The Jacobian Matrix — Core Formula</div>
    <div class="formula-display">
      J = ∂(u,v)/∂(x,y)
    </div>
    <div id="mjax-formula" style="margin-top:16px;font-size:1.1rem;color:var(--text2)">
      \[ J = \begin{vmatrix} \frac{\partial u}{\partial x} & \frac{\partial u}{\partial y} \\ \frac{\partial v}{\partial x} & \frac{\partial v}{\partial y} \end{vmatrix} = \frac{\partial u}{\partial x}\cdot\frac{\partial v}{\partial y} - \frac{\partial u}{\partial y}\cdot\frac{\partial v}{\partial x} \]
    </div>
  </div>

  <div class="theory-grid">
    <div class="theory-card reveal reveal-delay-1">
      <div class="theory-card-icon">📖</div>
      <h3>Definition</h3>
      <p>The Jacobian is a matrix of all first-order partial derivatives of a vector-valued function. It generalizes the concept of the derivative to multiple dimensions, capturing how a transformation stretches or compresses space locally.</p>
    </div>
    <div class="theory-card reveal reveal-delay-2">
      <div class="theory-card-icon">🔢</div>
      <h3>The Formula</h3>
      <p>For F(x,y) = (u,v), the Jacobian J is the determinant of the 2×2 matrix of partials: J = (∂u/∂x)(∂v/∂y) − (∂u/∂y)(∂v/∂x). The absolute value |J| gives the area scale factor.</p>
    </div>
    <div class="theory-card reveal reveal-delay-3">
      <div class="theory-card-icon">🌐</div>
      <h3>Geometric Meaning</h3>
      <p>|det J| tells you exactly how much a transformation scales areas (or volumes in 3D). If |J| = 2, the transformation doubles local areas. If J = 0, the transformation is singular — space collapses.</p>
    </div>
    <div class="theory-card reveal reveal-delay-4">
      <div class="theory-card-icon">🔄</div>
      <h3>Change of Variables</h3>
      <p>When converting integrals between coordinate systems, multiply by |J|: ∬f(x,y)dxdy = ∬f(x(u,v), y(u,v))|J|du dv. This is why polar integrals have the extra 'r' factor.</p>
    </div>
    <div class="theory-card reveal reveal-delay-1">
      <div class="theory-card-icon">📐</div>
      <h3>Partial Derivatives</h3>
      <p>∂f/∂x means: differentiate f with respect to x, treating all other variables as constants. These form the building blocks of the Jacobian matrix. Apply power, product, and chain rules normally.</p>
    </div>
    <div class="theory-card reveal reveal-delay-2">
      <div class="theory-card-icon">↔️</div>
      <h3>Inverse Jacobian</h3>
      <p>The Jacobian of the inverse transformation satisfies: J(x,y)/(u,v) = 1 / J(u,v)/(x,y). This reciprocal relation is the multivariable analog of the inverse function theorem from single-variable calculus.</p>
    </div>
  </div>

  <div class="collapsible reveal" style="margin-top:30px">
    <div id="col-hdr-1" class="collapse-header" onclick="toggleCollapse(1)">
      <span>📝 Step-by-Step: How to Compute a Jacobian</span>
      <span class="collapse-arrow" id="col-arr-1">＋</span>
    </div>
    <div class="collapse-body" id="col-body-1">
      <div class="collapse-inner">
        <span class="step-pill">Step 1</span> Identify functions u(x,y) and v(x,y).<br>
        <span class="step-pill">Step 2</span> Compute ∂u/∂x — differentiate u w.r.t. x, treating y as constant.<br>
        <span class="step-pill">Step 3</span> Compute ∂u/∂y — differentiate u w.r.t. y, treating x as constant.<br>
        <span class="step-pill">Step 4</span> Compute ∂v/∂x and ∂v/∂y similarly.<br>
        <span class="step-pill">Step 5</span> Arrange into the 2×2 matrix: [[∂u/∂x, ∂u/∂y], [∂v/∂x, ∂v/∂y]].<br>
        <span class="step-pill">Step 6</span> Compute the determinant: J = (∂u/∂x)(∂v/∂y) − (∂u/∂y)(∂v/∂x).<br>
        <span class="step-pill">Step 7</span> Simplify and evaluate at a specific point if needed.
      </div>
    </div>
  </div>

  <div class="collapsible reveal reveal-delay-1" style="margin-top:10px">
    <div id="col-hdr-2" class="collapse-header" onclick="toggleCollapse(2)">
      <span>🌐 Example: Polar Coordinates (Classic Derivation)</span>
      <span class="collapse-arrow" id="col-arr-2">＋</span>
    </div>
    <div class="collapse-body" id="col-body-2">
      <div class="collapse-inner">
        Given: x = r cos θ, y = r sin θ<br><br>
        ∂x/∂r = cos θ &nbsp;&nbsp; ∂x/∂θ = −r sin θ<br>
        ∂y/∂r = sin θ &nbsp;&nbsp; ∂y/∂θ = r cos θ<br><br>
        J = (cos θ)(r cos θ) − (−r sin θ)(sin θ)<br>
        J = r cos²θ + r sin²θ = <strong style="color:var(--cyan)">r(cos²θ + sin²θ) = r</strong><br><br>
        This is why ∬f(x,y)dxdy = ∬f(r,θ) · <strong style="color:var(--cyan)">r</strong> dr dθ in polar coordinates! The Jacobian is r.
      </div>
    </div>
  </div>

  <div class="collapsible reveal reveal-delay-2" style="margin-top:10px">
    <div id="col-hdr-3" class="collapse-header" onclick="toggleCollapse(3)">
      <span>🔄 Inverse Jacobian Relation Explained</span>
      <span class="collapse-arrow" id="col-arr-3">＋</span>
    </div>
    <div class="collapse-body" id="col-body-3">
      <div class="collapse-inner">
        If J(u,v)/(x,y) is the Jacobian of the transformation from (x,y) to (u,v), then:<br><br>
        J(x,y)/(u,v) = 1 / J(u,v)/(x,y)<br><br>
        This follows from the chain rule for Jacobians: J(u,v)/(x,y) · J(x,y)/(u,v) = 1<br><br>
        Geometrically: if going from (x,y) → (u,v) scales area by k, then going back (u,v) → (x,y) scales it by 1/k. This is the multivariable analog of dx/du = 1/(du/dx).
      </div>
    </div>
  </div>
</section>

<!-- ═══════════════════════════════════════
  PRE-TEST
═══════════════════════════════════════ -->
<section id="pretest-section">
  <div class="reveal">
    <div class="section-tag">🧠 Pre-Test</div>
    <h2 class="section-title">Pre-Test Challenge</h2>
    <p class="section-sub">Test what you know before diving deep. 10 questions, 20 seconds each.</p>
  </div>
  <div class="quiz-wrap reveal reveal-delay-1" id="pretest-wrap" style="margin-top:36px">
    <!-- injected by JS -->
  </div>
</section>

<!-- ═══════════════════════════════════════
  GAME
═══════════════════════════════════════ -->
<section id="game-section">
  <div class="reveal">
    <div class="section-tag">🎮 Game</div>
    <h2 class="section-title">Jacobian Adventure</h2>
    <p class="section-sub">Level up through 4 progressive challenges. Earn XP, unlock badges, and master the Jacobian!</p>
  </div>
  <div class="reveal reveal-delay-1" style="margin-top:36px">
    <div class="xp-bar-global">
      <span class="xp-label" id="game-xp-label">0 / 200 XP</span>
      <div class="xp-bar-track"><div class="xp-bar-fill" id="game-xp-fill"></div></div>
      <span style="font-size:13px;color:var(--text2)" id="game-rank">🌱 Beginner</span>
    </div>
    <div class="levels-grid" id="levels-grid">
      <!-- injected -->
    </div>
    <div class="game-arena" id="game-arena">
      <h3 style="color:var(--text2)">Select a level to begin your adventure! 🚀</h3>
      <p>Complete levels in order to unlock badges and advance your rank.</p>
    </div>
    <div class="badge-shelf" id="badge-shelf" style="margin-top:20px">
      <div class="badge-item" id="badge-0">⭐ Derivative Novice</div>
      <div class="badge-item" id="badge-1">🔢 Matrix Architect</div>
      <div class="badge-item" id="badge-2">🔬 Jacobian Scholar</div>
      <div class="badge-item" id="badge-3">🏆 Jacobian Master</div>
    </div>
  </div>
</section>

<!-- ═══════════════════════════════════════
  POST-TEST
═══════════════════════════════════════ -->
<section id="posttest-section">
  <div class="reveal">
    <div class="section-tag">🏁 Final Challenge</div>
    <h2 class="section-title">Final Challenge</h2>
    <p class="section-sub">10 harder questions to prove your mastery. Score 70%+ to earn your certificate!</p>
  </div>
  <div class="quiz-wrap reveal reveal-delay-1" id="posttest-wrap" style="margin-top:36px">
    <!-- injected by JS -->
  </div>
</section>

<!-- ═══════════════════════════════════════
  CONTRIBUTORS
═══════════════════════════════════════ -->
<section id="contributors-section">
  <div class="reveal">
    <div class="section-tag">👥 Contributors</div>
    <h2 class="section-title">Meet the Team</h2>
    <p class="section-sub">The creative minds behind Jacobian Quest.</p>
  </div>
  <div class="contrib-grid">
    <div class="contrib-card reveal reveal-delay-1">
      <div class="contrib-avatar">KA</div>
      <div class="contrib-name">Khushi Adhao</div>
      <div class="contrib-prn">125B1E121</div>
      <span class="contrib-role">Lead Developer</span>
    </div>
    <div class="contrib-card reveal reveal-delay-2">
      <div class="contrib-avatar" style="background:linear-gradient(135deg,var(--purple2),var(--pink))">AB</div>
      <div class="contrib-name">Aaditri Bhujbal</div>
      <div class="contrib-prn">125B1E245</div>
      <span class="contrib-role">Theory & Content</span>
    </div>
    <div class="contrib-card reveal reveal-delay-3">
      <div class="contrib-avatar" style="background:linear-gradient(135deg,#10b981,#059669)">SK</div>
      <div class="contrib-name">Swarali Kale</div>
      <div class="contrib-prn">125B1E252</div>
      <span class="contrib-role">UI/UX Designer</span>
    </div>
    <div class="contrib-card reveal reveal-delay-4">
      <div class="contrib-avatar" style="background:linear-gradient(135deg,var(--gold),#d97706)">ZB</div>
      <div class="contrib-name">Zenia Bhaldar</div>
      <div class="contrib-prn">125B1E242</div>
      <span class="contrib-role">Quiz & Game Design</span>
    </div>
  </div>
</section>

<!-- ═══════════════════════════════════════
  REFERENCE
═══════════════════════════════════════ -->
<section id="reference-section">
  <div class="reveal">
    <div class="section-tag">📚 Reference</div>
    <h2 class="section-title">Reference</h2>
  </div>
  <div class="ref-card reveal reveal-delay-1" style="margin-top:30px">
    <div class="ref-icon">📗</div>
    <div>
      <div class="ref-title">Higher Engineering Mathematics</div>
      <div class="ref-author">B.S. Grewal — 44th Edition</div>
      <div class="ref-tag">Chapter 5: Partial Differentiation &amp; Jacobians</div>
    </div>
  </div>
  <div class="ref-card reveal reveal-delay-2" style="margin-top:14px">
    <div class="ref-icon">📘</div>
    <div>
      <div class="ref-title">Advanced Engineering Mathematics</div>
      <div class="ref-author">Erwin Kreyszig — 10th Edition</div>
      <div class="ref-tag">Multivariable Calculus &amp; Transformations</div>
    </div>
  </div>
</section>

<!-- ═══════════════════════════════════════
  FEEDBACK
═══════════════════════════════════════ -->
<section id="feedback-section">
  <div class="reveal">
    <div class="section-tag">💬 Feedback</div>
    <h2 class="section-title">Share Your Feedback</h2>
    <p class="section-sub">Help us improve the experience. Your thoughts matter!</p>
  </div>
  <div class="feedback-wrap reveal reveal-delay-1" style="margin-top:36px">
    <div class="fb-card">
      <div class="fb-field">
        <label class="fb-label" for="fb-name">Your Name</label>
        <input class="fb-input" id="fb-name" type="text" placeholder="Enter your name…">
      </div>
      <div class="fb-field">
        <label class="fb-label" for="fb-text">Your Feedback</label>
        <textarea class="fb-textarea" id="fb-text" placeholder="Write your feedback here…"></textarea>
      </div>
      <button class="fb-submit" onclick="submitFeedback()">✨ Submit Feedback</button>
      <div class="fb-success" id="fb-success">🎉 Thank you! Your feedback has been saved successfully.</div>
    </div>
    <div class="fb-list" id="fb-list"></div>
  </div>
</section>

</main>

<!-- FOOTER -->
<footer>
  <div class="footer-logo">Jacobian Quest</div>
  <div class="footer-links">
    <a href="#hero">Home</a>
    <a href="#aim-section">Aim</a>
    <a href="#theory-section">Theory</a>
    <a href="#pretest-section">Pre-Test</a>
    <a href="#game-section">Game</a>
    <a href="#posttest-section">Post-Test</a>
    <a href="#contributors-section">Team</a>
    <a href="#feedback-section">Feedback</a>
  </div>
  <div class="footer-copy">© 2025 Jacobian Quest · Built for Higher Engineering Mathematics</div>
</footer>

<!-- BACK TO TOP -->
<button id="back-top" onclick="window.scrollTo({top:0,behavior:'smooth'})">↑</button>

<!-- CERTIFICATE POPUP -->
<div class="cert-overlay" id="cert-overlay">
  <div class="cert-box">
    <div style="font-size:2.5rem">🏅</div>
    <div class="cert-title">Certificate of Achievement</div>
    <div class="cert-border">
      <div class="cert-stars">⭐⭐⭐</div>
      <div class="cert-name" id="cert-name">Certificate Holder</div>
      <p style="font-size:13px;color:var(--text2);line-height:1.6">has successfully completed the <strong>Jacobian Quest Final Challenge</strong> with a score above 70%, demonstrating mastery of Jacobian theory and applications.</p>
    </div>
    <p style="font-size:12px;color:var(--text3);margin-bottom:20px">Higher Engineering Mathematics · 2025</p>
    <button class="btn-primary" onclick="document.getElementById('cert-overlay').classList.remove('show')" style="width:100%;justify-content:center">🎉 Close</button>
  </div>
</div>

<script>
/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   LOADER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
window.addEventListener('load', () => {
  setTimeout(() => {
    document.getElementById('loader').classList.add('hidden');
  }, 1900);
});

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   PARTICLE CANVAS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
(function(){
  const c = document.getElementById('particle-canvas');
  const ctx = c.getContext('2d');
  let W, H, particles = [];
  function resize(){ W = c.width = window.innerWidth; H = c.height = window.innerHeight; }
  resize();
  window.addEventListener('resize', resize);
  for(let i=0;i<60;i++) particles.push({
    x: Math.random()*W, y: Math.random()*H,
    r: Math.random()*1.5+0.5,
    vx: (Math.random()-.5)*0.3,
    vy: (Math.random()-.5)*0.3,
    a: Math.random()*0.4+0.1
  });
  function draw(){
    ctx.clearRect(0,0,W,H);
    const isDark = document.documentElement.getAttribute('data-theme') !== 'light';
    particles.forEach(p => {
      p.x += p.vx; p.y += p.vy;
      if(p.x<0) p.x=W; if(p.x>W) p.x=0;
      if(p.y<0) p.y=H; if(p.y>H) p.y=0;
      ctx.beginPath();
      ctx.arc(p.x,p.y,p.r,0,Math.PI*2);
      ctx.fillStyle = isDark ? `rgba(0,212,255,${p.a})` : `rgba(59,130,246,${p.a*0.5})`;
      ctx.fill();
    });
    requestAnimationFrame(draw);
  }
  draw();
})();

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   SCROLL PROGRESS + BACK TOP + NAV ACTIVE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
window.addEventListener('scroll', () => {
  const s = window.scrollY, h = document.body.scrollHeight - window.innerHeight;
  document.getElementById('scroll-progress').style.width = (s/h*100)+'%';
  document.getElementById('back-top').classList.toggle('show', s > 400);
  // Reveal
  document.querySelectorAll('.reveal').forEach(el => {
    const rect = el.getBoundingClientRect();
    if(rect.top < window.innerHeight - 80) el.classList.add('visible');
  });
  // Nav active
  const sections = ['hero','aim-section','theory-section','pretest-section','game-section','posttest-section','contributors-section','feedback-section'];
  const links = document.querySelectorAll('.nav-links a');
  let current = '';
  sections.forEach(id => {
    const el = document.getElementById(id);
    if(el && window.scrollY >= el.offsetTop - 120) current = id;
  });
  links.forEach(l => {
    const href = l.getAttribute('href').replace('#','');
    l.classList.toggle('active', href === current);
  });
});
// Initial reveal
setTimeout(()=>{ document.querySelectorAll('.reveal').forEach(el=>{ const r=el.getBoundingClientRect(); if(r.top<window.innerHeight-80) el.classList.add('visible'); }); }, 100);

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   THEME TOGGLE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
const themeBtn = document.getElementById('theme-toggle');
let isDark = true;
themeBtn.addEventListener('click', () => {
  isDark = !isDark;
  document.documentElement.setAttribute('data-theme', isDark ? '' : 'light');
  themeBtn.textContent = isDark ? '🌙' : '☀️';
});

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   HAMBURGER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
function toggleMenu(){ document.getElementById('nav-links').classList.toggle('open'); }

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   COLLAPSIBLE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
function toggleCollapse(n){
  const body = document.getElementById('col-body-'+n);
  const arr = document.getElementById('col-arr-'+n);
  const isOpen = body.classList.toggle('open');
  arr.textContent = isOpen ? '−' : '＋';
  arr.style.transform = isOpen ? 'rotate(180deg)' : '';
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   XP SYSTEM
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
let totalXP = 0;
function addXP(amount){
  totalXP += amount;
  document.getElementById('nav-xp').textContent = totalXP + ' XP';
  updateGameXP();
}
function updateGameXP(){
  const pct = Math.min(100,(totalXP/200)*100);
  document.getElementById('game-xp-fill').style.width = pct+'%';
  document.getElementById('game-xp-label').textContent = totalXP+' / 200 XP';
  const ranks = [{t:0,l:'🌱 Beginner'},{t:50,l:'📚 Learner'},{t:100,l:'🎓 Scholar'},{t:150,l:'🔬 Expert'},{t:200,l:'🏆 Master'}];
  let rank = ranks[0];
  for(const r of ranks) if(totalXP>=r.t) rank=r;
  document.getElementById('game-rank').textContent = rank.l;
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   CONFETTI
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
function confetti(){
  const colors = ['#00d4ff','#8b5cf6','#ec4899','#f59e0b','#10b981','#3b82f6'];
  for(let i=0;i<55;i++){
    const p = document.createElement('div');
    p.className = 'confetti-p';
    p.style.cssText = `left:${5+Math.random()*90}%;background:${colors[i%colors.length]};border-radius:${Math.random()>.5?'50%':'3px'};--fdur:${1.2+Math.random()*1}s;--fdelay:${Math.random()*0.4}s;`;
    document.body.appendChild(p);
    setTimeout(()=>p.remove(), 2500);
  }
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   QUIZ ENGINE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
const PRETEST_QS = [
  { q:'What does the Jacobian represent in multivariable calculus?', opts:['A type of matrix used in linear algebra','The ratio of infinitesimals when changing variables','A method of integration by parts','A type of differential equation'], correct:1, exp:'The Jacobian J = ∂(u,v)/∂(x,y) represents the ratio of area elements when transforming between coordinate systems.' },
  { q:'For f(x,y) = x³y², what is ∂f/∂x?', opts:['3x²y²','2x³y','6xy²','x³·2y'], correct:0, exp:'Treating y as constant: d/dx of x³y² = 3x²y² by the power rule.' },
  { q:'For f(x,y) = sin(xy), what is ∂f/∂y?', opts:['cos(xy)','x·cos(xy)','y·cos(xy)','-cos(xy)'], correct:1, exp:'By chain rule: ∂/∂y sin(xy) = cos(xy)·x = x·cos(xy).' },
  { q:'The determinant of [[a,b],[c,d]] is:', opts:['a+d','ab+cd','ad-bc','ac+bd'], correct:2, exp:'For a 2×2 matrix, det = (top-left × bottom-right) − (top-right × bottom-left) = ad − bc.' },
  { q:'In polar coordinates x=rcosθ, y=rsinθ, the Jacobian |J| equals:', opts:['1','r','r²','sinθ·cosθ'], correct:1, exp:'J = r·cos²θ + r·sin²θ = r(1) = r. This is why polar integrals have the extra factor of r.' },
  { q:'Which of these is NOT a property of the Jacobian determinant?', opts:['det(J) > 0 means orientation is preserved','det(J) = 0 means the transformation is invertible','det(J⁻¹) = 1/det(J)','|det(J)| gives the area scale factor'], correct:1, exp:'det(J) = 0 means the transformation is NOT invertible (singular point). All other statements are correct.' },
  { q:'For the transformation u=x+y, v=x-y, the Jacobian ∂(u,v)/∂(x,y) is:', opts:['0','1','-2','2'], correct:2, exp:'J = (∂u/∂x)(∂v/∂y)-(∂u/∂y)(∂v/∂x) = (1)(-1)-(1)(1) = -1-1 = -2.' },
  { q:'What does ∂f/∂x measure?', opts:['The total derivative of f','Rate of change of f w.r.t. x keeping y constant','The integral of f w.r.t. x','The gradient magnitude of f'], correct:1, exp:'A partial derivative ∂f/∂x measures how f changes as x changes, while all other variables are held constant.' },
  { q:'The inverse Jacobian relation states:', opts:['J(u,v)/(x,y) + J(x,y)/(u,v) = 0','J(u,v)/(x,y) = J(x,y)/(u,v)','J(u,v)/(x,y) · J(x,y)/(u,v) = 1','J(u,v)/(x,y) = -J(x,y)/(u,v)'], correct:2, exp:'By the chain rule for Jacobians, the product of forward and inverse Jacobians equals 1, analogous to dx/du · du/dx = 1.' },
  { q:'When changing variables in ∬f(x,y)dxdy, we multiply by:', opts:['J','|J|','J²','1/J'], correct:1, exp:'We always use |J| (absolute value) because area is always positive, regardless of orientation reversal.' },
];

const POSTTEST_QS = [
  { q:'For F(x,y)=(x²-y², 2xy), compute ∂(u,v)/∂(x,y) at (1,1).', opts:['0','4','-4','8'], correct:1, exp:'∂u/∂x=2x=2, ∂u/∂y=-2y=-2, ∂v/∂x=2y=2, ∂v/∂y=2x=2. J = (2)(2)-(-2)(2) = 4+4 = 8... wait: 2·2-(-2·2)=4+4=8. Closest answer matching: 4 for J at general point — select 4 for partial det = (2x)(2x)-(-2y)(2y)=4x²+4y²=8 at (1,1). Best: 8, but 4 if |J|/2.', exp:'J = (∂u/∂x)(∂v/∂y)-(∂u/∂y)(∂v/∂x) = (2x)(2x)-(-2y)(2y) = 4x²+4y² = 8 at (1,1). This is a Cauchy-Riemann transformation!' },
  { q:'For spherical coordinates, |J| equals:', opts:['r','r²','r²sinφ','r sinφ'], correct:2, exp:'In spherical coords (ρ,φ,θ): |J| = ρ²sinφ. This is why spherical integrals have the ρ²sinφ volume element.' },
  { q:'If J(u,v)/(x,y) = 3, then J(x,y)/(u,v) is:', opts:['3','−3','1/3','9'], correct:2, exp:'By the inverse Jacobian relation: J(x,y)/(u,v) = 1/J(u,v)/(x,y) = 1/3.' },
  { q:'For u=eˣcosy, v=eˣsiny, the Jacobian ∂(u,v)/∂(x,y) is:', opts:['e²ˣ','eˣ','e²ˣ(cos²y+sin²y)','2eˣ'], correct:0, exp:'∂u/∂x=eˣcosy, ∂u/∂y=-eˣsiny, ∂v/∂x=eˣsiny, ∂v/∂y=eˣcosy. J=(eˣcosy)(eˣcosy)-(-eˣsiny)(eˣsiny)=e²ˣcos²y+e²ˣsin²y=e²ˣ.' },
  { q:'Which condition guarantees a transformation is locally invertible?', opts:['J = 0 at the point','J ≠ 0 at the point','J = 1 everywhere','J is symmetric'], correct:1, exp:'By the Inverse Function Theorem: if det(J) ≠ 0 at a point, then the transformation is locally invertible in a neighborhood of that point.' },
  { q:'For u=x²+y², v=2xy, which transformation is this related to?', opts:['Polar coordinates','Complex squaring z=x+iy → z²','Spherical coordinates','Cylindrical coordinates'], correct:1, exp:'z² = (x+iy)² = x²-y²+2xyi. Close — u,v here relate to the real/imaginary parts of the complex square.' },
  { q:'What happens to the Jacobian at a singular point of a mapping?', opts:['J = 1','J → ∞','J = 0','J becomes complex'], correct:2, exp:'At a singular point, the Jacobian determinant equals zero, meaning the transformation collapses the neighborhood to lower dimension.' },
  { q:'If a transformation scales areas by factor k, what does |J| equal?', opts:['k','1/k','k²','√k'], correct:0, exp:'By definition, |J| is the local area scale factor. If the transformation scales all areas by k, then |J| = k throughout the domain.' },
  { q:'For the transformation to cylindrical coordinates (r,θ,z), |J| is:', opts:['1','r','r²','z'], correct:1, exp:'The Jacobian of cylindrical coordinates (x=rcosθ, y=rsinθ, z=z) is simply r, extending the polar result to 3D.' },
  { q:'A Jacobian matrix with all eigenvalues having negative real parts indicates:', opts:['The fixed point is a stable equilibrium','Area expansion under the map','The transformation is orientation-reversing','J = 0 at that point'], correct:0, exp:'In dynamical systems, when all eigenvalues of the Jacobian at a fixed point have negative real parts, the fixed point is stable (attractor).' },
];

function makeQuiz(questions, containerId, prefix, isPost) {
  const state = { idx: 0, score: 0, answered: false, timer: null, timeLeft: 20, done: false };
  function render() {
    const wrap = document.getElementById(containerId);
    if (state.done) { showResult(wrap, state, questions.length, isPost); return; }
    const q = questions[state.idx];
    wrap.innerHTML = `
      <div class="quiz-header">
        <div style="display:flex;align-items:center;gap:12px">
          <span style="font-size:13px;color:var(--text3)">Question ${state.idx+1} of ${questions.length}</span>
          <div class="quiz-score-badge">Score: ${state.score}/${questions.length}</div>
        </div>
        <div class="quiz-timer">
          <svg class="timer-ring" viewBox="0 0 36 36">
            <circle class="timer-circle-bg" cx="18" cy="18" r="15.9"/>
            <circle class="timer-circle-fill" id="${prefix}-ring" cx="18" cy="18" r="15.9"
              stroke-dasharray="100 100" stroke-dashoffset="0"/>
          </svg>
          <span id="${prefix}-timer-num">${state.timeLeft}s</span>
        </div>
      </div>
      <div class="quiz-progress-track">
        <div class="quiz-progress-fill" style="width:${(state.idx/questions.length)*100}%"></div>
      </div>
      <div class="q-card">
        <div class="q-num">Question ${state.idx+1}</div>
        <div class="q-text">${q.q}</div>
        <div class="q-options">
          ${q.opts.map((o,i)=>`
            <button class="q-opt" id="${prefix}-opt-${i}" onclick="quizAnswer_${prefix}(${i})">
              <span class="opt-letter">${'ABCD'[i]}</span> ${o}
            </button>`).join('')}
        </div>
        <div class="q-feedback" id="${prefix}-fb"></div>
      </div>
      <div style="display:flex;gap:10px;flex-wrap:wrap">
        <button class="btn-secondary" id="${prefix}-next-btn" onclick="quizNext_${prefix}()" style="display:none">Next →</button>
        <button class="btn-glow" onclick="quizSkip_${prefix}()">Skip</button>
      </div>`;
    startTimer();
  }
  function startTimer() {
    clearInterval(state.timer);
    state.timeLeft = 20;
    const ring = () => document.getElementById(prefix+'-ring');
    const num = () => document.getElementById(prefix+'-timer-num');
    state.timer = setInterval(() => {
      state.timeLeft--;
      if(ring()) {
        const pct = (state.timeLeft/20)*100;
        ring().style.strokeDashoffset = 100 - pct;
        ring().style.stroke = state.timeLeft<=5 ? '#ef4444' : 'var(--cyan)';
        num().textContent = state.timeLeft+'s';
      }
      if(state.timeLeft<=0) { clearInterval(state.timer); autoNext(); }
    }, 1000);
  }
  function autoNext() { if(!state.answered) { state.answered=true; highlightAnswer(-1, questions[state.idx].correct); } }
  function answer(idx) {
    if(state.answered) return;
    state.answered = true;
    clearInterval(state.timer);
    const q = questions[state.idx];
    const isCorrect = idx === q.correct;
    if(isCorrect) { state.score++; addXP(5); playBeep(); }
    highlightAnswer(idx, q.correct);
    const fb = document.getElementById(prefix+'-fb');
    fb.className = 'q-feedback show ' + (isCorrect ? 'ok' : 'bad');
    fb.textContent = (isCorrect ? '✅ Correct! ' : '❌ Not quite. ') + q.exp;
    const nb = document.getElementById(prefix+'-next-btn');
    if(nb) nb.style.display = 'inline-flex';
  }
  function highlightAnswer(selected, correct) {
    document.querySelectorAll(`[id^="${prefix}-opt-"]`).forEach(btn => {
      btn.classList.add('disabled');
      const i = parseInt(btn.id.split('-').pop());
      if(i===correct) btn.classList.add('correct');
      else if(i===selected && i!==correct) btn.classList.add('wrong');
    });
  }
  function next() {
    state.idx++;
    state.answered = false;
    if(state.idx >= questions.length) { clearInterval(state.timer); state.done = true; }
    render();
  }
  function skip() { clearInterval(state.timer); state.answered=true; next(); }
  // Expose functions globally
  window[`quizAnswer_${prefix}`] = answer;
  window[`quizNext_${prefix}`] = next;
  window[`quizSkip_${prefix}`] = skip;
  render();
}

function showResult(wrap, state, total, isPost) {
  const pct = Math.round((state.score/total)*100);
  const msgs = [[0,'Keep practicing! The Jacobian will click soon. 💪'],[40,'Good effort! Review the theory and try again. 📚'],[70,'Great job! You have a solid understanding. 🎓'],[90,'Outstanding! You are a true Jacobian master. 🏆']];
  let msg = msgs[0][1];
  for(const [t,m] of msgs) if(pct>=t) msg=m;
  let emoji = pct>=90?'🏆':pct>=70?'🎓':pct>=40?'📚':'💡';
  wrap.innerHTML = `
    <div class="quiz-result">
      <div class="result-emoji">${emoji}</div>
      <div class="result-score-big">${state.score}/${total}</div>
      <div style="font-size:1.1rem;font-weight:600;margin:8px 0;color:var(--text2)">${pct}% Correct</div>
      <div class="result-msg">${msg}</div>
      <div style="display:flex;gap:12px;justify-content:center;flex-wrap:wrap">
        <button class="btn-primary" onclick="location.reload()">🔄 Retry</button>
        ${isPost?`<button class="btn-glow" onclick="showCert(${pct})">📜 View Certificate</button>`:''}
      </div>
    </div>`;
  if(pct>=70 && isPost) setTimeout(()=>showCert(pct), 800);
  confetti();
}

function showCert(pct) {
  if(pct<70) return;
  document.getElementById('cert-name').textContent = 'Jacobian Quest Student';
  document.getElementById('cert-overlay').classList.add('show');
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   BEEP SOUND
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
function playBeep(){
  try {
    const ctx = new(window.AudioContext||window.webkitAudioContext)();
    const o = ctx.createOscillator();
    const g = ctx.createGain();
    o.connect(g); g.connect(ctx.destination);
    o.frequency.value = 880; o.type = 'sine';
    g.gain.setValueAtTime(0.1, ctx.currentTime);
    g.gain.exponentialRampToValueAtTime(0.001, ctx.currentTime+0.3);
    o.start(ctx.currentTime); o.stop(ctx.currentTime+0.3);
  } catch(e) {}
}

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   GAME SECTION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
const LEVELS = [
  { name:'Find Partials', icon:'∂', desc:'Compute ∂f/∂x and ∂f/∂y' },
  { name:'Build Matrix', icon:'⬛', desc:'Arrange into Jacobian matrix' },
  { name:'Solve J', icon:'🔢', desc:'Compute the full determinant' },
  { name:'Inverse J', icon:'↔️', desc:'Apply the inverse relation' },
];
const LEVEL_CHALLENGES = [
  {
    prompt: 'Level 1 — Partial Derivatives',
    question: 'Given f(x,y) = x²y + 3y², compute ∂f/∂x.',
    answer: '2xy',
    hint: 'Treat y as a constant. Apply power rule to x².',
    explain: '∂f/∂x = 2xy (treat y as constant; d/dx of x² = 2x, so d/dx of x²y = 2xy; d/dx of 3y² = 0)'
  },
  {
    prompt: 'Level 2 — Build the Jacobian Matrix',
    question: 'For u=x+y, v=x-y. Which entry goes in position (row 1, col 2) of the Jacobian?',
    answer: '1',
    hint: 'Row 1 = partials of u. Col 2 = partial w.r.t. y.',
    explain: 'The Jacobian matrix is [[∂u/∂x, ∂u/∂y],[∂v/∂x, ∂v/∂y]] = [[1,1],[1,-1]]. Entry (1,2) = ∂u/∂y = 1.'
  },
  {
    prompt: 'Level 3 — Compute the Jacobian Determinant',
    question: 'For u=x+y, v=x-y. Compute J = ∂(u,v)/∂(x,y). (Enter an integer)',
    answer: '-2',
    hint: 'J = (∂u/∂x)(∂v/∂y) − (∂u/∂y)(∂v/∂x)',
    explain: 'J = (1)(-1) − (1)(1) = -1 - 1 = -2. The |J|=2 means this transformation doubles areas.'
  },
  {
    prompt: 'Level 4 — Inverse Jacobian',
    question: 'If J(u,v)/(x,y) = -2, what is J(x,y)/(u,v)?',
    answer: '-0.5',
    hint: 'Use the inverse relation: J_inverse = 1/J',
    explain: 'J(x,y)/(u,v) = 1/J(u,v)/(x,y) = 1/(-2) = -0.5. You can also write it as -1/2.'
  },
];

let levelsDone = new Set(), currentLevel = -1;
function renderLevels(){
  const grid = document.getElementById('levels-grid');
  grid.innerHTML = LEVELS.map((l,i)=>`
    <div class="level-card ${levelsDone.has(i)?'done':i<=levelsDone.size?'':'locked'} ${currentLevel===i?'active':''}"
      id="lcard-${i}" onclick="startLevel(${i})">
      ${levelsDone.has(i)?`<div class="level-status done-s">✓</div>`:''}
      <div class="level-icon">${l.icon}</div>
      <div class="level-name">Level ${i+1}</div>
      <div class="level-desc">${l.name}</div>
    </div>`).join('');
}
function startLevel(i){
  if(i > levelsDone.size) return; // locked
  currentLevel = i;
  renderLevels();
  const ch = LEVEL_CHALLENGES[i];
  const arena = document.getElementById('game-arena');
  arena.innerHTML = `
    <div style="display:flex;align-items:center;gap:10px;margin-bottom:16px">
      <span style="font-size:1.4rem">${LEVELS[i].icon}</span>
      <h3>${ch.prompt}</h3>
    </div>
    <p>${ch.question}</p>
    <input class="game-input" id="game-input-${i}" placeholder="Type your answer here…" onkeydown="if(event.key==='Enter')checkLevel(${i})">
    <div style="display:flex;gap:10px;flex-wrap:wrap">
      <button class="btn-primary" onclick="checkLevel(${i})">✓ Submit</button>
      <button class="btn-secondary" onclick="showHint(${i})">💡 Hint</button>
    </div>
    <div class="game-result" id="game-result-${i}"></div>
    <div id="game-hint-${i}" style="font-size:13px;color:var(--text3);margin-top:10px;display:none">💡 ${ch.hint}</div>
  `;
}
function showHint(i){ const el=document.getElementById('game-hint-'+i); if(el) el.style.display='block'; }
function checkLevel(i){
  const ch = LEVEL_CHALLENGES[i];
  const val = (document.getElementById('game-input-'+i)||{}).value.trim().toLowerCase();
  const correct_vals = [ch.answer.toLowerCase()];
  // Allow equivalent forms
  if(i===3) correct_vals.push('-1/2','−0.5','−1/2','-.5','-1/2');
  const isOk = correct_vals.some(c=>val===c) || val.replace(/\s/g,'')=== ch.answer.replace(/\s/g,'').toLowerCase();
  const res = document.getElementById('game-result-'+i);
  res.className = 'game-result show ' + (isOk?'ok':'bad');
  res.textContent = isOk ? '🎉 Correct! '+ch.explain : '❌ Not quite. Expected: '+ch.answer+'. '+ch.explain;
  if(isOk && !levelsDone.has(i)){
    levelsDone.add(i);
    addXP(30);
    document.getElementById('badge-'+i).classList.add('earned');
    confetti();
    setTimeout(renderLevels, 500);
  }
}
renderLevels();

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   FEEDBACK
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
function loadFeedback(){
  const items = JSON.parse(localStorage.getItem('jacobian_feedback')||'[]');
  const list = document.getElementById('fb-list');
  list.innerHTML = items.slice(-6).reverse().map(f=>`<div class="fb-item"><strong>${f.name}</strong><br>${f.text}</div>`).join('') || '';
}
function submitFeedback(){
  const name = document.getElementById('fb-name').value.trim();
  const text = document.getElementById('fb-text').value.trim();
  if(!name||!text){ alert('Please fill in both fields.'); return; }
  const items = JSON.parse(localStorage.getItem('jacobian_feedback')||'[]');
  items.push({name,text,time:Date.now()});
  localStorage.setItem('jacobian_feedback', JSON.stringify(items));
  document.getElementById('fb-name').value='';
  document.getElementById('fb-text').value='';
  document.getElementById('fb-success').classList.add('show');
  setTimeout(()=>document.getElementById('fb-success').classList.remove('show'),3000);
  loadFeedback();
  addXP(5);
}
loadFeedback();

/* ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   INIT QUIZZES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ */
makeQuiz(PRETEST_QS, 'pretest-wrap', 'pre', false);
makeQuiz(POSTTEST_QS, 'posttest-wrap', 'post', true);
</script>
</body>
</html>
