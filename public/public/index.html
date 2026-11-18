const W = 2000, H = 500, DT = 0.05;
const dist = (a, b, c, d) => Math.hypot(a - c, b - d);
const clamp = (v, a, b) => Math.max(a, Math.min(b, +v || 0));

// 챔피언 정의: k=스킬 종류
const CH = {
  xerath:   { hp: 420, spd: 210, ar: 260, ad: 22,
    q: { k: "shot", cd: 4, rng: 1100, spd: 900, dmg: 100, r: 16, pierce: 1 },
    w: { k: "aoeAt", cd: 6, rng: 700, rad: 100, dmg: 80 } },
  sett:     { hp: 750, spd: 225, ar: 130, ad: 30,
    q: { k: "aoeSelf", cd: 4, rad: 140, dmg: 75 },
    w: { k: "shield", cd: 8, amt: 200, dur: 3 } },
  yasuo:    { hp: 520, spd: 235, ar: 130, ad: 26,
    q: { k: "shot", cd: 2, rng: 380, spd: 800, dmg: 65, r: 18, pierce: 1 },
    w: { k: "wall", cd: 9, dur: 3.5, rad: 110, rng: 200 } },
  teemo:    { hp: 400, spd: 225, ar: 260, ad: 20,
    q: { k: "shot", cd: 2.5, rng: 650, spd: 850, dmg: 60, r: 12 },
    w: { k: "haste", cd: 8, dur: 2.5 } },
  seungwoo: { hp: 560, spd: 225, ar: 140, ad: 28,
    q: { k: "fan", cd: 3.5, rng: 450, spd: 700, dmg: 40, r: 12, ang: 0.35 },
    w: { k: "heal", cd: 8, amt: 160 } },
};
const KEYS = Object.keys(CH);
const TX = [820, 560, 300]; // 바깥 타워부터

export default {
  fetch(req, env) {
    const u = new URL(req.url);
    if (u.pathname !== "/ws") return new Response("minirift server ok");
    const room = (u.searchParams.get("room") || "main").slice(0, 20);
    return env.ROOM.get(env.ROOM.idFromName(room)).fetch(req);
  },
};

export class Room {
  constructor() { this.socks = new Map(); this.nid = 1; this.timer = null; this.phase = "lobby"; this.host = 0; this.tk = 0; this.reset(); }

  reset() {
    this.towers = [];
    TX.forEach((x, i) => {
      this.towers.push({ team: "b", i, x, y: H / 2, hp: 800, atk: 0, fx: 0, fy: 0, fl: 0 });
      this.towers.push({ team: "r", i, x: W - x, y: H / 2, hp: 800, atk: 0, fx: 0, fy: 0, fl: 0 });
    });
    this.nex = { b: { nx: "b", x: 90, y: H / 2, hp: 1500 }, r: { nx: "r", x: W - 90, y: H / 2, hp: 1500 } };
    this.shots = []; this.walls = []; this.minions = []; this.fx = [];
    this.relics = [[680, 130], [680, 370], [1320, 130], [1320, 370]].map(([x, y]) => ({ x, y, cd: 0 }));
    this.t = 0; this.over = 0; this.win = "";
    for (const p of this.socks.values()) { p.k = 0; p.d = 0; this.spawn(p); }
  }

  spawn(p) {
    const c = CH[p.ch];
    p.hp = p.max = c.hp; p.sh = 0; p.shT = 0; p.haste = 0;
    p.x = p.team === "b" ? 200 : W - 200;
    p.y = H / 2 + (Math.random() - 0.5) * 200;
    p.tx = p.x; p.ty = p.y; p.dead = 0; p.cdq = 0; p.cdw = 0; p.atk = 0;
  }

