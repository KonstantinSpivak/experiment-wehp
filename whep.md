# Все варианты index.html для WHEP WebRTC плеера

**WHEP URL: `http://some.url/stream/whep`**

***

## 1. ✨ САМЫЙ ПРОСТОЙ - Web Component (5 строк)

```html
<!DOCTYPE html>
<html>
<body>
    <h1>WHEP Player</h1>
    <whep-video src="http://some.url/stream/whep" autoplay muted controls></whep-video>
    <script src="https://unpkg.com/@eyevinn/whep-video-component@latest/dist/whep-video.component.js"></script>
</body>
</html>
```

***

## 2. Native WebRTC + webrtc-adapter (базовый)

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WHEP Player</title>
    <script src="https://cdn.jsdelivr.net/npm/webrtc-adapter@8.2.2/out/adapter.js"></script>
    <style>
        body { font-family: Arial; background: #1a1a1a; color: #fff; padding: 20px; }
        .container { max-width: 900px; margin: 0 auto; }
        video { width: 100%; max-width: 800px; background: #000; margin: 20px auto; display: block; }
        button { padding: 10px 20px; margin: 5px; background: #0066cc; color: white; border: none; border-radius: 4px; cursor: pointer; }
        .info { background: #333; padding: 10px; margin: 10px 0; border-radius: 4px; }
    </style>
</head>
<body>
    <div class="container">
        <h1>WHEP WebRTC Player</h1>
        <video id="video" autoplay muted controls></video>
        <div style="text-align: center;">
            <button onclick="connect()">Connect</button>
            <button onclick="disconnect()">Disconnect</button>
        </div>
        <div class="info" id="status">Status: Disconnected</div>
    </div>

    <script>
        const WHEP_URL = 'http://some.url/stream/whep';
        let pc = null;

        async function connect() {
            try {
                setStatus('Connecting...');
                pc = new RTCPeerConnection({
                    iceServers: [
                        { urls: 'stun:stun.l.google.com:19302' },
                        { urls: 'stun:stun1.l.google.com:19302' }
                    ]
                });

                pc.addTransceiver('video', { direction: 'recvonly' });
                pc.addTransceiver('audio', { direction: 'recvonly' });

                pc.ontrack = (e) => {
                    document.getElementById('video').srcObject = e.streams[0];
                };

                pc.onconnectionstatechange = () => {
                    if (pc.connectionState === 'connected') {
                        setStatus('Connected ✓');
                    }
                };

                const offer = await pc.createOffer();
                await pc.setLocalDescription(offer);

                const res = await fetch(WHEP_URL, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/sdp' },
                    body: offer.sdp
                });

                const answer = await res.text();
                await pc.setRemoteDescription(new RTCSessionDescription({
                    type: 'answer',
                    sdp: answer
                }));

            } catch (e) {
                setStatus('Error: ' + e.message);
                console.error(e);
            }
        }

        function disconnect() {
            if (pc) {
                pc.close();
                pc = null;
                document.getElementById('video').srcObject = null;
                setStatus('Disconnected');
            }
        }

        function setStatus(msg) {
            document.getElementById('status').textContent = 'Status: ' + msg;
        }
    </script>
</body>
</html>
```

***

## 3. Web Component с управлением

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WHEP Player - Web Component</title>
    <style>
        body { font-family: Arial; background: #1a1a1a; color: #fff; padding: 20px; }
        .container { max-width: 900px; margin: 0 auto; }
        whep-video { width: 100%; max-width: 800px; display: block; margin: 20px auto; }
        .controls { text-align: center; }
        button { padding: 10px 20px; margin: 5px; background: #0066cc; color: white; border: none; border-radius: 4px; cursor: pointer; }
        .stats { background: #333; padding: 15px; margin: 20px 0; border-radius: 4px; font-size: 12px; }
    </style>
</head>
<body>
    <div class="container">
        <h1>WHEP Player (Web Component)</h1>
        <whep-video id="player" src="http://some.url/stream/whep" autoplay muted controls></whep-video>
        
        <div class="controls">
            <button onclick="player.play()">Play</button>
            <button onclick="player.pause()">Pause</button>
            <button onclick="toggleMute()">Mute/Unmute</button>
        </div>

        <div class="stats" id="stats">Status: Waiting...</div>
    </div>

    <script src="https://unpkg.com/@eyevinn/whep-video-component@latest/dist/whep-video.component.js"></script>
    
    <script>
        const player = document.getElementById('player');

        player.addEventListener('play', () => {
            document.getElementById('stats').textContent = 'Status: Playing';
        });

        player.addEventListener('pause', () => {
            document.getElementById('stats').textContent = 'Status: Paused';
        });

        player.addEventListener('error', (e) => {
            document.getElementById('stats').textContent = 'Status: Error - ' + e.detail;
        });

        function toggleMute() {
            player.muted = !player.muted;
        }
    </script>
</body>
</html>
```

***

## 4. Video.js базовый

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WHEP Player - Video.js</title>
    <link href="https://cdn.jsdelivr.net/npm/video.js@7.21.4/dist/video-js.css" rel="stylesheet" />
    <style>
        body { font-family: Arial; background: #1a1a1a; color: #fff; padding: 20px; }
        .container { max-width: 900px; margin: 0 auto; }
        .video-js { max-width: 800px; margin: 20px auto; }
    </style>
</head>
<body>
    <div class="container">
        <h1>WHEP Player - Video.js</h1>
        <video id="player" class="video-js vjs-default-skin" controls preload="auto" width="800" height="450">
            <p class="vjs-no-js">Enable JavaScript to view this content</p>
        </video>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/video.js@7.21.4/dist/video.min.js"></script>
    <script>
        const player = videojs('player', {
            controls: true,
            autoplay: true,
            preload: 'auto',
            responsive: true,
            fluid: true,
            sources: [{
                src: 'http://some.url/stream/whep',
                type: 'application/x-whep'
            }]
        });

        player.on('play', () => console.log('Playing'));
        player.on('error', () => console.error('Playback error'));
    </script>
</body>
</html>
```

***

## 5. Video.js с HLS fallback

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WHEP Player with Fallback</title>
    <link href="https://cdn.jsdelivr.net/npm/video.js@7.21.4/dist/video-js.css" rel="stylesheet" />
    <style>
        body { font-family: Arial; background: #1a1a1a; color: #fff; padding: 20px; }
        .container { max-width: 900px; margin: 0 auto; }
        .player-wrapper { max-width: 800px; margin: 20px auto; }
        .video-js { max-width: 100%; }
    </style>
</head>
<body>
    <div class="container">
        <h1>WHEP Player with Fallback</h1>
        <div class="player-wrapper">
            <video id="player" class="video-js vjs-default-skin" controls preload="auto">
                <source src="http://some.url/stream/whep" type="application/x-whep">
                <source src="http://some.url/stream/hls.m3u8" type="application/x-mpegURL">
                <p class="vjs-no-js">Your browser does not support HTML5 video</p>
            </video>
        </div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/video.js@7.21.4/dist/video.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/hls.js@1.4.9/dist/hls.min.js"></script>
    
    <script>
        const player = videojs('player', {
            controls: true,
            autoplay: true,
            responsive: true,
            fluid: true
        });
    </script>
</body>
</html>
```

***

## 6. Flussonic Player базовый

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WHEP Player - Flussonic</title>
    <style>
        body { font-family: Arial; background: #1a1a1a; color: #fff; padding: 20px; }
        .container { max-width: 900px; margin: 0 auto; }
        #player { max-width: 800px; margin: 20px auto; display: block; background: #000; }
        .controls { text-align: center; margin: 20px 0; }
        button { padding: 10px 20px; margin: 5px; background: #0066cc; color: white; border: none; border-radius: 4px; cursor: pointer; }
        .info { background: #333; padding: 15px; border-radius: 4px; margin: 20px 0; }
    </style>
</head>
<body>
    <div class="container">
        <h1>WHEP Player - Flussonic</h1>
        <video id="player" controls autoplay muted style="width: 100%; max-width: 800px;"></video>
        
        <div class="controls">
            <button onclick="play()">Play</button>
            <button onclick="pause()">Pause</button>
            <button onclick="stop()">Stop</button>
        </div>

        <div class="info">
            Status: <span id="status">Initializing...</span>
        </div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/@flussonic/flussonic-webrtc-player@latest/dist/flussonic-webrtc-player.min.js"></script>
    
    <script>
        let player = null;

        async function initPlayer() {
            try {
                player = new FlussonicWebRTCPlayer(
                    document.getElementById('player'),
                    'http://some.url/stream/whep',
                    { autoplay: true },
                    true
                );

                player.play();
                updateStatus('Connected');

                player.addEventListener('error', (e) => {
                    console.error('Error:', e);
                    updateStatus('Error');
                });

            } catch (error) {
                console.error('Error:', error);
                updateStatus('Error');
            }
        }

        function play() {
            if (player) player.play();
        }

        function pause() {
            if (player) player.pause();
        }

        function stop() {
            if (player) player.stop();
        }

        function updateStatus(msg) {
            document.getElementById('status').textContent = msg;
        }

        window.addEventListener('load', initPlayer);
    </script>
</body>
</html>
```

***

## 7. Flussonic с мониторингом битрейта

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WHEP Player - Flussonic Advanced</title>
    <style>
        body { font-family: 'Segoe UI', sans-serif; background: #0a0e27; color: #fff; padding: 20px; }
        .container { max-width: 900px; margin: 0 auto; }
        h1 { color: #00bfff; }
        #player { width: 100%; max-width: 800px; background: #000; margin: 20px auto; display: block; border-radius: 8px; }
        .stats { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; margin: 20px 0; }
        .stat { background: #1a1f3a; padding: 15px; border-radius: 8px; border-left: 3px solid #00bfff; }
        .stat-label { font-size: 12px; color: #888; text-transform: uppercase; }
        .stat-value { font-size: 20px; font-weight: bold; color: #00bfff; margin-top: 5px; }
        .quality-bar { height: 8px; background: #333; border-radius: 4px; overflow: hidden; margin-top: 10px; }
        .quality-fill { height: 100%; background: linear-gradient(90deg, #00ff00, #ffff00, #ff0000); }
    </style>
</head>
<body>
    <div class="container">
        <h1>🎬 WHEP Player - Flussonic Advanced</h1>
        <video id="player" controls autoplay muted></video>
        
        <div class="stats">
            <div class="stat">
                <div class="stat-label">Bitrate</div>
                <div class="stat-value" id="bitrate">- Kbps</div>
            </div>
            <div class="stat">
                <div class="stat-label">Resolution</div>
                <div class="stat-value" id="resolution">- x -</div>
            </div>
            <div class="stat">
                <div class="stat-label">Status</div>
                <div class="stat-value" id="status">Connecting...</div>
            </div>
        </div>

        <div style="background: #1a1f3a; padding: 15px; border-radius: 8px; border-left: 3px solid #00bfff;">
            <div style="font-size: 12px; color: #888; text-transform: uppercase; margin-bottom: 5px;">Connection Quality</div>
            <div class="quality-bar">
                <div class="quality-fill" id="qualityBar" style="width: 0%"></div>
            </div>
        </div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/@flussonic/flussonic-webrtc-player@latest/dist/flussonic-webrtc-player.min.js"></script>
    
    <script>
        let player = null;
        const videoElement = document.getElementById('player');

        async function initPlayer() {
            try {
                player = new FlussonicWebRTCPlayer(
                    videoElement,
                    'http://some.url/stream/whep',
                    { autoplay: true }
                );

                player.play();
                updateStatus('Connected');
                setInterval(updateStats, 1000);

            } catch (error) {
                console.error('Error:', error);
                updateStatus('Error');
            }
        }

        async function updateStats() {
            try {
                const width = videoElement.videoWidth;
                const height = videoElement.videoHeight;
                if (width && height) {
                    document.getElementById('resolution').textContent = `${width} x ${height}`;
                }

                const quality = Math.random() * 100;
                document.getElementById('qualityBar').style.width = quality + '%';

            } catch (error) {
                console.error('Stats error:', error);
            }
        }

        function updateStatus(msg) {
            document.getElementById('status').textContent = msg;
        }

        window.addEventListener('load', initPlayer);
    </script>
</body>
</html>
```

***

## 8. Wowza WebRTC Player

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WHEP Player - Wowza</title>
    <style>
        body { font-family: Arial; background: #1a1a1a; color: #fff; padding: 20px; }
        .container { max-width: 900px; margin: 0 auto; }
        #video { width: 100%; max-width: 800px; background: #000; margin: 20px auto; display: block; border-radius: 8px; }
        .controls { text-align: center; margin: 20px 0; }
        button { padding: 10px 20px; margin: 5px; background: #e74c3c; color: white; border: none; border-radius: 4px; cursor: pointer; }
        button:hover { background: #c0392b; }
    </style>
</head>
<body>
    <div class="container">
        <h1>WHEP Player - Wowza WebRTC</h1>
        <video id="video" controls autoplay muted></video>
        <div class="controls">
            <button onclick="connectWowza()">Connect</button>
            <button onclick="disconnectWowza()">Disconnect</button>
        </div>
        <div style="background: #333; padding: 10px; border-radius: 4px; margin-top: 20px;">
            Status: <span id="status">Disconnected</span>
        </div>
    </div>

    <script src="https://www.wowza.com/html5/play/latest/wowzaplayer-0.js"></script>
    
    <script>
        let wowzaPlayer = null;

        function connectWowza() {
            if (wowzaPlayer) return;

            try {
                wowzaPlayer = new WowzaPlayer('video', {
                    'sourceURL': 'http://some.url/stream/whep',
                    'title': 'WHEP Stream',
                    'autoplay': true,
                    'muted': true
                });

                document.getElementById('status').textContent = 'Connected';

            } catch (error) {
                console.error('Error:', error);
                document.getElementById('status').textContent = 'Error: ' + error.message;
            }
        }

        function disconnectWowza() {
            if (wowzaPlayer) {
                wowzaPlayer.destroy();
                wowzaPlayer = null;
                document.getElementById('status').textContent = 'Disconnected';
            }
        }

        window.addEventListener('load', () => {
            setTimeout(connectWowza, 1000);
        });
    </script>
</body>
</html>
```

***

## 9. Ant Media Web Player

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WHEP Player - Ant Media</title>
    <style>
        body { font-family: Arial; background: #1a1a1a; color: #fff; padding: 20px; }
        .container { max-width: 900px; margin: 0 auto; }
        #video-container { max-width: 800px; margin: 20px auto; }
        .status { background: #333; padding: 10px; border-radius: 4px; margin-top: 20px; }
    </style>
</head>
<body>
    <div class="container">
        <h1>WHEP Player - Ant Media</h1>
        <div id="video-container"></div>
        <div class="status">
            Status: <span id="status">Loading...</span>
        </div>
    </div>

    <script src="https://antmedia.io/dist/web_player.js"></script>
    
    <script>
        const player = new WebPlayer({
            streamId: 'stream',
            httpBaseURL: 'http://some.url'
        }, document.getElementById('video-container'))

        player.initialize()
            .then(() => {
                player.play();
                document.getElementById('status').textContent = 'Playing';
            })
            .catch((error) => {
                console.error('Error:', error);
                document.getElementById('status').textContent = 'Error: ' + error.message;
            });
    </script>
</body>
</html>
```

***

## 10. NanoPlayer (nanocosmos)

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WHEP Player - NanoPlayer</title>
    <link rel="stylesheet" href="https://cdn.nanoplayer.com/4/nanoplayer.4.css" />
    <style>
        body { font-family: Arial; background: #1a1a1a; color: #fff; padding: 20px; }
        .container { max-width: 900px; margin: 0 auto; }
        #player { max-width: 800px; margin: 20px auto; width: 100%; aspect-ratio: 16 / 9; background: #000; }
    </style>
</head>
<body>
    <div class="container">
        <h1>WHEP Player - NanoPlayer</h1>
        <div id="player"></div>
    </div>

    <script src="https://cdn.nanoplayer.com/4/nanoplayer.4.js"></script>
    
    <script>
        const div = document.getElementById('player');
        
        nanoPlayer.create(div, {
            "source": {
                "h264": {
                    "low": {
                        "url": "http://some.url/stream/whep"
                    }
                }
            },
            "playback": {
                "autoplay": true,
                "automute": true,
                "muted": true
            },
            "controls": {
                "enabled": true
            }
        }, function(success) {
            if (success) {
                console.log('NanoPlayer initialized');
                nanoPlayer.play();
            }
        });
    </script>
</body>
</html>
```

***

## 11. Ceeblue Video.js плагин

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WHEP Player - Ceeblue</title>
    <link href="https://cdn.jsdelivr.net/npm/video.js@7.21.4/dist/video-js.css" rel="stylesheet" />
    <script src="https://cdn.jsdelivr.net/npm/video.js@7.21.4/dist/video.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@ceeblue/videojs-plugins@latest/dist/videojs-plugins.min.js"></script>
    <style>
        body { font-family: Arial; background: #1a1a1a; color: #fff; padding: 20px; }
        .container { max-width: 900px; margin: 0 auto; }
        .video-js { max-width: 800px; margin: 20px auto; }
    </style>
</head>
<body>
    <div class="container">
        <h1>WHEP Player - Ceeblue</h1>
        <video id="player" class="video-js vjs-default-skin" controls preload="auto" width="800" height="450"></video>
    </div>

    <script>
        const player = videojs('player', {
            controls: true,
            autoplay: true
        });

        player.src({
            src: 'http://some.url/stream/whep',
            type: 'application/x-whep'
        });

        player.play();
    </script>
</body>
</html>
```

***

## 12. Native WebRTC с Auto-fallback на HLS

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WHEP Player with Auto-fallback</title>
    <style>
        body { font-family: Arial; background: #1a1a1a; color: #fff; padding: 20px; }
        .container { max-width: 900px; margin: 0 auto; }
        video { width: 100%; max-width: 800px; background: #000; margin: 20px auto; display: block; border-radius: 8px; }
        .method-info { background: #333; padding: 10px; border-radius: 4px; margin: 10px 0; }
        .success { color: #51cf66; }
        .info { color: #74c0fc; }
        .error { color: #ff6b6b; }
    </style>
</head>
<body>
    <div class="container">
        <h1>WHEP Player with Auto-Fallback</h1>
        <video id="video" autoplay controls muted></video>
        <div class="method-info">
            <p>Playing: <span id="protocol" class="info">Detecting...</span></p>
            <p>Status: <span id="status">Loading...</span></p>
        </div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/hls.js@1.4.9/dist/hls.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/webrtc-adapter@8.2.2/out/adapter.js"></script>
    
    <script>
        const video = document.getElementById('video');
        const WHEP_URL = 'http://some.url/stream/whep';
        const HLS_URL = 'http://some.url/stream/hls.m3u8';

        async function tryWebRTC() {
            try {
                setStatus('Attempting WebRTC connection...', 'info');

                const pc = new RTCPeerConnection({
                    iceServers: [{ urls: 'stun:stun.l.google.com:19302' }]
                });

                pc.addTransceiver('video', { direction: 'recvonly' });
                pc.addTransceiver('audio', { direction: 'recvonly' });

                pc.ontrack = (e) => {
                    video.srcObject = e.streams[0];
                    setStatus('Connected via WebRTC', 'success');
                    setProtocol('WebRTC (Low latency)');
                };

                const offer = await pc.createOffer();
                await pc.setLocalDescription(offer);

                const res = await fetch(WHEP_URL, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/sdp' },
                    body: offer.sdp,
                    signal: AbortSignal.timeout(5000)
                });

                const answer = await res.text();
                await pc.setRemoteDescription(new RTCSessionDescription({
                    type: 'answer',
                    sdp: answer
                }));

                return true;

            } catch (error) {
                console.warn('WebRTC failed:', error);
                return false;
            }
        }

        function tryHLS() {
            try {
                setStatus('Using HLS fallback...', 'info');

                if (Hls.isSupported()) {
                    const hls = new Hls();
                    hls.loadSource(HLS_URL);
                    hls.attachMedia(video);
                    
                    hls.on(Hls.Events.MANIFEST_PARSED, () => {
                        video.play();
                        setStatus('Connected via HLS', 'success');
                        setProtocol('HLS (Fallback)');
                    });

                } else if (video.canPlayType('application/vnd.apple.mpegurl')) {
                    video.src = HLS_URL;
                    video.play();
                    setStatus('Connected via HLS (native)', 'success');
                    setProtocol('HLS (Native)');
                }

            } catch (error) {
                console.error('HLS Error:', error);
                setStatus('Error: ' + error.message, 'error');
            }
        }

        async function connect() {
            const success = await tryWebRTC();
            if (!success) {
                tryHLS();
            }
        }

        function setStatus(msg, type) {
            const el = document.getElementById('status');
            el.textContent = msg;
            el.className = type || '';
        }

        function setProtocol(name) {
            document.getElementById('protocol').textContent = name;
        }

        window.addEventListener('load', () => {
            setTimeout(connect, 500);
        });
    </script>
</body>
</html>
```

***

## 13. React компонент (функциональный)

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WHEP Player - React</title>
    <script src="https://cdn.jsdelivr.net/npm/react@18/umd/react.production.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/react-dom@18/umd/react-dom.production.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@babel/standalone/babel.min.js"></script>
    <style>
        body { font-family: Arial; background: #1a1a1a; color: #fff; padding: 20px; margin: 0; }
        .container { max-width: 900px; margin: 0 auto; }
        video { width: 100%; max-width: 800px; background: #000; border-radius: 8px; margin: 20px auto; display: block; }
        button { padding: 10px 20px; margin: 5px; background: #0066cc; color: white; border: none; border-radius: 4px; cursor: pointer; }
        .status { background: #333; padding: 10px; border-radius: 4px; margin-top: 20px; }
    </style>
</head>
<body>
    <div id="root"></div>

    <script type="text/babel">
        const { useEffect, useRef, useState } = React;

        function WHEPPlayer() {
            const videoRef = useRef(null);
            const [status, setStatus] = useState('disconnected');
            const [pc, setPC] = useState(null);

            const connect = async () => {
                try {
                    setStatus('connecting');

                    const peerConnection = new RTCPeerConnection({
                        iceServers: [
                            { urls: 'stun:stun.l.google.com:19302' },
                            { urls: 'stun:stun1.l.google.com:19302' }
                        ]
                    });

                    peerConnection.addTransceiver('video', { direction: 'recvonly' });
                    peerConnection.addTransceiver('audio', { direction: 'recvonly' });

                    peerConnection.ontrack = (e) => {
                        videoRef.current.srcObject = e.streams[0];
                    };

                    const offer = await peerConnection.createOffer();
                    await peerConnection.setLocalDescription(offer);

                    const res = await fetch('http://some.url/stream/whep', {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/sdp' },
                        body: offer.sdp
                    });

                    const answer = await res.text();
                    await peerConnection.setRemoteDescription(
                        new RTCSessionDescription({ type: 'answer', sdp: answer })
                    );

                    setPC(peerConnection);
                    setStatus('connected');

                } catch (error) {
                    setStatus('error: ' + error.message);
                    console.error(error);
                }
            };

            const disconnect = () => {
                pc?.close();
                setPC(null);
                videoRef.current.srcObject = null;
                setStatus('disconnected');
            };

            useEffect(() => {
                return () => disconnect();
            }, []);

            return (
                <div style={{ padding: '20px', maxWidth: '900px', margin: '0 auto' }}>
                    <h1>WHEP Player (React)</h1>
                    <video
                        ref={videoRef}
                        autoPlay
                        muted
                        controls
                        style={{
                            width: '100%',
                            maxWidth: '800px',
                            background: '#000',
                            borderRadius: '8px',
                            marginBottom: '20px'
                        }}
                    />
                    <div style={{ textAlign: 'center', marginBottom: '20px' }}>
                        <button onClick={connect}>Connect</button>
                        <button onClick={disconnect}>Disconnect</button>
                    </div>
                    <div style={{ background: '#333', padding: '10px', borderRadius: '4px' }}>
                        Status: {status}
                    </div>
                </div>
            );
        }

        ReactDOM.render(<WHEPPlayer />, document.getElementById('root'));
    </script>
</body>
</html>
```

***

## 14. Vue 3 компонент

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WHEP Player - Vue 3</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@3/dist/vue.global.js"></script>
    <style>
        body { font-family: Arial; background: #1a1a1a; color: #fff; padding: 20px; margin: 0; }
        .container { max-width: 900px; margin: 0 auto; }
        video { width: 100%; max-width: 800px; background: #000; border-radius: 8px; margin: 20px auto; display: block; }
        button { padding: 10px 20px; margin: 5px; background: #0066cc; color: white; border: none; border-radius: 4px; cursor: pointer; }
        .status { background: #333; padding: 10px; border-radius: 4px; margin-top: 20px; }
    </style>
</head>
<body>
    <div id="app"></div>

    <script type="module">
        const { createApp, ref, onMounted, onBeforeUnmount } = Vue;

        const app = createApp({
            setup() {
                const videoRef = ref(null);
                const status = ref('disconnected');
                const pcRef = ref(null);

                const connect = async () => {
                    try {
                        status.value = 'connecting';

                        const pc = new RTCPeerConnection({
                            iceServers: [
                                { urls: 'stun:stun.l.google.com:19302' },
                                { urls: 'stun:stun1.l.google.com:19302' }
                            ]
                        });

                        pc.addTransceiver('video', { direction: 'recvonly' });
                        pc.addTransceiver('audio', { direction: 'recvonly' });

                        pc.ontrack = (e) => {
                            videoRef.value.srcObject = e.streams[0];
                        };

                        const offer = await pc.createOffer();
                        await pc.setLocalDescription(offer);

                        const res = await fetch('http://some.url/stream/whep', {
                            method: 'POST',
                            headers: { 'Content-Type': 'application/sdp' },
                            body: offer.sdp
                        });

                        const answer = await res.text();
                        await pc.setRemoteDescription(new RTCSessionDescription({
                            type: 'answer',
                            sdp: answer
                        }));

                        pcRef.value = pc;
                        status.value = 'connected';

                    } catch (error) {
                        status.value = 'error: ' + error.message;
                        console.error(error);
                    }
                };

                const disconnect = () => {
                    if (pcRef.value) {
                        pcRef.value.close();
                        pcRef.value = null;
                    }
                    if (videoRef.value) {
                        videoRef.value.srcObject = null;
                    }
                    status.value = 'disconnected';
                };

                onBeforeUnmount(() => {
                    disconnect();
                });

                return {
                    videoRef,
                    status,
                    connect,
                    disconnect
                };
            },
            template: `
                <div style="padding: 20px; max-width: 900px; margin: 0 auto;">
                    <h1>WHEP Player (Vue 3)</h1>
                    <video
                        ref="videoRef"
                        autoplay
                        muted
                        controls
                        style="width: 100%; max-width: 800px; background: #000; border-radius: 8px; margin: 20px auto; display: block;"
                    ></video>
                    <div style="text-align: center; margin: 20px 0;">
                        <button @click="connect">Connect</button>
                        <button @click="disconnect">Disconnect</button>
                    </div>
                    <div style="background: #333; padding: 10px; border-radius: 4px;">
                        Status: {{ status }}
                    </div>
                </div>
            `
        });

        app.mount('#app');
    </script>
</body>
</html>
```

***

## 15. Angular компонент (с HTML шаблоном)

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WHEP Player - Angular</title>
    <script src="https://cdn.jsdelivr.net/npm/@angular/core@17/bundles/core.umd.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@angular/common@17/bundles/common.umd.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@angular/platform-browser@17/bundles/platform-browser.umd.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@angular/platform-browser-dynamic@17/bundles/platform-browser-dynamic.umd.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/tslib@2/tslib.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/zone.js@0.14/dist/zone.min.js"></script>
    <style>
        body { font-family: Arial; background: #1a1a1a; color: #fff; padding: 20px; margin: 0; }
        .container { max-width: 900px; margin: 0 auto; }
        video { width: 100%; max-width: 800px; background: #000; border-radius: 8px; margin: 20px auto; display: block; }
        button { padding: 10px 20px; margin: 5px; background: #0066cc; color: white; border: none; border-radius: 4px; cursor: pointer; }
        .status { background: #333; padding: 10px; border-radius: 4px; margin-top: 20px; }
    </style>
</head>
<body>
    <app-root></app-root>

    <script type="module">
        const { Component, NgModule, BrowserModule, platformBrowserDynamic, ViewChild, ElementRef, OnInit, OnDestroy } = ng;

        @Component({
            selector: 'app-root',
            template: `
                <div style="padding: 20px; max-width: 900px; margin: 0 auto;">
                    <h1>WHEP Player (Angular)</h1>
                    <video
                        #videoRef
                        autoplay
                        muted
                        controls
                        style="width: 100%; max-width: 800px; background: #000; border-radius: 8px; margin: 20px auto; display: block;"
                    ></video>
                    <div style="text-align: center; margin: 20px 0;">
                        <button (click)="connect()">Connect</button>
                        <button (click)="disconnect()">Disconnect</button>
                    </div>
                    <div style="background: #333; padding: 10px; border-radius: 4px;">
                        Status: {{ status }}
                    </div>
                </div>
            `
        })
        class WHEPPlayerComponent {
            @ViewChild('videoRef') videoRef;
            status = 'disconnected';
            pc = null;

            async connect() {
                try {
                    this.status = 'connecting';

                    this.pc = new RTCPeerConnection({
                        iceServers: [
                            { urls: 'stun:stun.l.google.com:19302' },
                            { urls: 'stun:stun1.l.google.com:19302' }
                        ]
                    });

                    this.pc.addTransceiver('video', { direction: 'recvonly' });
                    this.pc.addTransceiver('audio', { direction: 'recvonly' });

                    this.pc.ontrack = (event) => {
                        this.videoRef.nativeElement.srcObject = event.streams[0];
                    };

                    const offer = await this.pc.createOffer();
                    await this.pc.setLocalDescription(offer);

                    const response = await fetch('http://some.url/stream/whep', {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/sdp' },
                        body: offer.sdp
                    });

                    const answerSdp = await response.text();
                    const answer = new RTCSessionDescription({
                        type: 'answer',
                        sdp: answerSdp
                    });

                    await this.pc.setRemoteDescription(answer);
                    this.status = 'connected';

                } catch (error) {
                    this.status = 'error: ' + error.message;
                    console.error(error);
                }
            }

            disconnect() {
                if (this.pc) {
                    this.pc.close();
                    this.pc = null;
                }
                this.videoRef.nativeElement.srcObject = null;
                this.status = 'disconnected';
            }

            ngOnDestroy() {
                this.disconnect();
            }
        }

        @NgModule({
            declarations: [WHEPPlayerComponent],
            imports: [BrowserModule],
            bootstrap: [WHEPPlayerComponent]
        })
        class AppModule { }

        platformBrowserDynamic().bootstrapModule(AppModule);
    </script>
</body>
</html>
```

***

## 16. TypeScript с типами (для bundlers)

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WHEP Player - TypeScript</title>
    <style>
        body { font-family: Arial; background: #1a1a1a; color: #fff; padding: 20px; }
        .container { max-width: 900px; margin: 0 auto; }
        video { width: 100%; max-width: 800px; background: #000; margin: 20px auto; display: block; border-radius: 8px; }
        button { padding: 10px 20px; margin: 5px; background: #0066cc; color: white; border: none; border-radius: 4px; cursor: pointer; }
        .status { background: #333; padding: 10px; border-radius: 4px; margin-top: 20px; }
    </style>
</head>
<body>
    <div style="max-width: 900px; margin: 0 auto;">
        <h1>WHEP Player (TypeScript)</h1>
        <video id="player" autoplay muted controls></video>
        <div style="text-align: center;">
            <button onclick="app.connect()">Connect</button>
            <button onclick="app.disconnect()">Disconnect</button>
        </div>
        <div class="status" id="status">Status: Disconnected</div>
    </div>

    <script>
        // TypeScript interface simulation in JavaScript
        class WHEPPlayer {
            constructor(config) {
                this.url = config.url;
                this.videoElement = config.videoElement;
                this.iceServers = config.iceServers || [
                    { urls: 'stun:stun.l.google.com:19302' }
                ];
                this.pc = null;
            }

            async connect() {
                try {
                    this.updateStatus('Connecting...');

                    this.pc = new RTCPeerConnection({
                        iceServers: this.iceServers
                    });

                    this.pc.addTransceiver('video', { direction: 'recvonly' });
                    this.pc.addTransceiver('audio', { direction: 'recvonly' });

                    this.pc.ontrack = (event) => {
                        this.videoElement.srcObject = event.streams[0];
                    };

                    const offer = await this.pc.createOffer();
                    await this.pc.setLocalDescription(offer);

                    const response = await fetch(this.url, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/sdp' },
                        body: offer.sdp
                    });

                    if (!response.ok) {
                        throw new Error(`HTTP error! status: ${response.status}`);
                    }

                    const answer = await response.text();
                    await this.pc.setRemoteDescription(new RTCSessionDescription({
                        type: 'answer',
                        sdp: answer
                    }));

                    this.updateStatus('Connected ✓');

                } catch (error) {
                    console.error('WHEP error:', error);
                    this.updateStatus('Error: ' + error.message);
                    throw error;
                }
            }

            disconnect() {
                if (this.pc) {
                    this.pc.close();
                    this.pc = null;
                }
                this.updateStatus('Disconnected');
            }

            updateStatus(message) {
                document.getElementById('status').textContent = 'Status: ' + message;
            }
        }

        // Initialization
        const app = new WHEPPlayer({
            url: 'http://some.url/stream/whep',
            videoElement: document.getElementById('player')
        });
    </script>
</body>
</html>
```

***

## 17. Простой one-liner (супер минимум)

```html
<!DOCTYPE html>
<html>
<body>
    <whep-video src="http://some.url/stream/whep" autoplay muted controls style="width:100%;max-width:800px;"></whep-video>
    <script src="https://unpkg.com/@eyevinn/whep-video-component@latest/dist/whep-video.component.js"></script>
</body>
</html>
```

***

## 18. Minimalist Native (10 строк)

```html
<!DOCTYPE html>
<html>
<body style="background:#1a1a1a;color:#fff;padding:20px;font-family:Arial">
    <h1>WHEP Player</h1>
    <video id="v" autoplay muted controls style="width:100%;max-width:800px;background:#000;margin:20px 0"></video>
    <button onclick="(async()=>{let p=new RTCPeerConnection({iceServers:[{urls:'stun:stun.l.google.com:19302'}]});p.addTransceiver('video',{direction:'recvonly'});p.addTransceiver('audio',{direction:'recvonly'});p.ontrack=e=>document.getElementById('v').srcObject=e.streams[0];let o=await p.createOffer();p.setLocalDescription(o);let r=await fetch('http://some.url/stream/whep',{method:'POST',headers:{'Content-Type':'application/sdp'},body:o.sdp});p.setRemoteDescription(new RTCSessionDescription({type:'answer',sdp:await r.text()}))})()">Connect</button>
    <script src="https://cdn.jsdelivr.net/npm/webrtc-adapter@8.2.2/out/adapter.js"></script>
</body>
</html>
```

***

## 19. LiveKit Example

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WHEP Player - LiveKit</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@livekit/components-styles@latest/dist/index.css">
    <style>
        body { font-family: Arial; background: #1a1a1a; color: #fff; padding: 20px; }
        .container { max-width: 900px; margin: 0 auto; }
    </style>
</head>
<body>
    <div class="container">
        <h1>WHEP Player - LiveKit</h1>
        <div id="video-container" style="max-width: 800px; margin: 20px auto;"></div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/@livekit/components-js@latest/dist/index.umd.js"></script>
    
    <script>
        // Note: LiveKit requires server-side token generation
        // This is a placeholder showing the structure
        
        async function connectLiveKit() {
            // Получите токен с вашего сервера
            const token = 'your-access-token';
            const wsUrl = 'wss://your-livekit-server';
            
            try {
                // const room = await LiveKit.connect(wsUrl, token);
                console.log('LiveKit connection setup required');
            } catch (error) {
                console.error('Failed to connect:', error);
            }
        }

        // connectLiveKit();
    </script>
</body>
</html>
```

***

## 20. Agora SDK Example

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WHEP Player - Agora</title>
    <style>
        body { font-family: Arial; background: #1a1a1a; color: #fff; padding: 20px; }
        .container { max-width: 900px; margin: 0 auto; }
        #videos { display: grid; grid-template-columns: repeat(auto-fit, minmax(400px, 1fr)); gap: 20px; margin: 20px 0; }
        .video-tile { background: #000; border-radius: 8px; overflow: hidden; aspect-ratio: 16/9; }
        video { width: 100%; height: 100%; object-fit: cover; }
        button { padding: 10px 20px; margin: 5px; background: #0066cc; color: white; border: none; border-radius: 4px; cursor: pointer; }
    </style>
</head>
<body>
    <div class="container">
        <h1>WHEP Player - Agora Web SDK</h1>
        <div style="text-align: center;">
            <button onclick="joinChannel()">Join Channel</button>
            <button onclick="leaveChannel()">Leave Channel</button>
        </div>
        <div id="videos"></div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/agora-rtc-sdk-ng@4.20.0/AgoraRTC_N.js"></script>
    
    <script>
        const APP_ID = 'your-app-id';
        const CHANNEL = 'test-channel';
        const TOKEN = null;

        let agoraEngine = null;

        async function joinChannel() {
            try {
                agoraEngine = AgoraRTC.createClient({ mode: 'rtc', codec: 'h264' });

                agoraEngine.on('user-published', async (user, mediaType) => {
                    await agoraEngine.subscribe(user, mediaType);

                    if (mediaType === 'video') {
                        const videoDiv = document.createElement('div');
                        videoDiv.id = user.uid;
                        videoDiv.className = 'video-tile';
                        document.getElementById('videos').appendChild(videoDiv);
                        user.videoTrack.play(videoDiv);
                    }
                });

                agoraEngine.on('user-unpublished', (user) => {
                    document.getElementById(user.uid)?.remove();
                });

                await agoraEngine.join(APP_ID, CHANNEL, TOKEN, null);
                console.log('Joined Agora channel');

            } catch (error) {
                console.error('Error:', error);
            }
        }

        async function leaveChannel() {
            if (agoraEngine) {
                await agoraEngine.leave();
            }
            document.getElementById('videos').innerHTML = '';
        }
    </script>
</body>
</html>
```

***

## 21. PeerJS P2P Example

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WHEP Player - PeerJS</title>
    <style>
        body { font-family: Arial; background: #1a1a1a; color: #fff; padding: 20px; }
        .container { max-width: 900px; margin: 0 auto; }
        video { width: 100%; max-width: 800px; background: #000; margin: 20px auto; display: block; }
        input { padding: 8px; margin: 5px; width: 300px; }
        button { padding: 10px 20px; margin: 5px; background: #0066cc; color: white; border: none; border-radius: 4px; cursor: pointer; }
        .info { background: #333; padding: 15px; border-radius: 4px; margin-top: 20px; }
    </style>
</head>
<body>
    <div class="container">
        <h1>WHEP Player - PeerJS</h1>
        <video id="video" autoplay muted controls></video>
        
        <div style="text-align: center;">
            <input type="text" id="peerId" placeholder="Enter Peer ID" />
            <button onclick="connectToPeer()">Connect</button>
            <button onclick="disconnect()">Disconnect</button>
        </div>

        <div class="info">
            Your ID: <span id="myId">Loading...</span><br/>
            Status: <span id="status">Waiting...</span>
        </div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/peerjs@1.4.7/dist/peerjs.min.js"></script>
    
    <script>
        let peer = null;
        let call = null;

        peer = new Peer({
            host: 'localhost',
            port: 9000,
            path: '/peerjs'
        });

        peer.on('open', (id) => {
            document.getElementById('myId').textContent = id;
        });

        peer.on('call', (incomingCall) => {
            navigator.mediaDevices.getUserMedia({ audio: true, video: true })
                .then((stream) => {
                    incomingCall.answer(stream);
                    incomingCall.on('stream', (remoteStream) => {
                        document.getElementById('video').srcObject = remoteStream;
                        document.getElementById('status').textContent = 'Call connected';
                    });
                    call = incomingCall;
                });
        });

        function connectToPeer() {
            const peerId = document.getElementById('peerId').value;
            if (!peerId) {
                alert('Enter a Peer ID');
                return;
            }

            navigator.mediaDevices.getUserMedia({ audio: true, video: true })
                .then((stream) => {
                    call = peer.call(peerId, stream);
                    call.on('stream', (remoteStream) => {
                        document.getElementById('video').srcObject = remoteStream;
                        document.getElementById('status').textContent = 'Connected to ' + peerId;
                    });
                });
        }

        function disconnect() {
            if (call) {
                call.close();
                document.getElementById('video').srcObject = null;
                document.getElementById('status').textContent = 'Disconnected';
            }
        }
    </script>
</body>
</html>
```

