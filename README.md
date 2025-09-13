<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <title>Simulateur ABS</title>
  <style>
    body {
      background: #1e1e1e;
      color: #fff;
      font-family: Arial, sans-serif;
      margin: 0;
      text-align: center;
    }
    h1 { color: #00bcd4; margin-top: 10px; }

    #car {
      margin: 20px auto;
      width: 300px;
      height: 500px;
      position: relative;
      background: #333;
      border-radius: 20px;
    }
    .wheel {
      width: 60px;
      height: 60px;
      border-radius: 50%;
      border: 4px solid #888;
      background: #222;
      position: absolute;
      background-image: radial-gradient(#555 30%, #111 70%);
    }
    #wheel-fl { top: 40px; left: 20px; }
    #wheel-fr { top: 40px; right: 20px; }
    #wheel-rl { bottom: 40px; left: 20px; }
    #wheel-rr { bottom: 40px; right: 20px; }

    .brake-light {
      position: absolute;
      bottom: 10px;
      width: 80px;
      height: 20px;
      background: #400;
      border-radius: 5px;
    }
    #light-left { left: 40px; }
    #light-right { right: 40px; }

    .on { background: red !important; box-shadow: 0 0 10px red; }

    .controls {
      margin: 20px auto;
      width: 90%;
      max-width: 800px;
      background: #2c2c2c;
      padding: 15px;
      border-radius: 10px;
    }
    .wheel-controls {
      display: inline-block;
      width: 45%;
      margin: 10px;
      background: #444;
      padding: 10px;
      border-radius: 10px;
    }
    label { display: block; margin-top: 5px; font-size: 14px; }
    input, select, button {
      margin-top: 3px;
      width: 90%;
      padding: 5px;
      border-radius: 5px;
      border: none;
    }
    button {
      background: #00bcd4;
      color: white;
      font-weight: bold;
      cursor: pointer;
    }
    button:hover { background: #0097a7; }
    .pedal {
      margin-top: 20px;
      font-size: 20px;
      padding: 15px 30px;
      border-radius: 50%;
      background: #d32f2f;
      border: none;
      color: white;
    }
    .pedal.active { background: #f44336; box-shadow: 0 0 15px red; }
  </style>
</head>
<body>
  <h1>Simulateur ABS - Prototype</h1>
  <div id="car">
    <div id="wheel-fl" class="wheel"></div>
    <div id="wheel-fr" class="wheel"></div>
    <div id="wheel-rl" class="wheel"></div>
    <div id="wheel-rr" class="wheel"></div>
    <div id="light-left" class="brake-light"></div>
    <div id="light-right" class="brake-light"></div>
  </div>

  <div class="controls">
    <h2>Contrôle des roues</h2>
    <div class="wheel-controls" data-wheel="fl">
      <h3>Roue Avant Gauche</h3>
      <label>Vitesse (km/h): <input type="number" value="50" min="0" max="200"></label>
      <label>Type capteur: 
        <select>
          <option>VR (inductif)</option>
          <option>Hall actif</option>
        </select>
      </label>
      <label>Amplitude (V): <input type="number" value="5" min="0" max="12"></label>
      <label>Fréquence (Hz): <input type="number" value="100" min="0" max="5000"></label>
      <button class="lock">Lock</button>
      <button class="unlock">Unlock</button>
    </div>

    <div class="wheel-controls" data-wheel="fr">
      <h3>Roue Avant Droite</h3>
      <label>Vitesse (km/h): <input type="number" value="50"></label>
      <label>Type capteur: 
        <select>
          <option>VR (inductif)</option>
          <option>Hall actif</option>
        </select>
      </label>
      <label>Amplitude (V): <input type="number" value="5"></label>
      <label>Fréquence (Hz): <input type="number" value="100"></label>
      <button class="lock">Lock</button>
      <button class="unlock">Unlock</button>
    </div>

    <div class="wheel-controls" data-wheel="rl">
      <h3>Roue Arrière Gauche</h3>
      <label>Vitesse (km/h): <input type="number" value="50"></label>
      <label>Type capteur: 
        <select>
          <option>VR (inductif)</option>
          <option>Hall actif</option>
        </select>
      </label>
      <label>Amplitude (V): <input type="number" value="5"></label>
      <label>Fréquence (Hz): <input type="number" value="100"></label>
      <button class="lock">Lock</button>
      <button class="unlock">Unlock</button>
    </div>

    <div class="wheel-controls" data-wheel="rr">
      <h3>Roue Arrière Droite</h3>
      <label>Vitesse (km/h): <input type="number" value="50"></label>
      <label>Type capteur: 
        <select>
          <option>VR (inductif)</option>
          <option>Hall actif</option>
        </select>
      </label>
      <label>Amplitude (V): <input type="number" value="5"></label>
      <label>Fréquence (Hz): <input type="number" value="100"></label>
      <button class="lock">Lock</button>
      <button class="unlock">Unlock</button>
    </div>

    <button id="brake" class="pedal">Frein</button>
  </div>

  <script>
    const wheels = {
      fl: {el: document.getElementById("wheel-fl"), speed:50, locked:false},
      fr: {el: document.getElementById("wheel-fr"), speed:50, locked:false},
      rl: {el: document.getElementById("wheel-rl"), speed:50, locked:false},
      rr: {el: document.getElementById("wheel-rr"), speed:50, locked:false}
    };

    // Animation
    let angle = {fl:0, fr:0, rl:0, rr:0};
    function animate() {
      for (let w in wheels) {
        if (!wheels[w].locked) {
          angle[w] += wheels[w].speed * 0.1;
          wheels[w].el.style.transform = "rotate(" + angle[w] + "deg)";
        }
      }
      requestAnimationFrame(animate);
    }
    animate();

    // Contrôles
    document.querySelectorAll(".wheel-controls").forEach(block => {
      const wheelName = block.dataset.wheel;
      const speedInput = block.querySelector("input[type=number]");
      const lockBtn = block.querySelector(".lock");
      const unlockBtn = block.querySelector(".unlock");
      speedInput.addEventListener("input", e => {
        wheels[wheelName].speed = parseFloat(e.target.value);
      });
      lockBtn.addEventListener("click", () => wheels[wheelName].locked = true);
      unlockBtn.addEventListener("click", () => wheels[wheelName].locked = false);
    });

    // Pédale de frein
    const brakeBtn = document.getElementById("brake");
    const lights = [document.getElementById("light-left"), document.getElementById("light-right")];
    brakeBtn.addEventListener("click", () => {
      brakeBtn.classList.toggle("active");
      const active = brakeBtn.classList.contains("active");
      lights.forEach(l => active ? l.classList.add("on") : l.classList.remove("on"));
    });
  </script>
</body>
</html>