  fetch(req) {
    if (req.headers.get("Upgrade") !== "websocket") return new Response("ws only", { status: 426 });
    const [client, server] = Object.values(new WebSocketPair());
    server.accept();
    const id = this.nid++;
    const nb = [...this.socks.values()].filter((p) => p.team === "b").length;
    const p = { id, ws: server, team: nb <= this.socks.size - nb ? "b" : "r", name: "P" + id, k: 0, d: 0,
      ch: KEYS[Math.floor(Math.random() * KEYS.length)] };
    this.spawn(p);
    this.socks.set(id, p);

    server.addEventListener("message", (e) => {
      try {
        const m = JSON.parse(e.data);
        if (m.t === "name") {
          p.name = String(m.n || "").slice(0, 10) || p.name;
          if (CH[m.ch]) p.ch = m.ch;
          this.spawn(p);
          if (!this.host || !this.socks.has(this.host)) this.host = id;
          server.send(JSON.stringify({ t: "hi", id, team: p.team, ch: p.ch, cdq: CH[p.ch].q.cd, cdw: CH[p.ch].w.cd }));
          return;
        }
        if (m.t === "chat") {
          const now = Date.now();
          if (now - (p.lc || 0) < 500) return;
          p.lc = now;
          const s = String(m.m || "").slice(0, 100).trim();
          if (s) this.bcast({ t: "chat", n: p.name, team: p.team, m: s });
          return;
        }
        if (this.phase === "lobby") {
          if (m.t === "pick" && CH[m.ch]) {
            p.ch = m.ch; this.spawn(p);
            server.send(JSON.stringify({ t: "hi", id, team: p.team, ch: p.ch, cdq: CH[p.ch].q.cd, cdw: CH[p.ch].w.cd }));
          }
          if (m.t === "team") p.team = p.team === "b" ? "r" : "b";
          if (m.t === "start" && this.host === id) { this.phase = "play"; this.reset(); }
          return;
        }
        if (p.dead > 0 || this.over) return;
        if (m.t === "mv") { p.tx = clamp(m.x, 0, W); p.ty = clamp(m.y, 60, H - 60); }
        if (m.t === "q" || m.t === "w") this.cast(p, m.t, +m.x || 0, +m.y || 0);
      } catch {}
    });
    const bye = () => {
      this.socks.delete(id);
      if (this.host === id) this.host = [...this.socks.keys()][0] || 0;
      if (!this.socks.size) { clearInterval(this.timer); this.timer = null; this.phase = "lobby"; this.reset(); }
    };
    server.addEventListener("close", bye);
    server.addEventListener("error", bye);
    if (!this.timer) this.timer = setInterval(() => this.tick(), DT * 1000);
    return new Response(null, { status: 101, webSocket: client });
  }

  bcast(o) { const s = JSON.stringify(o); for (const q of this.socks.values()) { try { q.ws.send(s); } catch {} } }

  cast(p, k, x, y) {
    const s = CH[p.ch][k], cd = "cd" + k;
    if (p[cd] > 0) return;
    p[cd] = s.cd;
    const a = Math.atan2(y - p.y, x - p.x);
    const shot = (an) => this.shots.push({ x: p.x, y: p.y, vx: Math.cos(an) * s.spd, vy: Math.sin(an) * s.spd,
      life: s.rng / s.spd, team: p.team, by: p, dmg: s.dmg, r: s.r, pierce: s.pierce, hit: new Set() });
    const aoe = (cx, cy, rad, dmg) => {
      for (const t of this.targets(p.team)) if (dist(cx, cy, t.x, t.y) < rad + t.r) this.hurt(t.o, dmg, p);
      this.fx.push([Math.round(cx), Math.round(cy), rad, p.team]);
    };
    switch (s.k) {
      case "shot": shot(a); break;
      case "fan": for (let i = -1; i <= 1; i++) shot(a + i * s.ang); break;
      case "aoeSelf": aoe(p.x, p.y, s.rad, s.dmg); break;
      case "aoeAt": { const d = Math.min(s.rng, dist(p.x, p.y, x, y)); aoe(p.x + Math.cos(a) * d, p.y + Math.sin(a) * d, s.rad, s.dmg); break; }
      case "shield": p.sh = s.amt; p.shT = s.dur; break;
      case "haste": p.haste = s.dur; break;
      case "heal": p.hp = Math.min(p.max, p.hp + s.amt); break;
      case "wall": this.walls.push({ x: p.x + Math.cos(a) * s.rng, y: p.y + Math.sin(a) * s.rng, team: p.team, life: s.dur, rad: s.rad }); break;
    }
  }

