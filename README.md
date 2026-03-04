<!DOCTYPE html>
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>IKTIDAR Premium AI Trader</title>
    <style>
        body { font-family: 'Poppins', sans-serif; background: #000; color: #fff; margin: 0; display: flex; align-items: center; justify-content: center; height: 100vh; overflow: hidden; }
        .premium-card { background: linear-gradient(145deg, #1a1a1a, #000); padding: 25px; border-radius: 30px; text-align: center; width: 95%; max-width: 450px; border: 2px solid #d4af37; box-shadow: 0 0 30px rgba(212, 175, 55, 0.3); position: relative; }
        #clock { position: absolute; top: 15px; right: 25px; font-size: 14px; color: #d4af37; font-weight: bold; }
        .logo-box img { width: 100px; filter: drop-shadow(0 0 10px #d4af37); margin-bottom: 10px; }
        .status-badge { background: #d4af37; color: #000; padding: 4px 12px; border-radius: 20px; font-size: 12px; font-weight: bold; text-transform: uppercase; margin-bottom: 15px; display: inline-block; }
        select { width: 48%; padding: 12px; margin: 10px 1%; border-radius: 12px; border: 1px solid #d4af37; background: #111; color: #d4af37; font-weight: bold; }
        video { width: 100%; border-radius: 20px; border: 1px solid #d4af37; margin-top: 10px; box-shadow: 0 0 15px rgba(212, 175, 55, 0.2); }
        #result { margin-top: 20px; font-size: 16px; font-weight: 500; color: #fff; background: rgba(212, 175, 55, 0.1); padding: 15px; border-radius: 15px; border-left: 5px solid #d4af37; min-height: 80px; text-shadow: 1px 1px 2px #000; }
        .scan-line { width: 100%; height: 2px; background: linear-gradient(90deg, transparent, #d4af37, transparent); position: absolute; top: 50%; left: 0; animation: scan 2.5s infinite; }
        @keyframes scan { 0% { top: 20%; } 100% { top: 85%; } }
    </style>
</head>
<body onload="startPremiumApp()">

    <div class="premium-card">
        <div id="clock">00:00:00</div>
        
        <div class="logo-box">
            <img src="1000116267.png" alt="IKTIDAR">
        </div>
        
        <div class="status-badge">Premium License Active</div>
        
        <div style="display: flex; justify-content: space-between;">
            <select id="timeframe">
                <option value="1 minute">1 MIN PRO</option>
                <option value="5 minutes">5 MIN PRO</option>
            </select>
            <select id="voiceType">
                <option value="female">FEMALE VOICE</option>
                <option value="male">MALE VOICE</option>
            </select>
        </div>

        <div style="position: relative;">
            <video id="video" autoplay playsinline></video>
            <div class="scan-line"></div>
        </div>
        
        <div id="result">Initializing Premium AI Engine...</div>
        <canvas id="canvas" style="display:none;"></canvas>
    </div>

    <script>
        function updateClock() { document.getElementById('clock').innerText = new Date().toLocaleTimeString(); }
        setInterval(updateClock, 1000);

        const video = document.getElementById('video');
        const canvas = document.getElementById('canvas');
        const resultDiv = document.getElementById('result');
        const apiKey = "AIzaSyBGITlkWp0CP3VXki1lM4CA66RhguB83H0"; 

        async function startPremiumApp() {
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: "environment" } });
                video.srcObject = stream;
                setInterval(analyzeMarket, 12000);
            } catch (err) {
                resultDiv.innerText = "Please allow camera access for Premium features.";
            }
        }

        async function analyzeMarket() {
            if (!video.srcObject) return;
            const tf = document.getElementById('timeframe').value;
            canvas.width = video.videoWidth; canvas.height = video.videoHeight;
            canvas.getContext('2d').drawImage(video, 0, 0);
            const base64Data = canvas.toDataURL('image/jpeg').split(',')[1];

            try {
                const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:generateContent?key=${apiKey}`, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        contents: [{
                            parts: [{ text: `You are a Senior Trading Expert. Analyze this chart for ${tf}. Identify Trend, Volatility, and predict Next Candle. Output in Bengali: 1. Signal (UP/DOWN), 2. Confidence %, 3. Martingale step. Keep it highly professional.` },
                            { inline_data: { mime_type: "image/jpeg", data: base64Data } }]
                        }]
                    })
                });
                const data = await response.json();
                const signal = data.candidates[0].content.parts[0].text;
                resultDiv.innerText = signal;
                speakSignal(signal);
            } catch (e) { resultDiv.innerText = "Market data unavailable. Re-scanning..."; }
        }

        function speakSignal(text) {
            const utterance = new SpeechSynthesisUtterance(text);
            const voiceType = document.getElementById('voiceType').value;
            utterance.pitch = (voiceType === 'male') ? 0.8 : 1.2;
            utterance.lang = 'bn-BD';
            window.speechSynthesis.speak(utterance);
        }
    </script>
</body>
</html>
