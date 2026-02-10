<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>¿Melanny quieres ser mi San Valentín?</title>

  <style>
    :root{
      --bg1:#ff3b7a;
      --bg2:#7c3aed;
      --card:#ffffffcc;
      --text:#1f2937;
      --titleRed:#ff2d55;
      --ecg: rgba(0, 255, 160, 0.32); /* color del electro */
      --ecgGlow: rgba(0, 255, 160, 0.18);
    }

    *{box-sizing:border-box}

    body{
      margin:0;
      min-height:100vh;
      display:flex;
      align-items:center;
      justify-content:center;
      font-family: system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif;
      color:var(--text);
      background:
        radial-gradient(1200px 800px at 20% 20%, #ffd1e3 0%, transparent 55%),
        radial-gradient(1200px 800px at 80% 30%, #d9c7ff 0%, transparent 55%),
        linear-gradient(135deg, var(--bg1), var(--bg2));
      overflow:hidden;
    }

    /* Canvas ECG al fondo */
    #ecgCanvas{
      position:fixed;
      inset:0;
      width:100%;
      height:100%;
      z-index:0;
      pointer-events:none;
      mix-blend-mode: screen;
      opacity:.9;
    }

    .card{
      width:min(92vw, 560px);
      background:var(--card);
      backdrop-filter: blur(10px);
      border:1px solid #ffffff80;
      border-radius:24px;
      padding:28px 22px;
      box-shadow: 0 20px 60px rgba(0,0,0,.25);
      text-align:center;
      position:relative;
      z-index:2;
    }

    h1{
      margin:6px 0 10px;
      font-size: clamp(26px, 4vw, 36px);
      letter-spacing:.2px;
    }

    p{
      margin:0 0 18px;
      font-size: 16px;
      line-height:1.4;
      opacity:.92;
    }

    .sub{
      margin: 0 0 14px;
      font-size: 14px;
      opacity: .88;
    }

    /* ✅ Rojo para el título "Feliz San Valentín, amor." */
    .titulo-rojo{
      color: var(--titleRed);
      text-shadow: 0 0 12px rgba(255,45,85,.35);
    }

    .names{
      font-weight:700;
      color:#111827;
    }

    .btns{
      margin-top:18px;
      display:flex;
      gap:12px;
      justify-content:center;
      flex-wrap:wrap;
      position:relative;
      min-height:60px;
    }

    button{
      border:none;
      border-radius:14px;
      padding:14px 18px;
      font-size:16px;
      font-weight:700;
      cursor:pointer;
      transition: transform .12s ease, filter .12s ease;
      box-shadow: 0 10px 22px rgba(0,0,0,.18);
      user-select:none;
    }
    button:active{ transform: scale(.98); }

    .yes{ background: linear-gradient(135deg, #22c55e, #16a34a); color:white; }
    .no{ background: linear-gradient(135deg, #fb7185, #ef4444); color:white; position:relative; }

    .footer{
      margin-top:16px;
      font-size:12px;
      opacity:.75;
    }

    .badge{
      display:inline-flex;
      gap:8px;
      align-items:center;
      padding:8px 12px;
      border-radius:999px;
      background:#ffffffb0;
      border:1px solid #ffffff80;
      font-size:12px;
      margin-bottom:12px;
    }
    .badge span{
      width:10px;height:10px;border-radius:999px;
      background: #ff3b7a;
      box-shadow: 0 0 14px #ff3b7a;
    }

    /* ✅ Corazones reales (SVG), flotando */
    .heart{
      position:fixed;
      left:0;
      bottom:-40px;
      width:28px;
      height:28px;
      pointer-events:none;
      z-index:1; /* por encima del ECG, por debajo de la card */
      filter: drop-shadow(0 10px 14px rgba(0,0,0,.20));
      animation: floatUp linear forwards;
      opacity:.95;
    }
    @keyframes floatUp{
      from { transform: translateY(0) translateX(0) scale(1); opacity: .95; }
      to   { transform: translateY(-115vh) translateX(var(--drift, 0px)) scale(1.05); opacity: 0; }
    }

    .modal{
      position:fixed; inset:0;
      display:none;
      align-items:center;
      justify-content:center;
      background: rgba(0,0,0,.45);
      padding:18px;
      z-index:3;
    }
    .modal.open{ display:flex; }
    .modal-box{
      width:min(92vw, 520px);
      background:white;
      border-radius:20px;
      padding:22px;
      text-align:center;
      box-shadow:0 30px 80px rgba(0,0,0,.35);
    }
    .big{
      font-size:44px;
      margin:6px 0 12px;
    }
    .close{
      margin-top:14px;
      background:#111827;
      color:white;
    }
  </style>
</head>

<body>
  <!-- Fondo ECG -->
  <canvas id="ecgCanvas"></canvas>

  <div class="card" id="card">
    <div class="badge"><span></span> Invitación especial</div>

    <div class="sub">
      <strong class="titulo-rojo">Feliz San Valentín, amor.</strong><br>
      Un detallito de tu novio, con mucho amor. Porque siempre pienso en cómo hacerte sentir especial.
    </div>

    <h1>¿Quieres ser mi San Valentín? 💘</h1>

    <p>
      <span class="names">Chris</span> ➜ <span class="names">Mi muñeca hermosa</span><br>
      Prometo risas, detallitos y el mejor San Valentín de tu vida.
    </p>

    <div class="btns" id="btns">
      <button class="yes" id="yesBtn">Sí 💚</button>
      <button class="no" id="noBtn">Obvio sí 🙃</button>
    </div>

    <div class="footer">Nota: de todas maneras no tienes opción… ya eres mía 😌</div>
  </div>

  <div class="modal" id="modal" aria-hidden="true">
    <div class="modal-box" role="dialog" aria-modal="true" aria-label="Confirmación">
      <div class="big">🥹💖</div>
      <h2>¡Sabía que dirías que sí!</h2>
      <p>
        Ahora solo falta escoger: ¿cena, helado o ambos? 😄<br>
        Pero que la pasamos genial, eso seguro.
      </p>
      <button class="close" id="closeBtn">Cerrar</button>
    </div>
  </div>

  <script>
    const noBtn = document.getElementById("noBtn");
    const yesBtn = document.getElementById("yesBtn");
    const btns = document.getElementById("btns");
    const modal = document.getElementById("modal");
    const closeBtn = document.getElementById("closeBtn");

    // Botón que se escapa
    function moveNo() {
      const rect = btns.getBoundingClientRect();
      const b = noBtn.getBoundingClientRect();

      const maxX = rect.width - b.width;
      const maxY = rect.height - b.height;

      const x = Math.max(0, Math.min(maxX, Math.random() * maxX));
      const y = Math.max(0, Math.min(maxY, Math.random() * maxY));

      noBtn.style.position = "absolute";
      noBtn.style.left = `${x}px`;
      noBtn.style.top = `${y}px`;
    }

    noBtn.addEventListener("mouseenter", moveNo);
    noBtn.addEventListener("click", moveNo);

    yesBtn.addEventListener("click", () => {
      modal.classList.add("open");
      modal.setAttribute("aria-hidden", "false");
      confettiHearts(50);
    });

    closeBtn.addEventListener("click", () => {
      modal.classList.remove("open");
      modal.setAttribute("aria-hidden", "true");
    });

    modal.addEventListener("click", (e) => {
      if (e.target === modal) {
        modal.classList.remove("open");
        modal.setAttribute("aria-hidden", "true");
      }
    });

    // ✅ Corazones SVG reales
    function spawnHeart() {
      const heart = document.createElement("div");
      heart.className = "heart";

      const size = 18 + Math.random() * 30;
      heart.style.width = `${size}px`;
      heart.style.height = `${size}px`;

      const left = Math.random() * 100;
      heart.style.left = `${left}vw`;

      const duration = 4 + Math.random() * 4;
      heart.style.animationDuration = `${duration}s`;

      // Un poquito de “drift” lateral para que no suban rectos
      const drift = (Math.random() * 80 - 40).toFixed(0);
      heart.style.setProperty("--drift", `${drift}px`);

      const colors = ["#ff2d55", "#ff6b9a", "#ffd1e3", "#c084fc", "#fb7185"];
      const color = colors[Math.floor(Math.random() * colors.length)];

      // SVG corazón real
      heart.innerHTML = `
        <svg viewBox="0 0 24 24" width="100%" height="100%" aria-hidden="true">
          <path fill="${color}" d="M12 21s-7.2-4.6-9.6-8.7C.6 9.2 2.2 5.9 5.6 5.3c1.8-.3 3.5.4 4.6 1.7 1.1-1.3 2.8-2 4.6-1.7 3.4.6 5 3.9 3.2 7.0C19.2 16.4 12 21 12 21z"/>
        </svg>
      `;

      document.body.appendChild(heart);
      setTimeout(() => heart.remove(), duration * 1000);
    }

    setInterval(spawnHeart, 380);

    function confettiHearts(n) {
      for (let i = 0; i < n; i++) setTimeout(spawnHeart, i * 35);
    }

    // ✅ Fondo ECG animado (canvas)
    const canvas = document.getElementById("ecgCanvas");
    const ctx = canvas.getContext("2d");

    function resize() {
      const dpr = Math.max(1, window.devicePixelRatio || 1);
      canvas.width = Math.floor(window.innerWidth * dpr);
      canvas.height = Math.floor(window.innerHeight * dpr);
      canvas.style.width = "100%";
      canvas.style.height = "100%";
      ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
    }
    window.addEventListener("resize", resize);
    resize();

    // ECG: patrón repetido con pico QRS
    function ecgWave(phase) {
      // phase en [0,1)
      // baseline = 0, devolvemos valores pequeños + un pico marcado
      let y = 0;

      // ondulación suave
      y += 0.02 * Math.sin(phase * Math.PI * 2);

      // Pico QRS alrededor del 0.12
      const p = phase;
      if (p > 0.10 && p < 0.16) {
        // pico súper agudo: sube rápido y baja rápido
        const t = (p - 0.10) / 0.06; // 0..1
        // forma triangular con “punta”
        y += (t < 0.25) ? (t / 0.25) * 1.2
           : (t < 0.5)  ? (1 - (t - 0.25)/0.25) * 1.2
           : (t < 0.75) ? -((t - 0.5)/0.25) * 0.55
           : -((1 - (t - 0.75)/0.25) * 0.55);
      }

      // pequeña onda T después
      if (p > 0.22 && p < 0.38) {
        const t = (p - 0.22) / 0.16;
        y += 0.18 * Math.sin(t * Math.PI);
      }

      return y;
    }

    let t0 = performance.now();
    let scroll = 0;

    function drawECG(now) {
      const w = window.innerWidth;
      const h = window.innerHeight;

      // velocidad de “barrido”
      const dt = (now - t0) / 1000;
      t0 = now;
      scroll += dt * 140; // px/seg (ajusta aquí)

      // fade sutil para “trazo” continuo
      ctx.fillStyle = "rgba(0,0,0,0.06)";
      ctx.fillRect(0, 0, w, h);

      // Línea ECG
      const baseline = h * 0.62;
      const amp = Math.min(90, h * 0.12);
      const periodPx = 260; // ancho de un latido en px
      const phaseSpeed = 1 / periodPx; // fase por px

      ctx.lineWidth = 2;
      ctx.strokeStyle = getComputedStyle(document.documentElement).getPropertyValue("--ecg").trim();
      ctx.shadowColor = getComputedStyle(document.documentElement).getPropertyValue("--ecgGlow").trim();
      ctx.shadowBlur = 14;

      ctx.beginPath();
      for (let x = 0; x <= w; x++) {
        const phase = ((x + scroll) * phaseSpeed) % 1;
        const y = baseline - ecgWave(phase) * amp;
        if (x === 0) ctx.moveTo(x, y);
        else ctx.lineTo(x, y);
      }
      ctx.stroke();

      // reset sombras para no afectar otros dibujos
      ctx.shadowBlur = 0;

      requestAnimationFrame(drawECG);
    }

    // Inicializa el canvas con negro “monitor” encima del fondo rosa
    ctx.fillStyle = "rgba(0,0,0,0.22)";
    ctx.fillRect(0, 0, window.innerWidth, window.innerHeight);
    requestAnimationFrame(drawECG);
  </script>
</body>
</html>