  hurt(o, dmg, by) {
    if (o.dead > 0 || o.hp <= 0) return;
    if (o.sh > 0) { const a = Math.min(o.sh, dmg); o.sh -= a; dmg -= a; }
    o.hp -= dmg;
    if (o.hp <= 0) {
      if (o.ws) { o.dead = 5; o.d++; if (by && by.ws) by.k++; }
      if (o.nx && !this.over) { this.over = 8; this.win = o.nx === "b" ? "r" : "b"; }
    }
  }

  // 공격 가능한 적: 챔피언, 미니언, 맨 앞 타워(없으면 넥서스)
  targets(team) {
    const en = team === "b" ? "r" : "b", list = [];
    for (const p of this.socks.values()) if (p.team === en && p.dead <= 0) list.push({ o: p, x: p.x, y: p.y, r: 20 });
    for (const m of this.minions) if (m.team === en && m.hp > 0) list.push({ o: m, x: m.x, y: m.y, r: 14 });
    const tw = this.towers.filter((t) => t.team === en && t.hp > 0).sort((a, b) => a.i - b.i)[0];
    if (tw) list.push({ o: tw, x: tw.x, y: tw.y, r: 36 });
    else list.push({ o: this.nex[en], x: this.nex[en].x, y: this.nex[en].y, r: 60 });
    return list;
  }

