# rutmart126-pixel.github.io
<!-- Winke-Widget: Einfach einfügen -->
<section id="winking-hero" style="display:flex;justify-content:center;padding:2rem;">
  <!-- Wrapper für responsive Skalierung -->
  <div class="wink-wrapper" style="max-width:220px;width:30vw;min-width:120px;">
    <!-- SVG Figur -->
    <svg viewBox="0 0 200 300" preserveAspectRatio="xMidYMid meet" class="winker-svg" aria-label="Winkende Figur">
      <!-- Beine / Körper -->
      <g id="body">
        <rect x="88" y="130" width="24" height="70" rx="6" fill="#1a2a6c" />
        <rect x="75" y="200" width="50" height="12" rx="6" fill="#1a2a6c" />
      </g>

      <!-- Kopf -->
      <g id="head">
        <circle cx="100" cy="85" r="36" fill="#ffd86b" stroke="#000" stroke-width="2"/>
        <!-- Auge (geschlossen als leichter Bogen) -->
        <path d="M85 78 q7 6 14 0" fill="none" stroke="#000" stroke-width="3" stroke-linecap="round"/>
        <!-- Mund -->
        <path d="M88 98 q12 10 24 0" fill="none" stroke="#6b3b00" stroke-width="3" stroke-linecap="round"/>
      </g>

      <!-- Linker Arm (ruhend) -->
      <g id="arm-left" transform="translate(62,140)">
        <rect x="-6" y="0" width="12" height="54" rx="6" fill="#ffd86b" />
      </g>

      <!-- Rechter Arm (winkt) — pivot bei Schulter -->
      <g id="arm-right" transform="translate(138,120)">
        <!-- Oberarm (wird rotiert) -->
        <g id="right-rotate" style="transform-origin: 0 12px;">
          <rect x="0" y="0" width="12" height="54" rx="6" fill="#ffd86b" />
          <!-- Hand -->
          <ellipse cx="6" cy="56" rx="10" ry="7" fill="#ffd86b" />
        </g>
      </g>

      <!-- Shirt (über Kopf und Armen leicht drüber) -->
      <g id="shirt">
        <path d="M60 130 q40 -20 80 0 v20 q-40 20 -80 0 z" fill="#b21f1f" stroke="#000" stroke-width="1"/>
      </g>
    </svg>
  </div>
</section>

<style>
/* --- Animationen --- */
.winker-svg { display:block; width:100%; height:auto; }

/* Keyframes: Winken (rotieren am Schulter-Pivot) */
@keyframes wave-rotate {
  0%   { transform: rotate(0deg); }
  20%  { transform: rotate(-35deg); }  /* Arm hoch */
  40%  { transform: rotate(-10deg); }  /* zurück */
  60%  { transform: rotate(-30deg); }  /* kleiner Winken */
  80%  { transform: rotate(-12deg); }
  100% { transform: rotate(0deg); }
}

/* Hand-Tilt für mehr Lebendigkeit */
@keyframes hand-tilt {
  0%   { transform: rotate(0deg) translateY(0); }
  50%  { transform: rotate(12deg) translateY(-2px); }
  100% { transform: rotate(0deg) translateY(0); }
}

/* Anwenden: auf das rotierende Gruppen-Element */
#wink-wrapper-active #right-rotate,
#right-rotate {
  /* Basis: transform-origin in CSS fallback (SVG inline transform-origin wird via style gesetzt) */
  transform-origin: 0 12px;
  animation: wave-rotate 2.2s ease-in-out infinite;
  /* animation-duration kann via JS geändert werden */
}

/* Hand leicht separat animieren (kann kombiniert werden) */
#right-rotate ellipse {
  transform-origin: 6px 56px;
  animation: hand-tilt 0.9s ease-in-out infinite;
}

/* Optional: reduzierte Animation bei geringer Bildschirmbreite */
@media (max-width:480px) {
  @keyframes wave-rotate { /* sanfter auf Mobil */
    0% { transform: rotate(0deg); }
    40% { transform: rotate(-28deg); }
    60% { transform: rotate(-10deg); }
    100% { transform: rotate(0deg); }
  }
}
</style>

<script>
/* --- Optional: JS um Geschwindigkeit & Aktivierung zu kontrollieren --- */
/* Beispiel: führe die Animation nur aus, wenn das Element im Viewport ist (performanter) */
(function(){
  const svg = document.querySelector('.winker-svg');
  if(!svg) return;

  const rightRotate = document.getElementById('right-rotate');

  // Standarddauer (in Sekunden)
  let duration = 2.2;

  // Wenn du die Geschwindigkeit per data-attr setzen willst:
  // <div class="wink-wrapper" data-speed="1.5"> -> schneller: kleinere Zahl = schneller
  const wrapper = svg.closest('.wink-wrapper');
  if(wrapper && wrapper.dataset && wrapper.dataset.speed) {
    const sp = parseFloat(wrapper.dataset.speed);
    if(!isNaN(sp) && sp > 0) {
      duration = sp;
    }
  }

  // Apply duration (CSS animation-duration)
  rightRotate.style.animationDuration = duration + 's';
  // Handanimation etwas schneller/unterteilt
  const hand = rightRotate.querySelector('ellipse');
  if(hand) hand.style.animationDuration = Math.max(0.8, duration / 2.5) + 's';

  // IntersectionObserver: startet Animation erst wenn sichtbar (reduziert CPU/Nutzerablenkung)
  const io = new IntersectionObserver(entries => {
    entries.forEach(entry => {
      if(entry.isIntersecting) {
        rightRotate.style.animationPlayState = 'running';
        if(hand) hand.style.animationPlayState = 'running';
      } else {
        rightRotate.style.animationPlayState = 'paused';
        if(hand) hand.style.animationPlayState = 'paused';
      }
    });
  }, {threshold: 0.25});

  io.observe(svg);
})();
</script>
