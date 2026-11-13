---
name: web-game-audio
description: "Use when adding sound effects or music to a browser game: Web Audio API chip-style synthesis (square, triangle, noise), retro SFX like lasers, explosions, coins and jumps, simple sequenced jingles, autoplay unlock, volume/mute settings, and 3D positional audio in Three.js. Does not cover voice chat or streaming media."
---

# Game Audio (Web Audio, chip-style)

Synthesize sounds in code — no sample files needed, fully offline, era-appropriate.

## 1. Rules
- One shared `AudioContext`, created lazily and **resumed on the first user gesture** (click/keydown/touch). Browsers block audio before that.
- Master `GainNode` → destination. Separate `sfx` and `music` gains for independent volume sliders. Persist volume/mute in Dexie or localStorage.
- Trigger sounds from game events (emitted by the simulation), never from render code.
- Limit concurrent voices (e.g. max 8; drop or steal oldest) to avoid noise pile-up.
- Mute when the tab is hidden (`visibilitychange` → `ctx.suspend()`).

## 2. Minimal synth
```typescript
let ctx: AudioContext | null = null; let master: GainNode;
export function audio() {
  if (!ctx) { ctx = new AudioContext(); master = ctx.createGain(); master.gain.value = 0.3; master.connect(ctx.destination); }
  if (ctx.state === "suspended") void ctx.resume();
  return ctx;
}
export function tone(freq: number, dur: number, type: OscillatorType = "square", slideTo?: number, vol = 0.5) {
  const a = audio(), t = a.currentTime;
  const o = a.createOscillator(), g = a.createGain();
  o.type = type; o.frequency.setValueAtTime(freq, t);
  if (slideTo) o.frequency.exponentialRampToValueAtTime(slideTo, t + dur);
  g.gain.setValueAtTime(vol, t); g.gain.exponentialRampToValueAtTime(0.001, t + dur);
  o.connect(g).connect(master); o.start(t); o.stop(t + dur);
}
let noiseBuf: AudioBuffer | null = null;
export function noise(dur: number, vol = 0.5) {
  const a = audio(), t = a.currentTime;
  noiseBuf ??= (() => { const b = a.createBuffer(1, a.sampleRate, a.sampleRate); const d = b.getChannelData(0); for (let i = 0; i < d.length; i++) d[i] = Math.random() * 2 - 1; return b; })();
  const s = a.createBufferSource(), g = a.createGain(); s.buffer = noiseBuf;
  g.gain.setValueAtTime(vol, t); g.gain.exponentialRampToValueAtTime(0.001, t + dur);
  s.connect(g).connect(master); s.start(t); s.stop(t + dur);
}
```
Recipes: laser `tone(1200, 0.15, "square", 200)`; jump `tone(300, 0.12, "square", 700)`; coin `tone(988,0.08)` then `tone(1319,0.3)`; explosion `noise(0.5)`; chess move click `noise(0.04, 0.3)` + `tone(180, 0.05, "triangle")`.

## 3. Music & jingles
- Schedule notes ahead using `ctx.currentTime` (lookahead ~100 ms) — never with `setTimeout` timing alone.
- Encode tunes as `[noteName, beats]` arrays; convert with `440 * 2 ** ((midi - 69) / 12)`.
- Use original compositions or public-domain melodies, not copyrighted game themes.
- Background loops like Space Invaders' 4-note march: tie tempo to game state (fewer enemies → faster).

## 4. Libraries (optional)
`zzfx` (tiny, great for retro SFX) or `howler` if the user supplies sample files. Avoid heavy frameworks.

## 5. 3D positional audio
In R3F, drei `<PositionalAudio>` for sample files; for synth, route through a `PannerNode` positioned from the entity.