  tick() {
    if (this.phase === "lobby") {
      if (++this.tk % 4 === 0) this.bcast({ t: "s", ph: "lobby", host: this.host, p: [...this.socks.values()].map((p) => [p.id, p.team, p.name, p.ch]) });
      return;
    }
    if (this.over) {
      this.over -= DT;
      if (this.over <= 0) { this.phase = "lobby"; this.reset(); }
    } else {
      this.t += DT;
      if (this.t % 20 < DT) {
        for (let i = 0; i < 4; i++) {
          this.minions.push({ team: "b", x: 180 - i * 30, y: H / 2, hp: 120, atk: 0 });
          this.minions.push({ team: "r", x: W - 180 + i * 30, y: H / 2, hp: 120, atk: 0 });
        }
      }
      for (const p of this.socks.values()) {
        const c = CH[p.ch];
        p.cdq = Math.max(0, p.cdq - DT); p.cdw = Math.max(0, p.cdw - DT); p.atk -= DT;
        p.haste = Math.max(0, p.haste - DT);
        if (p.shT > 0) { p.shT -= DT; if (p.shT <= 0) p.sh = 0; }
        if (p.dead > 0) { p.dead -= DT; if (p.dead <= 0) this.spawn(p); continue; }
        if (p.team === "b" ? p.x < 260 : p.x > W - 260) p.hp = Math.min(p.max, p.hp + 40 * DT);
        const d = dist(p.x, p.y, p.tx, p.ty), step = c.spd * (p.haste > 0 ? 1.6 : 1) * DT;
        if (d <= step) { p.x = p.tx; p.y = p.ty; } else { p.x += ((p.tx - p.x) / d) * step; p.y += ((p.ty - p.y) / d) * step; }
        if (p.atk <= 0) {
          let best = null, bd = 1e9;
          for (const t of this.targets(p.team)) {
            const dd = dist(p.x, p.y, t.x, t.y) - t.r;
            if (dd < c.ar && dd < bd) { best = t; bd = dd; }
          }
          if (best) { this.hurt(best.o, c.ad, p); p.atk = 0.8; }
        }
      }
      for (const q of this.relics) {
        q.cd = Math.max(0, q.cd - DT);
        if (q.cd > 0) continue;
        for (const p of this.socks.values()) {
          if (p.dead <= 0 && p.hp < p.max && dist(p.x, p.y, q.x, q.y) < 36) { p.hp = Math.min(p.max, p.hp + 150); q.cd = 30; break; }
        }
      }
      for (const T of this.towers) {
        if (T.hp <= 0) continue;
        T.atk -= DT; T.fl -= DT;
        if (T.atk > 0) continue;
        let best = null, bd = 1e9, mn = false;
        for (const m of this.minions) {
          if (m.team === T.team || m.hp <= 0) continue;
          const d = dist(T.x, T.y, m.x, m.y);
          if (d < 270 && d < bd) { best = m; bd = d; mn = true; }
        }
        if (!best) for (const p of this.socks.values()) {
          if (p.team === T.team || p.dead > 0) continue;
          const d = dist(T.x, T.y, p.x, p.y);
          if (d < 270 && d < bd) { best = p; bd = d; }
        }
        if (best) { this.hurt(best, mn ? 70 : 45, null); T.atk = 1; T.fx = best.x; T.fy = best.y; T.fl = 0.2; }
      }
      for (const m of this.minions) {
        m.atk -= DT;
        let best = null, bd = 1e9;
        for (const t of this.targets(m.team)) {
          const dd = dist(m.x, m.y, t.x, t.y) - t.r;
          if (dd < bd) { best = t; bd = dd; }
        }
        if (best && bd < 50) { if (m.atk <= 0) { this.hurt(best.o, 10, m); m.atk = 1; } }
        else m.x += (m.team === "b" ? 1 : -1) * 70 * DT;
      }
      for (const w of this.walls) w.life -= DT;
      this.walls = this.walls.filter((w) => w.life > 0);
      for (const s of this.shots) {
        s.x += s.vx * DT; s.y += s.vy * DT; s.life -= DT;
        if (this.walls.some((w) => w.team !== s.team && dist(s.x, s.y, w.x, w.y) < w.rad)) { s.life = 0; continue; }
        for (const t of this.targets(s.team)) {
          if (s.hit.has(t.o)) continue;
          if (dist(s.x, s.y, t.x, t.y) < t.r + s.r) {
            this.hurt(t.o, s.dmg, s.by); s.hit.add(t.o);
            if (!s.pierce) { s.life = 0; break; }
          }
        }
      }
      this.shots = this.shots.filter((s) => s.life > 0);
      this.minions = this.minions.filter((m) => m.hp > 0);
    }
    const r = Math.round;
    const msg = JSON.stringify({
      t: "s",
      p: [...this.socks.values()].map((p) => [p.id, p.team, p.name, r(p.x), r(p.y), r(p.hp), p.max, p.dead > 0 ? 1 : 0, p.k, p.d, +p.cdq.toFixed(1), +p.cdw.toFixed(1), p.ch, r(p.sh)]),
      m: this.minions.map((m) => [m.team, r(m.x), r(m.y), r(m.hp)]),
      s: this.shots.map((s) => [r(s.x), r(s.y), s.team]),
      tw: this.towers.map((T) => [T.team, T.x, r(T.hp), T.fl > 0 ? r(T.fx) : 0, T.fl > 0 ? r(T.fy) : 0]),
      wl: this.walls.map((w) => [r(w.x), r(w.y), w.rad, w.team]),
      fx: this.fx,
      n: [this.nex.b.hp, this.nex.r.hp],
      win: this.win, ph: "play", host: this.host, tm: Math.round(this.t),
      rl: this.relics.map((q) => (q.cd <= 0 ? 1 : 0)),
    });
    this.fx = [];
    for (const p of this.socks.values()) { try { p.ws.send(msg); } catch {} }
  }
}
