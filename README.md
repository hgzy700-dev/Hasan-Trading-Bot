<!DOCTYPE html>
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HASAN PRO Smart AI</title>
    <style>
        body { font-family: 'Segoe UI', sans-serif; background: #000; color: #fff; margin: 0; display: flex; align-items: center; justify-content: center; height: 100vh; overflow: hidden; }
        .pro-container { background: rgba(15, 15, 15, 0.98); padding: 30px; border-radius: 30px; text-align: center; width: 95%; max-width: 420px; border: 1px solid #d4af37; box-shadow: 0 0 50px rgba(212, 175, 55, 0.2); position: relative; }
        #clock { position: absolute; top: 15px; right: 25px; font-size: 13px; color: #d4af37; font-weight: bold; }
        .app-title { color: #d4af37; font-size: 32px; font-weight: 900; letter-spacing: 3px; margin: 10px 0; text-shadow: 0 0 10px rgba(212, 175, 55, 0.4); }
        .status-badge { background: #d4af37; color: #000; padding: 4px 15px; border-radius: 20px; font-size: 10px; font-weight: bold; margin-bottom: 20px; display: inline-block; }
        video { width: 100%; border-radius: 20px; border: 1px solid #333; margin-top: 10px; }
        #result { margin-top: 20px; font-size: 15px; background: #111; padding: 15px; border-radius: 15px; border-left: 4px solid #d4af37; text-align: left; min-height: 80px; line-height: 1.5; }
        .ai-wave { height: 3px; width: 100%; background: linear-gradient(90deg, transparent, #d4af37, transparent); position: absolute; bottom: 0; left: 0; animation: wave 2s infinite linear; }
        @keyframes wave { 0% { transform: translateX(-100%); } 100% { transform: translateX(100%); } }
    </style>
</head>
<body onload="startSmartAI()">

    <div class="pro-container">
        <div id="clock">00:00:00</div>
        <h1 class="app-title">HASAN PRO</h1>
        <div class="status-badge">ADVANCED AI VOICE ACTIVE</div>
        
        <div style="position: relative;">
            <video id="video" autoplay playsinline></video>
            <div class="ai-wave"></div>
        </div>
        
        <div id="result">স্মার্ট এআই ইঞ্জিন চালু হচ্ছে... চার্টের দিকে ক্যামেরা স্থির রাখুন।</div>
        <canvas id="canvas" style="display:none;"></canvas>
    </div>

    <script>
        function updateClock() { document.getElementById('clock').innerText = new Date().toLocaleTimeString(); }
        setInterval(updateClock, 1000);

        const video = document.getElementById('video');
        const canvas = document.getElementById('canvas');
        const resultDiv = document.getElementById('result');
        const apiKey = "AIzaSyBGITlkWp0CP3VXki1lM4CA66RhguB83H0"; 

        async function startSmartAI() {
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: "environment" } });
                video.srcObject = stream;
                // AI কথা বলা শুরু করবে শুরুতে
                speak("হ্যালো হাসান ভাই, আপনার স্মার্ট এআই ট্রেডিং অ্যাসিস্ট্যান্ট এখন সক্রিয়। আমি চার্ট পর্যবেক্ষণ করছি।"); 
                setInterval(analyzeChart, 15000); 
            } catch (err) { resultDiv.innerText = "ক্যামেরা পারমিশন দিন।"; }
        }

        async function analyzeChart() {
            if (!video.srcObject) return;
            canvas.width = video.videoWidth; canvas.height = video.videoHeight;
            canvas.getContext('2d').drawImage(video, 0, 0);
            const base64Data = canvas.toDataURL('image/jpeg').split(',')[1];

            try {
                const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:generateContent?key=${apiKey}`, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        contents: [{
                            parts: [{ text: "You are a professional AI trading assistant named Hasan Pro. Analyze this chart. Give a short, smart prediction in Bengali like a human assistant. Example: 'হাসান ভাই, এখন মার্কেট আপট্রেন্ডে আছে, পরবর্তী ক্যান্ডেল উপরে যাওয়ার সম্ভাবনা ৮০ শতাংশ। আপনি কল ট্রেড নিতে পারেন।'" },
                            { inline_data: { mime_type: "image/jpeg", data: base64Data } }]
                        }]
                    })
                });
                const data = await response.json();
                const signal = data.candidates[0].content.parts[0].text;
                resultDiv.innerText = signal;
                speak(signal);
            } catch (e) { console.log("Re-scanning..."); }
        }

        function speak(text) {
            const utterance = new SpeechSynthesisUtterance(text);
            utterance.lang = 'bn-BD';
            utterance.rate = 1.0; // কথার গতি (স্বাভাবিক)
            utterance.pitch = 1.1; // কন্ঠস্বর একটু মিষ্টি এবং এআই এর মতো শোনাবে
            window.speechSynthesis.speak(utterance);
        }
    </script>
</body>
</html>
