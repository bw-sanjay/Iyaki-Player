<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Iyaki Player — Cinematic Media Experience</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,600;1,300;1,400&family=Syne:wght@400;500;600;700;800&family=JetBrains+Mono:wght@300;400&display=swap" rel="stylesheet">
<style>
  :root {
    --color-surface: #000000;
    --color-surface-2: #0d0d0d;
    --color-surface-3: #1a1a1a;
    --color-accent: #ffffff;
    --color-accent-warm: #cccccc;
    --color-text: #ffffff;
    --color-text-muted: rgba(255,255,255,0.45);
    --color-text-dim: rgba(255,255,255,0.2);
    --color-glass: rgba(0,0,0,0.4);
    --color-glass-border: rgba(255,255,255,0.1);
    --font-display: 'Cormorant Garamond', serif;
    --font-ui: 'Syne', sans-serif;
    --font-mono: 'JetBrains Mono', monospace;
  }

  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  html { scroll-behavior: smooth; }

  body {
    background: var(--color-surface);
    color: var(--color-text);
    font-family: var(--font-ui);
    overflow-x: hidden;
    cursor: none;
  }

  /* CUSTOM CURSOR */
  .cursor {
    position: fixed;
    top: 0; left: 0;
    width: 10px; height: 10px;
    background: var(--color-accent);
    border-radius: 50%;
    pointer-events: none;
    z-index: 9999;
    transition: transform 0.15s ease, background 0.3s;
    transform: translate(-50%, -50%);
  }
  .cursor-ring {
    position: fixed;
    top: 0; left: 0;
    width: 36px; height: 36px;
    border: 1px solid rgba(255,255,255,0.4);
    border-radius: 50%;
    pointer-events: none;
    z-index: 9998;
    transform: translate(-50%, -50%);
    transition: transform 0.35s cubic-bezier(0.25,0.46,0.45,0.94), width 0.3s, height 0.3s, border-color 0.3s;
  }
  body:has(a:hover) .cursor-ring,
  body:has(button:hover) .cursor-ring {
    width: 60px; height: 60px;
    border-color: rgba(255,255,255,0.7);
  }

  /* NOISE OVERLAY */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)' opacity='0.04'/%3E%3C/svg%3E");
    pointer-events: none;
    z-index: 1000;
    opacity: 0.4;
  }

  /* SCROLLBAR */
  ::-webkit-scrollbar { width: 3px; }
  ::-webkit-scrollbar-track { background: var(--color-surface); }
  ::-webkit-scrollbar-thumb { background: rgba(255,255,255,0.3); border-radius: 2px; }

  /* ===== NAV ===== */
  nav {
    position: fixed;
    top: 0; left: 0; right: 0;
    z-index: 100;
    padding: 24px 60px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    backdrop-filter: blur(20px);
    background: rgba(0,0,0,0.7);
    border-bottom: 1px solid var(--color-glass-border);
    transition: padding 0.4s ease;
  }
  nav.scrolled { padding: 16px 60px; }
  .nav-logo {
    font-family: var(--font-display);
    font-size: 1.6rem;
    font-weight: 600;
    letter-spacing: 0.02em;
    color: var(--color-text);
    text-decoration: none;
  }
  .nav-logo span { color: rgba(255,255,255,0.5); }
  .nav-links {
    display: flex;
    gap: 40px;
    list-style: none;
  }
  .nav-links a {
    color: var(--color-text-muted);
    text-decoration: none;
    font-size: 0.78rem;
    font-weight: 500;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    transition: color 0.25s;
    position: relative;
  }
  .nav-links a::after {
    content: '';
    position: absolute;
    bottom: -3px; left: 0; right: 100%;
    height: 1px;
    background: var(--color-accent);
    transition: right 0.3s ease;
  }
  .nav-links a:hover { color: var(--color-text); }
  .nav-links a:hover::after { right: 0; }
  .nav-cta {
    padding: 9px 24px;
    background: var(--color-accent);
    color: var(--color-surface);
    border: none;
    border-radius: 2px;
    font-family: var(--font-ui);
    font-size: 0.75rem;
    font-weight: 700;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    cursor: none;
    transition: background 0.25s, transform 0.2s;
    text-decoration: none;
  }
  .nav-cta:hover { background: #cccccc; transform: translateY(-1px); }

  /* ===== HERO ===== */
  #hero {
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    position: relative;
    overflow: hidden;
    padding: 120px 40px 80px;
    text-align: center;
  }

  /* Cinematic grid lines */
  #hero::before {
    content: '';
    position: absolute;
    inset: 0;
    background-image:
      linear-gradient(rgba(255,255,255,0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(255,255,255,0.03) 1px, transparent 1px);
    background-size: 80px 80px;
    pointer-events: none;
  }

  /* Glow orbs — white/grey only */
  .orb {
    position: absolute;
    border-radius: 50%;
    pointer-events: none;
    filter: blur(80px);
    opacity: 0.08;
    animation: orbFloat 8s ease-in-out infinite;
  }
  .orb-1 { width: 600px; height: 600px; background: #ffffff; top: -200px; left: -200px; animation-delay: 0s; }
  .orb-2 { width: 400px; height: 400px; background: #888888; bottom: -100px; right: -100px; animation-delay: -3s; }
  .orb-3 { width: 300px; height: 300px; background: #aaaaaa; top: 40%; left: 50%; transform: translate(-50%,-50%); animation-delay: -5s; opacity: 0.05; }
  @keyframes orbFloat {
    0%, 100% { transform: translate(0, 0) scale(1); }
    33% { transform: translate(20px, -30px) scale(1.05); }
    66% { transform: translate(-15px, 20px) scale(0.97); }
  }

  .hero-eyebrow {
    font-family: var(--font-mono);
    font-size: 0.7rem;
    letter-spacing: 0.25em;
    color: rgba(255,255,255,0.5);
    text-transform: uppercase;
    margin-bottom: 28px;
    opacity: 0;
    transform: translateY(20px);
    animation: fadeUp 0.8s ease forwards 0.3s;
  }

  .hero-title {
    font-family: var(--font-display);
    font-size: clamp(4rem, 10vw, 9rem);
    font-weight: 300;
    line-height: 0.9;
    letter-spacing: -0.02em;
    margin-bottom: 12px;
    opacity: 0;
    transform: translateY(30px);
    animation: fadeUp 1s ease forwards 0.5s;
  }
  .hero-title em {
    font-style: italic;
    color: var(--color-text);
  }
  .hero-title .line-2 {
    display: block;
    font-weight: 600;
    letter-spacing: 0.05em;
    font-size: 0.55em;
    text-transform: uppercase;
    font-family: var(--font-ui);
    color: var(--color-text-muted);
    margin-top: 10px;
  }

  .hero-sub {
    font-size: 1rem;
    color: var(--color-text-muted);
    max-width: 480px;
    line-height: 1.7;
    margin: 32px auto 52px;
    opacity: 0;
    transform: translateY(20px);
    animation: fadeUp 0.8s ease forwards 0.8s;
    font-weight: 400;
  }

  .hero-actions {
    display: flex;
    gap: 16px;
    justify-content: center;
    flex-wrap: wrap;
    opacity: 0;
    transform: translateY(20px);
    animation: fadeUp 0.8s ease forwards 1s;
  }

  .btn-primary {
    display: inline-flex;
    align-items: center;
    gap: 10px;
    padding: 14px 36px;
    background: var(--color-accent);
    color: var(--color-surface);
    border: none;
    border-radius: 2px;
    font-family: var(--font-ui);
    font-size: 0.8rem;
    font-weight: 700;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    text-decoration: none;
    cursor: none;
    transition: background 0.25s, transform 0.2s, box-shadow 0.3s;
    box-shadow: 0 0 40px rgba(255,255,255,0.15);
  }
  .btn-primary:hover { background: #e0e0e0; transform: translateY(-2px); box-shadow: 0 8px 50px rgba(255,255,255,0.25); }

  .btn-secondary {
    display: inline-flex;
    align-items: center;
    gap: 10px;
    padding: 13px 36px;
    background: transparent;
    color: var(--color-text);
    border: 1px solid var(--color-glass-border);
    border-radius: 2px;
    font-family: var(--font-ui);
    font-size: 0.8rem;
    font-weight: 500;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    text-decoration: none;
    cursor: none;
    transition: border-color 0.25s, transform 0.2s, background 0.25s;
    backdrop-filter: blur(10px);
  }
  .btn-secondary:hover { border-color: rgba(255,255,255,0.5); background: rgba(255,255,255,0.05); transform: translateY(-2px); }

  /* Player mockup */
  .hero-player {
    margin-top: 80px;
    width: 100%;
    max-width: 900px;
    position: relative;
    opacity: 0;
    transform: translateY(40px) scale(0.96);
    animation: fadeUp 1.2s ease forwards 1.2s;
  }
  .player-shell {
    background: var(--color-surface-3);
    border: 1px solid var(--color-glass-border);
    border-radius: 12px;
    overflow: hidden;
    box-shadow: 0 40px 120px rgba(0,0,0,0.8), 0 0 0 1px rgba(255,255,255,0.05);
  }
  .player-titlebar {
    background: rgba(0,0,0,0.5);
    padding: 12px 18px;
    display: flex;
    align-items: center;
    gap: 8px;
    border-bottom: 1px solid var(--color-glass-border);
  }
  .dot { width: 12px; height: 12px; border-radius: 50%; }
  .dot-r { background: #ff5f57; }
  .dot-y { background: #ffbd2e; }
  .dot-g { background: #28c941; }
  .player-filename {
    margin-left: 12px;
    font-family: var(--font-mono);
    font-size: 0.65rem;
    color: var(--color-text-muted);
    letter-spacing: 0.05em;
  }
  .player-screen {
    aspect-ratio: 16/9;
    background: #050507;
    position: relative;
    overflow: hidden;
  }
  /* Cinematic scan lines */
  .player-screen::before {
    content: '';
    position: absolute;
    inset: 0;
    background: repeating-linear-gradient(0deg, transparent, transparent 2px, rgba(0,0,0,0.08) 2px, rgba(0,0,0,0.08) 4px);
    pointer-events: none;
    z-index: 2;
  }
  /* Film grain */
  .player-screen::after {
    content: '';
    position: absolute;
    inset: -50%;
    background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 512 512' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='grain'%3E%3CfeTurbulence type='turbulence' baseFrequency='0.75' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23grain)' opacity='0.08'/%3E%3C/svg%3E");
    pointer-events: none;
    z-index: 3;
    animation: grain 0.5s steps(1) infinite;
  }
  @keyframes grain {
    0% { transform: translate(0,0); }
    20% { transform: translate(-3%, -6%); }
    40% { transform: translate(-6%, 3%); }
    60% { transform: translate(3%, -4%); }
    80% { transform: translate(5%, 6%); }
    100% { transform: translate(-2%, 2%); }
  }
  .player-scene {
    position: absolute;
    inset: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    background: radial-gradient(ellipse at 30% 40%, rgba(40,40,40,1) 0%, #050507 70%);
  }
  .scene-glow {
    position: absolute;
    width: 300px; height: 200px;
    background: radial-gradient(ellipse, rgba(255,255,255,0.06) 0%, transparent 70%);
    top: 20%; left: 15%;
  }
  .scene-title-card {
    text-align: center;
    z-index: 1;
  }
  .scene-title-card h3 {
    font-family: var(--font-display);
    font-size: 2.5rem;
    font-weight: 300;
    letter-spacing: 0.3em;
    text-transform: uppercase;
    color: rgba(255,255,255,0.85);
  }
  .scene-title-card p {
    font-family: var(--font-mono);
    font-size: 0.6rem;
    letter-spacing: 0.2em;
    color: rgba(255,255,255,0.4);
    margin-top: 8px;
    text-transform: uppercase;
  }

  /* Audio bars visualizer */
  .visualizer {
    position: absolute;
    bottom: 60px;
    left: 50%;
    transform: translateX(-50%);
    display: flex;
    gap: 3px;
    align-items: flex-end;
    height: 40px;
    z-index: 4;
  }
  .v-bar {
    width: 3px;
    background: rgba(255,255,255,0.6);
    border-radius: 2px;
    opacity: 0.6;
    animation: barDance var(--dur, 0.8s) ease-in-out infinite alternate;
  }
  @keyframes barDance {
    from { height: 4px; }
    to { height: var(--h, 20px); }
  }

  .player-controls {
    background: rgba(0,0,0,0.7);
    backdrop-filter: blur(20px);
    padding: 14px 20px;
    border-top: 1px solid var(--color-glass-border);
  }
  .progress-track {
    height: 3px;
    background: rgba(255,255,255,0.1);
    border-radius: 2px;
    margin-bottom: 12px;
    position: relative;
    overflow: hidden;
  }
  .progress-fill {
    height: 100%;
    width: 38%;
    background: linear-gradient(90deg, #ffffff, #888888);
    border-radius: 2px;
    position: relative;
  }
  .progress-fill::after {
    content: '';
    position: absolute;
    right: -1px; top: 50%;
    width: 8px; height: 8px;
    background: var(--color-text);
    border-radius: 50%;
    transform: translateY(-50%);
    box-shadow: 0 0 8px rgba(255,255,255,0.8);
  }
  .ctrl-row {
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
  .ctrl-btns {
    display: flex;
    align-items: center;
    gap: 16px;
  }
  .ctrl-icon {
    color: var(--color-text-muted);
    font-size: 0.9rem;
    transition: color 0.2s;
    display: flex;
    align-items: center;
  }
  .ctrl-icon svg { width: 16px; height: 16px; fill: currentColor; }
  .ctrl-icon.active { color: var(--color-text); }
  .ctrl-play {
    width: 34px; height: 34px;
    background: #ffffff;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    color: var(--color-surface);
    font-size: 0.7rem;
    padding-left: 2px;
  }
  .ctrl-play svg { width: 12px; height: 12px; fill: #000; }
  .ctrl-time {
    font-family: var(--font-mono);
    font-size: 0.65rem;
    color: var(--color-text-muted);
    letter-spacing: 0.05em;
  }
  .ctrl-right {
    display: flex;
    align-items: center;
    gap: 16px;
  }
  .vol-bar {
    width: 60px; height: 3px;
    background: rgba(255,255,255,0.1);
    border-radius: 2px;
    position: relative;
  }
  .vol-fill {
    height: 100%;
    width: 70%;
    background: rgba(255,255,255,0.6);
    border-radius: 2px;
  }
  .badge-pill {
    font-family: var(--font-mono);
    font-size: 0.55rem;
    letter-spacing: 0.1em;
    color: rgba(255,255,255,0.6);
    border: 1px solid rgba(255,255,255,0.2);
    padding: 2px 6px;
    border-radius: 999px;
    text-transform: uppercase;
  }

  /* Level meters */
  .level-meters {
    display: flex;
    align-items: center;
    gap: 4px;
    margin-right: 4px;
  }
  .meter-channel {
    display: flex;
    flex-direction: column;
    gap: 1px;
  }
  .meter-label { font-family: var(--font-mono); font-size: 0.5rem; color: var(--color-text-dim); letter-spacing: 0.05em; }
  .meter-bar { width: 3px; height: 18px; background: rgba(255,255,255,0.1); border-radius: 1px; position: relative; overflow: hidden; }
  .meter-fill { position: absolute; bottom: 0; width: 100%; border-radius: 1px; animation: meterPulse 1.2s ease-in-out infinite alternate; }
  .meter-fill-l { height: 75%; background: linear-gradient(to top, #ffffff, #888888); animation-delay: 0s; }
  .meter-fill-r { height: 60%; background: linear-gradient(to top, #ffffff, #aaaaaa); animation-delay: 0.3s; }
  @keyframes meterPulse {
    from { height: 40%; }
    to { height: 85%; }
  }

  @keyframes fadeUp {
    to { opacity: 1; transform: translateY(0) scale(1); }
  }

  /* SCROLL REVEAL BASE */
  .reveal {
    opacity: 0;
    transform: translateY(40px);
    transition: opacity 0.8s ease, transform 0.8s ease;
  }
  .reveal.visible {
    opacity: 1;
    transform: translateY(0);
  }
  .reveal-left {
    opacity: 0;
    transform: translateX(-40px);
    transition: opacity 0.8s ease, transform 0.8s ease;
  }
  .reveal-left.visible { opacity: 1; transform: translateX(0); }
  .reveal-right {
    opacity: 0;
    transform: translateX(40px);
    transition: opacity 0.8s ease, transform 0.8s ease;
  }
  .reveal-right.visible { opacity: 1; transform: translateX(0); }

  /* ===== SECTION BASE ===== */
  section { padding: 120px 60px; position: relative; }
  .section-label {
    font-family: var(--font-mono);
    font-size: 0.65rem;
    letter-spacing: 0.25em;
    color: rgba(255,255,255,0.4);
    text-transform: uppercase;
    margin-bottom: 20px;
  }
  .section-title {
    font-family: var(--font-display);
    font-size: clamp(2.5rem, 5vw, 4.5rem);
    font-weight: 300;
    line-height: 1.05;
    letter-spacing: -0.01em;
  }
  .section-title em { font-style: italic; color: var(--color-accent); }
  .section-body {
    color: var(--color-text-muted);
    font-size: 0.95rem;
    line-height: 1.8;
    max-width: 540px;
    margin-top: 20px;
  }

  /* ===== MARQUEE ===== */
  .marquee-section {
    padding: 24px 0;
    overflow: hidden;
    border-top: 1px solid var(--color-glass-border);
    border-bottom: 1px solid var(--color-glass-border);
    background: var(--color-surface-2);
  }
  .marquee-track {
    display: flex;
    gap: 60px;
    animation: marquee 25s linear infinite;
    white-space: nowrap;
  }
  .marquee-track:hover { animation-play-state: paused; }
  .marquee-item {
    font-family: var(--font-mono);
    font-size: 0.7rem;
    letter-spacing: 0.15em;
    color: var(--color-text-muted);
    text-transform: uppercase;
    flex-shrink: 0;
  }
  .marquee-item span { color: rgba(255,255,255,0.6); margin-right: 60px; }
  @keyframes marquee { from { transform: translateX(0); } to { transform: translateX(-50%); } }

  /* ===== FEATURES GRID ===== */
  #features {
    background: var(--color-surface);
  }
  .features-header {
    text-align: center;
    max-width: 700px;
    margin: 0 auto 80px;
  }
  .features-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 1px;
    background: var(--color-glass-border);
    border: 1px solid var(--color-glass-border);
    border-radius: 12px;
    overflow: hidden;
    max-width: 1100px;
    margin: 0 auto;
  }
  .feat-card {
    background: var(--color-surface-2);
    padding: 40px 36px;
    position: relative;
    overflow: hidden;
    transition: background 0.3s;
  }
  .feat-card::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 2px;
    background: linear-gradient(90deg, transparent, rgba(255,255,255,0.4), transparent);
    opacity: 0;
    transition: opacity 0.3s;
  }
  .feat-card:hover { background: var(--color-surface-3); }
  .feat-card:hover::before { opacity: 1; }
  .feat-icon {
    margin-bottom: 20px;
    display: block;
    color: rgba(255,255,255,0.7);
  }
  .feat-icon svg { width: 28px; height: 28px; stroke: currentColor; fill: none; stroke-width: 1.5; }
  .feat-num {
    font-family: var(--font-mono);
    font-size: 0.55rem;
    color: var(--color-text-dim);
    letter-spacing: 0.1em;
    position: absolute;
    top: 20px; right: 20px;
  }
  .feat-title {
    font-family: var(--font-ui);
    font-size: 0.9rem;
    font-weight: 600;
    letter-spacing: 0.05em;
    margin-bottom: 10px;
    text-transform: uppercase;
  }
  .feat-desc {
    font-size: 0.82rem;
    color: var(--color-text-muted);
    line-height: 1.7;
  }

  /* ===== CINEMATIC SECTION ===== */
  #cinematic {
    background: var(--color-surface-2);
    overflow: hidden;
  }
  .cinematic-inner {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 80px;
    align-items: center;
    max-width: 1200px;
    margin: 0 auto;
  }
  .cinematic-visual {
    position: relative;
    aspect-ratio: 4/3;
    border-radius: 8px;
    overflow: hidden;
    border: 1px solid var(--color-glass-border);
  }
  .cv-bg {
    position: absolute;
    inset: 0;
    background: radial-gradient(ellipse at 60% 50%, rgba(60,60,60,0.3) 0%, rgba(0,0,0,0.95) 65%);
  }
  .cv-content {
    position: absolute;
    inset: 0;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 20px;
    padding: 30px;
  }
  .dim-demo {
    width: 100%;
    border-radius: 6px;
    overflow: hidden;
    border: 1px solid rgba(255,255,255,0.08);
    position: relative;
  }
  .dim-bar {
    height: 4px;
    background: rgba(255,255,255,0.05);
    width: 100%;
  }
  .dim-bar.active {
    background: linear-gradient(90deg, #ffffff, #888888);
    width: 60%;
    animation: pulse-glow 2s ease-in-out infinite;
  }
  @keyframes pulse-glow {
    0%, 100% { box-shadow: 0 0 8px rgba(255,255,255,0.3); }
    50% { box-shadow: 0 0 20px rgba(255,255,255,0.6); }
  }
  .dim-main {
    aspect-ratio: 16/9;
    background: linear-gradient(135deg, #0a0a0a, #1a1a1a);
    display: flex;
    align-items: center;
    justify-content: center;
    position: relative;
  }
  .theater-text {
    font-family: var(--font-display);
    font-size: 1.5rem;
    font-style: italic;
    color: rgba(255,255,255,0.6);
    letter-spacing: 0.2em;
    text-transform: uppercase;
  }
  .sidebar-dim {
    position: absolute;
    left: 0; top: 0; bottom: 0;
    width: 25%;
    background: rgba(0,0,0,0.6);
    backdrop-filter: blur(4px);
    border-right: 1px solid rgba(255,255,255,0.04);
    animation: dimPulse 4s ease-in-out infinite;
  }
  @keyframes dimPulse {
    0%, 100% { opacity: 0.5; }
    50% { opacity: 0.9; }
  }

  /* Visualizer demo */
  .visualizer-demo {
    width: 100%;
    background: rgba(0,0,0,0.5);
    border: 1px solid var(--color-glass-border);
    border-radius: 6px;
    padding: 20px;
    display: flex;
    align-items: center;
    gap: 20px;
  }
  .album-art {
    width: 55px; height: 55px;
    background: linear-gradient(135deg, #333333, #555555);
    border-radius: 4px;
    flex-shrink: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    animation: artRotate 8s linear infinite;
    color: rgba(255,255,255,0.5);
  }
  .album-art svg { width: 22px; height: 22px; stroke: currentColor; fill: none; stroke-width: 1.5; }
  @keyframes artRotate {
    from { transform: rotate(0deg); }
    to { transform: rotate(360deg); }
  }
  .vis-bars {
    display: flex;
    align-items: flex-end;
    gap: 3px;
    height: 40px;
    flex: 1;
  }
  .vis-b {
    flex: 1;
    background: linear-gradient(to top, rgba(255,255,255,0.8), rgba(255,255,255,0.2));
    border-radius: 2px 2px 0 0;
    animation: visDance var(--vd, 0.6s) ease-in-out infinite alternate;
  }
  @keyframes visDance {
    from { height: 20%; }
    to { height: var(--vh, 80%); }
  }

  /* ===== SHORTCUTS ===== */
  #shortcuts {
    background: var(--color-surface);
  }
  .shortcuts-inner {
    max-width: 1100px;
    margin: 0 auto;
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 80px;
    align-items: start;
  }
  .shortcut-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 1px;
    background: var(--color-glass-border);
    border: 1px solid var(--color-glass-border);
    border-radius: 8px;
    overflow: hidden;
  }
  .sc-item {
    background: var(--color-surface-2);
    padding: 16px 20px;
    display: flex;
    align-items: center;
    gap: 14px;
    transition: background 0.2s;
  }
  .sc-item:hover { background: var(--color-surface-3); }
  .kbd {
    font-family: var(--font-mono);
    font-size: 0.65rem;
    background: rgba(255,255,255,0.06);
    border: 1px solid rgba(255,255,255,0.15);
    border-bottom-width: 2px;
    padding: 4px 9px;
    border-radius: 4px;
    color: rgba(255,255,255,0.7);
    letter-spacing: 0.05em;
    min-width: 40px;
    text-align: center;
    flex-shrink: 0;
  }
  .sc-label {
    font-size: 0.78rem;
    color: var(--color-text-muted);
    font-weight: 400;
  }

  /* ===== FORMAT SUPPORT ===== */
  #formats {
    background: var(--color-surface-2);
    text-align: center;
  }
  .format-cloud {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    justify-content: center;
    margin-top: 50px;
    max-width: 800px;
    margin-left: auto;
    margin-right: auto;
  }
  .fmt-tag {
    font-family: var(--font-mono);
    font-size: 0.68rem;
    letter-spacing: 0.1em;
    padding: 6px 14px;
    border: 1px solid var(--color-glass-border);
    border-radius: 2px;
    color: var(--color-text-muted);
    text-transform: uppercase;
    transition: border-color 0.2s, color 0.2s, background 0.2s;
    cursor: default;
  }
  .fmt-tag:hover { border-color: rgba(255,255,255,0.5); color: #ffffff; background: rgba(255,255,255,0.05); }
  .fmt-tag.video { color: rgba(220,60,60,0.9); border-color: rgba(220,60,60,0.4); }
  .fmt-tag.audio { color: rgba(60,200,200,0.9); border-color: rgba(60,200,200,0.4); }

  /* ===== DOWNLOAD ===== */
  #download {
    background: var(--color-surface);
    text-align: center;
    position: relative;
    overflow: hidden;
  }
  #download::before {
    content: '';
    position: absolute;
    top: 50%; left: 50%;
    transform: translate(-50%, -50%);
    width: 800px; height: 800px;
    background: radial-gradient(ellipse, rgba(255,255,255,0.03) 0%, transparent 70%);
    pointer-events: none;
  }
  .dl-inner {
    max-width: 700px;
    margin: 0 auto;
    position: relative;
    z-index: 1;
  }
  .dl-version {
    font-family: var(--font-mono);
    font-size: 0.65rem;
    color: var(--color-text-dim);
    letter-spacing: 0.15em;
    margin-top: 16px;
  }
  .dl-cards {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 16px;
    margin-top: 60px;
    text-align: left;
  }
  .dl-card {
    background: var(--color-surface-2);
    border: 1px solid var(--color-glass-border);
    border-radius: 8px;
    padding: 28px 24px;
    position: relative;
    overflow: hidden;
    transition: border-color 0.3s, transform 0.2s;
    cursor: none;
    text-decoration: none;
    color: var(--color-text);
    display: block;
  }
  .dl-card::after {
    content: '';
    position: absolute;
    inset: 0;
    background: linear-gradient(135deg, rgba(255,255,255,0.03), transparent);
    opacity: 0;
    transition: opacity 0.3s;
  }
  .dl-card:hover { border-color: rgba(255,255,255,0.3); transform: translateY(-3px); }
  .dl-card:hover::after { opacity: 1; }
  .dl-card.featured { border-color: rgba(255,255,255,0.3); }
  .dl-card.featured::before {
    content: 'CURRENT';
    position: absolute;
    top: 12px; right: 12px;
    font-family: var(--font-mono);
    font-size: 0.5rem;
    letter-spacing: 0.1em;
    color: rgba(255,255,255,0.6);
    border: 1px solid rgba(255,255,255,0.2);
    padding: 2px 6px;
    border-radius: 999px;
  }
  .dl-os-icon { margin-bottom: 16px; display: block; color: rgba(255,255,255,0.7); }
  .dl-os-icon svg { width: 32px; height: 32px; fill: currentColor; }
  .dl-os-name { font-weight: 600; font-size: 0.9rem; margin-bottom: 4px; }
  .dl-os-arch { font-family: var(--font-mono); font-size: 0.6rem; color: var(--color-text-muted); letter-spacing: 0.1em; }
  .dl-size { font-family: var(--font-mono); font-size: 0.6rem; color: var(--color-text-dim); margin-top: 16px; }
  .dl-btn {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    margin-top: 16px;
    padding: 8px 18px;
    background: rgba(255,255,255,0.06);
    border: 1px solid rgba(255,255,255,0.15);
    color: rgba(255,255,255,0.7);
    border-radius: 2px;
    font-family: var(--font-ui);
    font-size: 0.72rem;
    font-weight: 600;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    transition: background 0.25s;
  }
  .dl-card:hover .dl-btn { background: rgba(255,255,255,0.12); }

  /* ===== FOOTER ===== */
  footer {
    padding: 60px;
    border-top: 1px solid var(--color-glass-border);
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-wrap: wrap;
    gap: 24px;
  }
  .footer-logo {
    font-family: var(--font-display);
    font-size: 1.4rem;
    font-weight: 600;
  }
  .footer-logo span { color: rgba(255,255,255,0.4); }
  .footer-copy {
    font-family: var(--font-mono);
    font-size: 0.6rem;
    color: var(--color-text-dim);
    letter-spacing: 0.1em;
  }
  .footer-links {
    display: flex;
    gap: 32px;
    list-style: none;
  }
  .footer-links a {
    font-size: 0.75rem;
    color: var(--color-text-muted);
    text-decoration: none;
    letter-spacing: 0.05em;
    transition: color 0.2s;
  }
  .footer-links a:hover { color: #ffffff; }

  /* ===== OCR DEMO ===== */
  .ocr-demo {
    background: var(--color-surface);
    border: 1px solid var(--color-glass-border);
    border-radius: 6px;
    padding: 20px;
    position: relative;
  }
  .ocr-frame {
    aspect-ratio: 16/9;
    background: linear-gradient(135deg, #0a0a0a, #1a1a1a);
    border-radius: 4px;
    display: flex;
    align-items: center;
    justify-content: center;
    position: relative;
    overflow: hidden;
    margin-bottom: 16px;
  }
  .ocr-text-block {
    text-align: center;
    padding: 16px;
  }
  .ocr-text-block p {
    font-family: var(--font-display);
    font-size: 1.2rem;
    color: rgba(255,255,255,0.6);
    line-height: 1.5;
  }
  .ocr-scan-line {
    position: absolute;
    left: 0; right: 0;
    height: 2px;
    background: linear-gradient(90deg, transparent, rgba(255,255,255,0.7), transparent);
    animation: scanMove 3s ease-in-out infinite;
    opacity: 0.8;
  }
  @keyframes scanMove {
    0% { top: 0; opacity: 0; }
    10% { opacity: 1; }
    90% { opacity: 1; }
    100% { top: 100%; opacity: 0; }
  }
  .ocr-result {
    display: flex;
    align-items: center;
    gap: 10px;
  }
  .ocr-badge {
    font-family: var(--font-mono);
    font-size: 0.6rem;
    letter-spacing: 0.1em;
    color: rgba(255,255,255,0.7);
    border: 1px solid rgba(255,255,255,0.25);
    padding: 3px 8px;
    border-radius: 999px;
    text-transform: uppercase;
    animation: blink 1.5s ease-in-out infinite;
  }
  @keyframes blink { 50% { opacity: 0.4; } }
  .ocr-text-out {
    font-family: var(--font-mono);
    font-size: 0.7rem;
    color: var(--color-text-muted);
  }


  /* ===== ABOUT / DEVELOPER ===== */
  #about {
    background: var(--color-surface-2);
    position: relative;
    overflow: hidden;
  }
  #about::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 1px;
    background: linear-gradient(90deg, transparent, rgba(220,60,60,0.5), transparent);
  }
  .about-inner {
    max-width: 1100px;
    margin: 0 auto;
    display: grid;
    grid-template-columns: 1fr 1.4fr;
    gap: 80px;
    align-items: center;
  }
  .about-avatar-wrap {
    position: relative;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 20px;
  }
  .about-avatar {
    width: 180px;
    height: 180px;
    border-radius: 50%;
    background: linear-gradient(135deg, #1a1a1a, #2a2a2a);
    border: 2px solid rgba(220,60,60,0.4);
    display: flex;
    align-items: center;
    justify-content: center;
    position: relative;
    overflow: hidden;
    box-shadow: 0 0 60px rgba(220,60,60,0.15), 0 20px 60px rgba(0,0,0,0.6);
  }
  .about-avatar::before {
    content: '';
    position: absolute;
    inset: -2px;
    background: conic-gradient(from 0deg, rgba(220,60,60,0.6), transparent, rgba(220,60,60,0.2), transparent, rgba(220,60,60,0.6));
    border-radius: 50%;
    animation: rotateBorder 4s linear infinite;
    z-index: -1;
  }
  @keyframes rotateBorder {
    to { transform: rotate(360deg); }
  }
  .about-avatar-initials {
    font-family: var(--font-ui);
    font-size: 3.5rem;
    font-weight: 800;
    color: rgba(255,255,255,0.9);
    letter-spacing: -0.02em;
  }
  .dev-badge {
    font-family: var(--font-mono);
    font-size: 0.6rem;
    letter-spacing: 0.2em;
    color: rgba(220,60,60,0.9);
    border: 1px solid rgba(220,60,60,0.4);
    padding: 4px 12px;
    border-radius: 999px;
    text-transform: uppercase;
    background: rgba(220,60,60,0.08);
  }
  .about-stats {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 16px;
    width: 100%;
    max-width: 280px;
    margin-top: 8px;
  }
  .about-stat {
    background: var(--color-surface-3);
    border: 1px solid var(--color-glass-border);
    border-radius: 6px;
    padding: 14px 16px;
    text-align: center;
  }
  .about-stat-num {
    font-family: var(--font-display);
    font-size: 1.6rem;
    font-weight: 600;
    color: rgba(220,60,60,0.9);
  }
  .about-stat-label {
    font-family: var(--font-mono);
    font-size: 0.55rem;
    letter-spacing: 0.1em;
    color: var(--color-text-dim);
    text-transform: uppercase;
    margin-top: 4px;
  }
  .about-content { }
  .about-name {
    font-family: var(--font-ui);
    font-size: 2rem;
    font-weight: 700;
    letter-spacing: 0.02em;
    margin-bottom: 4px;
  }
  .about-name span { color: rgba(220,60,60,0.9); }
  .about-role {
    font-family: var(--font-mono);
    font-size: 0.7rem;
    letter-spacing: 0.2em;
    color: var(--color-text-muted);
    text-transform: uppercase;
    margin-bottom: 28px;
  }
  .about-bio {
    color: var(--color-text-muted);
    font-size: 0.95rem;
    line-height: 1.85;
    margin-bottom: 32px;
    max-width: 520px;
  }
  .about-skills {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 32px;
  }
  .about-skill-tag {
    font-family: var(--font-mono);
    font-size: 0.62rem;
    letter-spacing: 0.1em;
    padding: 5px 14px;
    border-radius: 2px;
    background: rgba(220,60,60,0.08);
    border: 1px solid rgba(220,60,60,0.25);
    color: rgba(220,60,60,0.8);
    text-transform: uppercase;
  }
  .about-quote {
    border-left: 2px solid rgba(220,60,60,0.5);
    padding: 12px 20px;
    font-family: var(--font-display);
    font-style: italic;
    font-size: 1.1rem;
    color: rgba(255,255,255,0.6);
    background: rgba(220,60,60,0.04);
    border-radius: 0 4px 4px 0;
  }
  @media (max-width: 900px) {
    .about-inner { grid-template-columns: 1fr; gap: 40px; }
    .about-avatar-wrap { margin: 0 auto; }
  }

  /* RESPONSIVE */
  @media (max-width: 900px) {
    nav { padding: 20px 24px; }
    .nav-links { display: none; }
    section { padding: 80px 24px; }
    .features-grid { grid-template-columns: 1fr; }
    .cinematic-inner { grid-template-columns: 1fr; gap: 40px; }
    .shortcuts-inner { grid-template-columns: 1fr; gap: 40px; }
    .dl-cards { grid-template-columns: 1fr; }
    footer { flex-direction: column; gap: 24px; text-align: center; }
    .footer-links { flex-wrap: wrap; justify-content: center; }
  }
</style>
</head>
<body>

<!-- CURSOR -->
<div class="cursor" id="cursor"></div>
<div class="cursor-ring" id="cursorRing"></div>

<!-- NAV -->
<nav id="navbar">
  <a href="#" class="nav-logo"><img src="data:image/png;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/4gHYSUNDX1BST0ZJTEUAAQEAAAHIAAAAAAQwAABtbnRyUkdCIFhZWiAH4AABAAEAAAAAAABhY3NwAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAQAA9tYAAQAAAADTLQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAlkZXNjAAAA8AAAACRyWFlaAAABFAAAABRnWFlaAAABKAAAABRiWFlaAAABPAAAABR3dHB0AAABUAAAABRyVFJDAAABZAAAAChnVFJDAAABZAAAAChiVFJDAAABZAAAAChjcHJ0AAABjAAAADxtbHVjAAAAAAAAAAEAAAAMZW5VUwAAAAgAAAAcAHMAUgBHAEJYWVogAAAAAAAAb6IAADj1AAADkFhZWiAAAAAAAABimQAAt4UAABjaWFlaIAAAAAAAACSgAAAPhAAAts9YWVogAAAAAAAA9tYAAQAAAADTLXBhcmEAAAAAAAQAAAACZmYAAPKnAAANWQAAE9AAAApbAAAAAAAAAABtbHVjAAAAAAAAAAEAAAAMZW5VUwAAACAAAAAcAEcAbwBvAGcAbABlACAASQBuAGMALgAgADIAMAAxADb/2wBDAAUDBAQEAwUEBAQFBQUGBwwIBwcHBw8LCwkMEQ8SEhEPERETFhwXExQaFRERGCEYGh0dHx8fExciJCIeJBweHx7/2wBDAQUFBQcGBw4ICA4eFBEUHh4eHh4eHh4eHh4eHh4eHh4eHh4eHh4eHh4eHh4eHh4eHh4eHh4eHh4eHh4eHh4eHh7/wAARCAfQB9ADASIAAhEBAxEB/8QAHQABAAEEAwEAAAAAAAAAAAAAAAMBBgcIAgQJBf/EAF4QAQABAgQCAgkLDgoIBgIDAAABAgMEBRESBgcIIRMUIjFBUWFxgQkWN3J0dZGhsrO0FRgjMjhCVnaChJOUsdEnNUNTYnODwdLhFzNSVGOSw9MkKDZkZfAmNEWj8f/EABwBAQACAgMBAAAAAAAAAAAAAAABBgUHAwQIAv/EADURAQABAwICCQIFBAMBAQAAAAABAgMRBAUhMQYSExQiQXGBoVGRBxWxwdEyQlLhYnLwYYL/2gAMAwEAAhEDEQA/ANMgAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA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g5amqgCupqoArqpqpqagrqKamoKqaqAK6qAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAD/9k=" alt="Iyaki" style="height:36px;width:auto;display:block;" /></a>
  <ul class="nav-links">
    <li><a href="#features">Features</a></li>
    <li><a href="#cinematic">Experience</a></li>
    <li><a href="#formats">Formats</a></li>
    <li><a href="#shortcuts">Shortcuts</a></li>
    <li><a href="#about">About</a></li>
  </ul>
  <a href="#download" class="nav-cta">Download</a>
</nav>

<!-- HERO -->
<section id="hero">
  <div class="orb orb-1"></div>
  <div class="orb orb-2"></div>
  <div class="orb orb-3"></div>

  <div class="hero-eyebrow">— Cinematic Media Experience</div>
  <h1 class="hero-title">
    <em>Iyaki</em> Player
    <span class="line-2">Where every frame becomes art</span>
  </h1>
  <p class="hero-sub">
    A feature-rich, cinematic media player built for immersive playback.
    Universal format support, on-device intelligence, and a theater-grade experience.
  </p>
  <div class="hero-actions">
    <a href="#download" class="btn-primary">
      <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M12 5v14M5 12l7 7 7-7"/></svg>
      Download Free
    </a>
    <a href="#features" class="btn-secondary">
      Explore Features
      <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M5 12h14M12 5l7 7-7 7"/></svg>
    </a>
  </div>

  <!-- PLAYER MOCKUP -->
  <div class="hero-player">
    <div class="player-shell">
      <div class="player-titlebar">
        <div class="dot dot-r"></div>
        <div class="dot dot-y"></div>
        <div class="dot dot-g"></div>
        <span class="player-filename">Interstellar.2014.mkv — Iyaki Player</span>
      </div>
      <div class="player-screen">
        <div class="player-scene">
          <div class="scene-glow"></div>
          <div class="sidebar-dim"></div>
          <div class="scene-title-card">
            <h3>Interstellar</h3>
            <p>&#9654; 1:24:36 — Theater Mode Active</p>
          </div>
          <!-- Visualizer bars at bottom -->
          <div class="visualizer">
            <div class="v-bar" style="--dur:0.5s;--h:8px"></div>
            <div class="v-bar" style="--dur:0.7s;--h:22px"></div>
            <div class="v-bar" style="--dur:0.4s;--h:14px"></div>
            <div class="v-bar" style="--dur:0.9s;--h:30px"></div>
            <div class="v-bar" style="--dur:0.6s;--h:18px"></div>
            <div class="v-bar" style="--dur:0.8s;--h:25px"></div>
            <div class="v-bar" style="--dur:0.5s;--h:12px"></div>
            <div class="v-bar" style="--dur:0.7s;--h:20px"></div>
            <div class="v-bar" style="--dur:0.4s;--h:15px"></div>
            <div class="v-bar" style="--dur:0.6s;--h:28px"></div>
            <div class="v-bar" style="--dur:0.9s;--h:10px"></div>
            <div class="v-bar" style="--dur:0.5s;--h:22px"></div>
          </div>
        </div>
      </div>
      <div class="player-controls">
        <div class="progress-track"><div class="progress-fill"></div></div>
        <div class="ctrl-row">
          <div class="ctrl-btns">
            <!-- Skip Back -->
            <span class="ctrl-icon">
              <svg viewBox="0 0 24 24"><path d="M19 20L9 12l10-8v16z"/><line x1="5" y1="4" x2="5" y2="20" stroke-width="2" stroke="currentColor"/></svg>
            </span>
            <!-- Play -->
            <div class="ctrl-play">
              <svg viewBox="0 0 24 24" fill="#000"><polygon points="5,3 19,12 5,21"/></svg>
            </div>
            <!-- Skip Forward -->
            <span class="ctrl-icon">
              <svg viewBox="0 0 24 24"><path d="M5 4l10 8-10 8V4z"/><line x1="19" y1="4" x2="19" y2="20" stroke-width="2" stroke="currentColor"/></svg>
            </span>
            <span class="ctrl-time">1:24:36 / 2:49:17</span>
          </div>
          <div class="ctrl-right">
            <div class="level-meters">
              <div class="meter-channel">
                <div class="meter-label">L</div>
                <div class="meter-bar"><div class="meter-fill meter-fill-l"></div></div>
              </div>
              <div class="meter-channel">
                <div class="meter-label">R</div>
                <div class="meter-bar"><div class="meter-fill meter-fill-r"></div></div>
              </div>
            </div>
            <div class="vol-bar"><div class="vol-fill"></div></div>
            <!-- Volume icon -->
            <span class="ctrl-icon active">
              <svg viewBox="0 0 24 24"><polygon points="11,5 6,9 2,9 2,15 6,15 11,19"/><path d="M15.54 8.46a5 5 0 0 1 0 7.07"/><path d="M19.07 4.93a10 10 0 0 1 0 14.14"/></svg>
            </span>
            <span class="badge-pill">1.0x</span>
            <span class="badge-pill">Sub</span>
            <!-- Fullscreen icon -->
            <span class="ctrl-icon">
              <svg viewBox="0 0 24 24"><path d="M8 3H5a2 2 0 0 0-2 2v3m18 0V5a2 2 0 0 0-2-2h-3m0 18h3a2 2 0 0 0 2-2v-3M3 16v3a2 2 0 0 0 2 2h3"/></svg>
            </span>
          </div>
        </div>
      </div>
    </div>
  </div>
</section>

<!-- MARQUEE -->
<div class="marquee-section">
  <div class="marquee-track">
    <span class="marquee-item"><span>&#9654;</span>MP4 &nbsp;·&nbsp; MKV &nbsp;·&nbsp; WebM &nbsp;·&nbsp; AVI &nbsp;·&nbsp; MOV &nbsp;·&nbsp; MP3 &nbsp;·&nbsp; FLAC &nbsp;·&nbsp; WAV &nbsp;·&nbsp; AAC &nbsp;·&nbsp; OPUS &nbsp;·&nbsp; OGG &nbsp;·&nbsp; Theater Dimming &nbsp;·&nbsp; OCR Engine &nbsp;·&nbsp; Subtitle Engine &nbsp;·&nbsp; Multi-Track Audio &nbsp;·&nbsp; Audio Visualizer &nbsp;·&nbsp;</span>
    <span class="marquee-item"><span>&#9654;</span>MP4 &nbsp;·&nbsp; MKV &nbsp;·&nbsp; WebM &nbsp;·&nbsp; AVI &nbsp;·&nbsp; MOV &nbsp;·&nbsp; MP3 &nbsp;·&nbsp; FLAC &nbsp;·&nbsp; WAV &nbsp;·&nbsp; AAC &nbsp;·&nbsp; OPUS &nbsp;·&nbsp; OGG &nbsp;·&nbsp; Theater Dimming &nbsp;·&nbsp; OCR Engine &nbsp;·&nbsp; Subtitle Engine &nbsp;·&nbsp; Multi-Track Audio &nbsp;·&nbsp; Audio Visualizer &nbsp;·&nbsp;</span>
  </div>
</div>

<!-- FEATURES -->
<section id="features">
  <div class="features-header reveal">
    <div class="section-label">// 01 — Capabilities</div>
    <h2 class="section-title">Built for<br><em>every frame,</em><br>every format.</h2>
  </div>
  <div class="features-grid">
    <!-- 01 Universal Formats — film icon -->
    <div class="feat-card reveal" style="transition-delay:0.1s">
      <span class="feat-num">01</span>
      <span class="feat-icon">
        <svg viewBox="0 0 24 24"><rect x="2" y="2" width="20" height="20" rx="2"/><line x1="7" y1="2" x2="7" y2="22"/><line x1="17" y1="2" x2="17" y2="22"/><line x1="2" y1="12" x2="22" y2="12"/><line x1="2" y1="7" x2="7" y2="7"/><line x1="17" y1="7" x2="22" y2="7"/><line x1="17" y1="17" x2="22" y2="17"/><line x1="2" y1="17" x2="7" y2="17"/></svg>
      </span>
      <div class="feat-title">Universal Formats</div>
      <p class="feat-desc">MP4, MKV, WebM, AVI, MOV, MP3, FLAC, WAV, AAC and dozens more. Play any file without conversion or codec hunting.</p>
    </div>
    <!-- 02 OCR — scan icon -->
    <div class="feat-card reveal" style="transition-delay:0.15s">
      <span class="feat-num">02</span>
      <span class="feat-icon">
        <svg viewBox="0 0 24 24"><path d="M3 7V5a2 2 0 0 1 2-2h2"/><path d="M17 3h2a2 2 0 0 1 2 2v2"/><path d="M21 17v2a2 2 0 0 1-2 2h-2"/><path d="M7 21H5a2 2 0 0 1-2-2v-2"/><line x1="3" y1="12" x2="21" y2="12"/></svg>
      </span>
      <div class="feat-title">On-Device OCR</div>
      <p class="feat-desc">Powered by Tesseract.js. Pause on any frame, extract and copy text directly from video in seconds.</p>
    </div>
    <!-- 03 Theater Dimming — moon icon -->
    <div class="feat-card reveal" style="transition-delay:0.2s">
      <span class="feat-num">03</span>
      <span class="feat-icon">
        <svg viewBox="0 0 24 24"><path d="M21 12.79A9 9 0 1 1 11.21 3 7 7 0 0 0 21 12.79z"/></svg>
      </span>
      <div class="feat-title">Theater Dimming</div>
      <p class="feat-desc">Immersive mode dims the entire surrounding UI after a short delay. Pure cinematic focus, zero distractions.</p>
    </div>
    <!-- 04 Audio Visualizer — activity bars icon -->
    <div class="feat-card reveal" style="transition-delay:0.25s">
      <span class="feat-num">04</span>
      <span class="feat-icon">
        <svg viewBox="0 0 24 24"><line x1="22" y1="12" x2="18" y2="12"/><line x1="6" y1="12" x2="2" y2="12"/><line x1="12" y1="2" x2="12" y2="6"/><line x1="12" y1="18" x2="12" y2="22"/><polyline points="20 6 16 10"/><polyline points="8 14 4 18"/><polyline points="4 6 8 10"/><polyline points="16 14 20 18"/></svg>
      </span>
      <div class="feat-title">Audio Visualizer</div>
      <p class="feat-desc">Dynamic audio visualizer with album artwork display and animated level bars for every audio file.</p>
    </div>
    <!-- 05 Subtitle Engine — message square icon -->
    <div class="feat-card reveal" style="transition-delay:0.3s">
      <span class="feat-num">05</span>
      <span class="feat-icon">
        <svg viewBox="0 0 24 24"><path d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z"/></svg>
      </span>
      <div class="feat-title">Subtitle Engine</div>
      <p class="feat-desc">Custom SRT and VTT rendering. Auto-convert subtitles, adjust size, color, background, and drop-shadows.</p>
    </div>
    <!-- 06 Live Video Tuning — sliders icon -->
    <div class="feat-card reveal" style="transition-delay:0.35s">
      <span class="feat-num">06</span>
      <span class="feat-icon">
        <svg viewBox="0 0 24 24"><line x1="4" y1="21" x2="4" y2="14"/><line x1="4" y1="10" x2="4" y2="3"/><line x1="12" y1="21" x2="12" y2="12"/><line x1="12" y1="8" x2="12" y2="3"/><line x1="20" y1="21" x2="20" y2="16"/><line x1="20" y1="12" x2="20" y2="3"/><line x1="1" y1="14" x2="7" y2="14"/><line x1="9" y1="8" x2="15" y2="8"/><line x1="17" y1="16" x2="23" y2="16"/></svg>
      </span>
      <div class="feat-title">Live Video Tuning</div>
      <p class="feat-desc">Adjust brightness, contrast, and saturation in real-time. Plus precise L/R audio level meters via Web Audio API.</p>
    </div>
    <!-- 07 Persistent Library — database icon -->
    <div class="feat-card reveal" style="transition-delay:0.4s">
      <span class="feat-num">07</span>
      <span class="feat-icon">
        <svg viewBox="0 0 24 24"><ellipse cx="12" cy="5" rx="9" ry="3"/><path d="M21 12c0 1.66-4 3-9 3s-9-1.34-9-3"/><path d="M3 5v14c0 1.66 4 3 9 3s9-1.34 9-3V5"/></svg>
      </span>
      <div class="feat-title">Persistent Library</div>
      <p class="feat-desc">IndexedDB-powered media library with intelligent thumbnail generation. Your collection survives every relaunch.</p>
    </div>
    <!-- 08 Multi-Track Audio — layers icon -->
    <div class="feat-card reveal" style="transition-delay:0.45s">
      <span class="feat-num">08</span>
      <span class="feat-icon">
        <svg viewBox="0 0 24 24"><polygon points="12 2 2 7 12 12 22 7 12 2"/><polyline points="2 17 12 22 22 17"/><polyline points="2 12 12 17 22 12"/></svg>
      </span>
      <div class="feat-title">Multi-Track Audio</div>
      <p class="feat-desc">Seamlessly switch between multiple embedded audio tracks. Dual-audio films, commentary tracks — all at your fingertips.</p>
    </div>
    <!-- 09 Network Streams — wifi icon -->
    <div class="feat-card reveal" style="transition-delay:0.5s">
      <span class="feat-num">09</span>
      <span class="feat-icon">
        <svg viewBox="0 0 24 24"><path d="M5 12.55a11 11 0 0 1 14.08 0"/><path d="M1.42 9a16 16 0 0 1 21.16 0"/><path d="M8.53 16.11a6 6 0 0 1 6.95 0"/><line x1="12" y1="20" x2="12.01" y2="20"/></svg>
      </span>
      <div class="feat-title">Network Streams</div>
      <p class="feat-desc">Stream media directly from URLs. Local files or network sources — Iyaki handles them all seamlessly.</p>
    </div>
  </div>
</section>

<!-- CINEMATIC EXPERIENCE -->
<section id="cinematic">
  <div class="cinematic-inner">
    <div class="reveal-left">
      <div class="section-label">// 02 — Experience</div>
      <h2 class="section-title">Cinema in<br>every <em>pixel.</em></h2>
      <p class="section-body">Theater Dimming gently fades the surrounding interface the moment playback begins. Your media expands — the controls vanish. Pure immersion, by design.</p>
      <p class="section-body" style="margin-top:16px">The custom audio visualizer transforms music sessions into visual performances. Album art, animated bars, real-time dB meters — all rendered natively.</p>
    </div>
    <div class="reveal-right">
      <div class="cinematic-visual">
        <div class="cv-bg"></div>
        <div class="cv-content">
          <div class="dim-demo">
            <div class="dim-bar active"></div>
            <div class="dim-main">
              <div class="sidebar-dim"></div>
              <span class="theater-text">Dune</span>
            </div>
            <div class="dim-bar"></div>
          </div>
          <div class="visualizer-demo">
            <div class="album-art">
              <svg viewBox="0 0 24 24"><circle cx="12" cy="12" r="10"/><circle cx="12" cy="12" r="3"/></svg>
            </div>
            <div class="vis-bars">
              <div class="vis-b" style="--vd:0.4s;--vh:90%"></div>
              <div class="vis-b" style="--vd:0.6s;--vh:40%"></div>
              <div class="vis-b" style="--vd:0.5s;--vh:75%"></div>
              <div class="vis-b" style="--vd:0.8s;--vh:55%"></div>
              <div class="vis-b" style="--vd:0.45s;--vh:85%"></div>
              <div class="vis-b" style="--vd:0.7s;--vh:45%"></div>
              <div class="vis-b" style="--vd:0.55s;--vh:70%"></div>
              <div class="vis-b" style="--vd:0.65s;--vh:60%"></div>
              <div class="vis-b" style="--vd:0.4s;--vh:95%"></div>
              <div class="vis-b" style="--vd:0.75s;--vh:35%"></div>
              <div class="vis-b" style="--vd:0.5s;--vh:80%"></div>
              <div class="vis-b" style="--vd:0.85s;--vh:50%"></div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</section>

<!-- OCR SECTION -->
<section id="ocr" style="background:var(--color-surface)">
  <div style="max-width:1100px;margin:0 auto;display:grid;grid-template-columns:1fr 1fr;gap:80px;align-items:center" class="cinematic-inner">
    <div class="reveal-left">
      <div class="ocr-demo">
        <div class="ocr-frame">
          <div class="ocr-scan-line"></div>
          <div class="ocr-text-block">
            <p>"Do not go gentle into<br>that good night."</p>
          </div>
        </div>
        <div class="ocr-result">
          <span class="ocr-badge">&#9679; Extracted</span>
          <span class="ocr-text-out">Text copied to clipboard</span>
        </div>
      </div>
    </div>
    <div class="reveal-right">
      <div class="section-label">// 03 — Intelligence</div>
      <h2 class="section-title">Read what you<br><em>watch.</em></h2>
      <p class="section-body">Pause. Iyaki's on-device OCR engine, powered by Tesseract.js, scans the frame and extracts every word. No internet. No upload. Pure local intelligence at the moment you need it.</p>
    </div>
  </div>
</section>

<!-- FORMATS -->
<section id="formats">
  <div class="reveal">
    <div class="section-label" style="text-align:center">// 04 — Compatibility</div>
    <h2 class="section-title" style="text-align:center">Every format.<br><em>Zero compromise.</em></h2>
  </div>
  <div class="format-cloud reveal">
    <span class="fmt-tag video">MP4</span>
    <span class="fmt-tag video">MKV</span>
    <span class="fmt-tag video">WebM</span>
    <span class="fmt-tag video">AVI</span>
    <span class="fmt-tag video">MOV</span>
    <span class="fmt-tag video">WMV</span>
    <span class="fmt-tag video">FLV</span>
    <span class="fmt-tag video">TS</span>
    <span class="fmt-tag video">M4V</span>
    <span class="fmt-tag video">3GP</span>
    <span class="fmt-tag audio">MP3</span>
    <span class="fmt-tag audio">FLAC</span>
    <span class="fmt-tag audio">WAV</span>
    <span class="fmt-tag audio">AAC</span>
    <span class="fmt-tag audio">OGG</span>
    <span class="fmt-tag audio">OPUS</span>
    <span class="fmt-tag audio">M4A</span>
    <span class="fmt-tag audio">AIFF</span>
    <span class="fmt-tag audio">WMA</span>
    <span class="fmt-tag">SRT</span>
    <span class="fmt-tag">VTT</span>
    <span class="fmt-tag">Network URLs</span>
  </div>
  <div style="display:flex;gap:24px;justify-content:center;margin-top:32px" class="reveal">
    <div style="display:flex;align-items:center;gap:8px;font-family:var(--font-mono);font-size:0.65rem;color:var(--color-text-muted)">
      <span style="width:10px;height:10px;background:rgba(220,60,60,0.8);display:block;border-radius:1px"></span>Video
    </div>
    <div style="display:flex;align-items:center;gap:8px;font-family:var(--font-mono);font-size:0.65rem;color:var(--color-text-muted)">
      <span style="width:10px;height:10px;background:rgba(60,200,200,0.8);display:block;border-radius:1px"></span>Audio
    </div>
    <div style="display:flex;align-items:center;gap:8px;font-family:var(--font-mono);font-size:0.65rem;color:var(--color-text-muted)">
      <span style="width:10px;height:10px;background:rgba(255,255,255,0.1);display:block;border-radius:1px"></span>Other
    </div>
  </div>
</section>

<!-- SHORTCUTS -->
<section id="shortcuts">
  <div class="shortcuts-inner">
    <div class="reveal-left">
      <div class="section-label">// 05 — Control</div>
      <h2 class="section-title">Every key,<br><em>a command.</em></h2>
      <p class="section-body">Iyaki is built for those who prefer their hands on the keyboard. A comprehensive shortcut system means you never leave the flow of watching.</p>
    </div>
    <div class="reveal-right">
      <div class="shortcut-grid">
        <div class="sc-item"><span class="kbd">Space</span><span class="sc-label">Play / Pause</span></div>
        <div class="sc-item"><span class="kbd">F</span><span class="sc-label">Fullscreen</span></div>
        <div class="sc-item"><span class="kbd">M</span><span class="sc-label">Mute</span></div>
        <div class="sc-item"><span class="kbd">&#8592;&#8594;</span><span class="sc-label">Seek ±10s</span></div>
        <div class="sc-item"><span class="kbd">&#8593;&#8595;</span><span class="sc-label">Volume</span></div>
        <div class="sc-item"><span class="kbd">C</span><span class="sc-label">Subtitles</span></div>
        <div class="sc-item"><span class="kbd">A</span><span class="sc-label">Audio Track</span></div>
        <div class="sc-item"><span class="kbd">S</span><span class="sc-label">Settings</span></div>
        <div class="sc-item"><span class="kbd">I</span><span class="sc-label">Metadata</span></div>
        <div class="sc-item"><span class="kbd">J / L</span><span class="sc-label">Seek ±10s</span></div>
        <div class="sc-item"><span class="kbd">K</span><span class="sc-label">Play / Pause</span></div>
        <div class="sc-item"><span class="kbd">&lt; / &gt;</span><span class="sc-label">Speed</span></div>
      </div>
    </div>
  </div>
</section>

<!-- DOWNLOAD -->
<section id="download">
  <div class="dl-inner">
    <div class="reveal">
      <div class="section-label" style="text-align:center">// 06 — Download</div>
      <h2 class="section-title">Start watching<br><em>right now.</em></h2>
      <p class="section-body" style="margin:20px auto 0;text-align:center">Free to download. No subscription. No telemetry. Just pure, local, cinematic playback.</p>
      <div class="dl-version">v1.0.0 — Released 2025</div>
    </div>
    <div class="dl-cards reveal">
      <!-- macOS -->
      <a href="Iyaki_Player-1_0_0-arm64.dmg" download class="dl-card featured">
        <span class="dl-os-icon">
          <!-- Apple logo SVG -->
          <svg viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg" fill="currentColor"><path d="M18.71 19.5c-.83 1.24-1.71 2.45-3.05 2.47-1.34.03-1.77-.79-3.29-.79-1.53 0-2 .77-3.27.82-1.31.05-2.3-1.32-3.14-2.53C4.25 17 2.94 12.45 4.7 9.39c.87-1.52 2.43-2.48 4.12-2.51 1.28-.02 2.5.87 3.29.87.78 0 2.26-1.07 3.8-.91.65.03 2.47.26 3.64 1.98-.09.06-2.17 1.28-2.15 3.81.03 3.02 2.65 4.03 2.68 4.04-.03.07-.42 1.44-1.38 2.83M13 3.5c.73-.83 1.94-1.46 2.94-1.5.13 1.17-.34 2.35-1.04 3.19-.69.85-1.83 1.51-2.95 1.42-.15-1.15.41-2.35 1.05-3.11z"/></svg>
        </span>
        <div class="dl-os-name">macOS</div>
        <div class="dl-os-arch">Apple Silicon (ARM64)</div>
        <div class="dl-size">Iyaki_Player-1_0_0-arm64.dmg</div>
        <div class="dl-btn">
          <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M12 5v14M5 12l7 7 7-7"/></svg>
          Download
        </div>
      </a>
      <!-- Windows -->
      <div class="dl-card" style="opacity:0.5">
        <span class="dl-os-icon">
          <!-- Windows logo -->
          <svg viewBox="0 0 88 88" xmlns="http://www.w3.org/2000/svg"><path d="M0 12.402l35.687-4.86.016 34.423-35.67.203zm35.67 33.529l.028 34.453L.028 75.48.026 45.7zm4.326-39.025L87.314 0v41.527l-47.318.376zm47.329 39.349l-.011 41.34-47.318-6.678-.066-34.739z"/></svg>
        </span>
        <div class="dl-os-name">Windows</div>
        <div class="dl-os-arch">x64 — Coming Soon</div>
        <div class="dl-size">—</div>
        <div class="dl-btn" style="opacity:0.5">Coming Soon</div>
      </div>
      <!-- Linux -->
      <div class="dl-card" style="opacity:0.5">
        <span class="dl-os-icon">
          <!-- Linux Tux-style simplified icon -->
          <svg viewBox="0 0 24 24" fill="currentColor" xmlns="http://www.w3.org/2000/svg"><path d="M12 2a2 2 0 0 1 2 2c0 .74-.4 1.39-1 1.73V7h1a7 7 0 0 1 7 7H3a7 7 0 0 1 7-7h1V5.73c-.6-.34-1-.99-1-1.73a2 2 0 0 1 2-2M7 14l-1.5 4.5L7 20h2l1-3H14l1 3h2l1.5-1.5L17 14H7m2 1.5h6l.5 2H8.5l.5-2z"/></svg>
        </span>
        <div class="dl-os-name">Linux</div>
        <div class="dl-os-arch">x86_64 — Coming Soon</div>
        <div class="dl-size">—</div>
        <div class="dl-btn" style="opacity:0.5">Coming Soon</div>
      </div>
    </div>
    <div class="reveal" style="margin-top:40px">
      <p style="font-family:var(--font-mono);font-size:0.6rem;color:var(--color-text-dim);letter-spacing:0.1em">Requires macOS 11.0+ (Big Sur) for Apple Silicon · Built with Electron</p>
    </div>
  </div>
</section>


<!-- ABOUT / DEVELOPER -->
<section id="about">
  <div class="about-inner">
    <div class="about-avatar-wrap reveal">
      <div class="about-avatar">
        <span class="about-avatar-initials">BS</span>
      </div>
      <div class="dev-badge">&#9679; Developer</div>
      <div class="about-stats">
        <div class="about-stat">
          <div class="about-stat-num">1</div>
          <div class="about-stat-label">Apps</div>
        </div>
        <div class="about-stat">
          <div class="about-stat-num">22+</div>
          <div class="about-stat-label">Formats</div>
        </div>
        <div class="about-stat">
          <div class="about-stat-num">100%</div>
          <div class="about-stat-label">Local</div>
        </div>
        <div class="about-stat">
          <div class="about-stat-num">0</div>
          <div class="about-stat-label">Ads</div>
        </div>
      </div>
    </div>
    <div class="about-content reveal-right">
      <div class="section-label">// 07 — The Creator</div>
      <div class="about-name">Bw<span>Sanjay</span></div>
      <div class="about-role">Application Developer &amp; Designer</div>
      <p class="about-bio">
        BwSanjay is the sole developer and creative force behind Iyaki Player — a passion project born from a love of cinema, clean design, and the belief that software should feel as good as it works. Every pixel, every animation, and every feature in Iyaki was crafted with intent and care.
      </p>
      <p class="about-bio" style="margin-top:-16px">
        Specializing in cross-platform desktop applications, BwSanjay combines deep technical expertise with an eye for cinematic aesthetics. Iyaki Player represents the intersection of performance engineering and visual craft — built entirely on local, privacy-first principles.
      </p>
      <div class="about-skills">
        <span class="about-skill-tag">Electron</span>
        <span class="about-skill-tag">JavaScript</span>
        <span class="about-skill-tag">FFmpeg</span>
        <span class="about-skill-tag">Tesseract.js</span>
        <span class="about-skill-tag">UI Design</span>
        <span class="about-skill-tag">macOS</span>
        <span class="about-skill-tag">Cross-Platform</span>
      </div>
      <div class="about-quote">
        "Great software should disappear — leaving only the experience."
      </div>
    </div>
  </div>
</section>

<!-- FOOTER -->
<footer>
  <div class="footer-logo">Iyaki<span>.</span></div>
  <ul class="footer-links">
    <li><a href="#features">Features</a></li>
    <li><a href="#download">Download</a></li>
    <li><a href="#shortcuts">Shortcuts</a></li>
    <li><a href="#about">About</a></li>
  </ul>
  <div class="footer-copy">© 2025 Iyaki Player · All rights reserved</div>
</footer>

<script>
  // Cursor
  const cursor = document.getElementById('cursor');
  const ring = document.getElementById('cursorRing');
  let mx = 0, my = 0, rx = 0, ry = 0;
  document.addEventListener('mousemove', e => { mx = e.clientX; my = e.clientY; cursor.style.left = mx+'px'; cursor.style.top = my+'px'; });
  const animRing = () => { rx += (mx - rx) * 0.12; ry += (my - ry) * 0.12; ring.style.left = rx+'px'; ring.style.top = ry+'px'; requestAnimationFrame(animRing); };
  animRing();

  // Nav scroll
  const nav = document.getElementById('navbar');
  window.addEventListener('scroll', () => { nav.classList.toggle('scrolled', window.scrollY > 60); });

  // Scroll reveal
  const reveals = document.querySelectorAll('.reveal, .reveal-left, .reveal-right');
  const obs = new IntersectionObserver((entries) => {
    entries.forEach((e) => {
      if (e.isIntersecting) { e.target.classList.add('visible'); }
    });
  }, { threshold: 0.12 });
  reveals.forEach(el => obs.observe(el));

  // Stagger reveal children
  document.querySelectorAll('.features-grid .feat-card').forEach((card, i) => {
    card.style.transitionDelay = (i * 0.07) + 's';
  });
</script>
</body>
</html>
