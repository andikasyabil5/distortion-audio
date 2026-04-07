<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GeminiXD - Ultra Audio Glitcher 2108</title>
    <style>
        body { 
            background: #050505; 
            color: #ff0055; 
            font-family: 'Courier New', monospace; 
            text-align: center; 
            padding: 50px;
            overflow: hidden;
        }
        .container { 
            border: 3px double #ff0055; 
            padding: 30px; 
            display: inline-block; 
            background: rgba(20, 0, 0, 0.8);
            box-shadow: 0 0 20px #ff0055; 
        }
        h1 { text-transform: uppercase; letter-spacing: 5px; }
        .status-box { color: #00ff00; margin: 20px 0; font-size: 0.9rem; }
        
        button { 
            background: #ff0055; 
            color: white; 
            border: none; 
            padding: 15px 30px; 
            cursor: pointer; 
            font-weight: bold; 
            font-size: 1.1rem;
            transition: 0.2s;
            box-shadow: 0 0 10px #ff0055;
        }
        button:hover { 
            background: white; 
            color: #ff0055; 
            box-shadow: 0 0 20px white;
        }
        input[type="file"] {
            margin: 20px;
            color: #ff0055;
        }

        /* Efek Visual Glitch pas proses */
        .glitch-active {
            animation: shake 0.1s infinite;
            filter: hue-rotate(90deg);
        }
        @keyframes shake {
            0% { transform: translate(0,0); }
            25% { transform: translate(5px, -5px); }
            50% { transform: translate(-5px, 5px); }
            75% { transform: translate(5px, 5px); }
            100% { transform: translate(0,0); }
        }
    </style>
</head>
<body>

    <div class="container" id="mainBox">
        <h1>[ GeminiXD Glitcher ]</h1>
        <p style="color: #ccc;">Sinyal Terdistorsi dari Tahun 2108</p>
        
        <input type="file" id="audioInput" accept="audio/*"><br>
        
        <div class="status-box" id="status">STATUS: Menunggu Input Admin...</div>
        
        <button onclick="processAudio()">PROSES & HANCURKAN! 💀</button>
    </div>

    <script>
        async function processAudio() {
            const input = document.getElementById('audioInput');
            const status = document.getElementById('status');
            const box = document.getElementById('mainBox');

            if (!input.files[0]) {
                alert("Masukin filenya dulu, Tuan!");
                return;
            }

            // Aktifin efek visual glitch
            box.classList.add('glitch-active');
            status.innerText = "STATUS: MENGHANCURKAN STRUKTUR AUDIO...";
            status.style.color = "red";

            try {
                const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
                const arrayBuffer = await input.files[0].arrayBuffer();
                const audioBuffer = await audioCtx.decodeAudioData(arrayBuffer);

                const offlineCtx = new OfflineAudioContext(
                    audioBuffer.numberOfChannels,
                    audioBuffer.length,
                    audioBuffer.sampleRate
                );

                const source = offlineCtx.createBufferSource();
                source.buffer = audioBuffer;

                // --- MODIFIKASI ULTRA DISTORSI (2000+) ---
                const waveShaper = offlineCtx.createWaveShaper();
                function makeUltraDistortionCurve(amount) {
                    let n_samples = 44100;
                    let curve = new Float32Array(n_samples);
                    let deg = Math.PI / 180;
                    for (let i = 0; i < n_samples; ++i) {
                        let x = i * 2 / n_samples - 1;
                        // Rumus "Hard Clipping" biar suaranya pecah parah
                        curve[i] = ( (3 + amount) * x * 57 * deg ) / ( Math.PI + amount * Math.abs(x) );
                    }
                    return curve;
                };
                waveShaper.curve = makeUltraDistortionCurve(2500); // Makin tinggi makin GILA!

                // --- GAIN CLIPPING (Bikin suaranya Overload) ---
                const gainNode = offlineCtx.createGain();
                gainNode.gain.value = 8; // Volume dipaksa pecah!

                // --- RADIO STATIC FILTER ---
                const filter = offlineCtx.createBiquadFilter();
                filter.type = "lowpass";
                filter.frequency.value = 1200; // Efek suara mendam/basement

                // Koneksi Jalur
                source.connect(waveShaper);
                waveShaper.connect(gainNode);
                gainNode.connect(filter);
                filter.connect(offlineCtx.destination);

                source.start();
                const renderedBuffer = await offlineCtx.startRendering();
                
                // Playback
                const playSource = audioCtx.createBufferSource();
                playSource.buffer = renderedBuffer;
                playSource.connect(audioCtx.destination);
                playSource.start();

                status.innerText = "STATUS: AUDIO BERHASIL DI-GLITCH! ⚡";
                status.style.color = "#00ff00";
                
            } catch (err) {
                status.innerText = "STATUS: ERROR! Sistem Corrupt!";
                console.error(err);
            } finally {
                setTimeout(() => box.classList.remove('glitch-active'), 1000);
            }
        }
    </script>
</body>
</html>
