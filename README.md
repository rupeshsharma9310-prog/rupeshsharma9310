# rupeshsharma9310
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CinemaAI — AI Video Generator</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            font-family: 'Inter', system-ui, -apple-system, sans-serif;
            background: linear-gradient(180deg, #0a0a0f 0%, #12121a 100%);
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 20px;
            color: #e2e8f0;
        }
        .container {
            max-width: 920px;
            width: 100%;
            background: rgba(18, 18, 26, 0.95);
            backdrop-filter: blur(20px);
            border-radius: 28px;
            padding: 36px;
            box-shadow: 0 30px 60px -15px rgba(0,0,0,0.7);
            border: 1px solid rgba(255,255,255,0.06);
        }
        .header { text-align: center; margin-bottom: 32px; }
        .header h1 {
            font-size: 28px;
            font-weight: 800;
            background: linear-gradient(90deg, #f43f5e, #fb923c, #fbbf24);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            letter-spacing: -0.5px;
            margin-bottom: 6px;
        }
        .header p { color: #64748b; font-size: 14px; font-weight: 500; }

        /* Tabs */
        .tabs {
            display: flex;
            gap: 8px;
            margin-bottom: 28px;
            background: rgba(255,255,255,0.03);
            padding: 6px;
            border-radius: 16px;
            border: 1px solid rgba(255,255,255,0.05);
        }
        .tab {
            flex: 1;
            padding: 12px 16px;
            border: none;
            border-radius: 12px;
            background: transparent;
            color: #64748b;
            font-size: 14px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s;
            font-family: inherit;
        }
        .tab.active {
            background: linear-gradient(135deg, #f43f5e, #fb923c);
            color: white;
            box-shadow: 0 4px 15px rgba(244,63,94,0.3);
        }

        /* Grid Layout */
        .grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 28px;
        }
        @media (max-width: 768px) { .grid { grid-template-columns: 1fr; } }

        /* Form Elements */
        .form-group { margin-bottom: 20px; }
        label {
            display: block;
            font-size: 11px;
            font-weight: 700;
            color: #94a3b8;
            margin-bottom: 8px;
            text-transform: uppercase;
            letter-spacing: 0.08em;
        }
        textarea, input[type="text"], select {
            width: 100%;
            padding: 14px;
            border: 2px solid #1e293b;
            border-radius: 14px;
            background: #0f1117;
            color: #e2e8f0;
            font-size: 14px;
            outline: none;
            transition: all 0.2s;
            font-family: inherit;
        }
        textarea { min-height: 100px; resize: vertical; line-height: 1.5; }
        textarea:focus, input:focus, select:focus { border-color: #f43f5e; }
        .hint-row {
            display: flex;
            justify-content: space-between;
            margin-top: 6px;
        }
        .hint { font-size: 11px; color: #475569; }
        .hint.active { color: #f43f5e; }

        .row { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }

        /* Style Presets */
        .presets {
            display: flex;
            gap: 8px;
            flex-wrap: wrap;
        }
        .preset-btn {
            padding: 8px 16px;
            border: 1px solid #334155;
            border-radius: 20px;
            background: transparent;
            color: #94a3b8;
            font-size: 12px;
            cursor: pointer;
            transition: all 0.2s;
            font-family: inherit;
        }
        .preset-btn:hover, .preset-btn.active {
            border-color: #f43f5e;
            background: rgba(244,63,94,0.15);
            color: #f43f5e;
            font-weight: 600;
        }

        /* Generate Button */
        .generate-btn {
            width: 100%;
            padding: 18px;
            border: none;
            border-radius: 16px;
            background: linear-gradient(135deg, #f43f5e, #fb923c);
            color: white;
            font-size: 16px;
            font-weight: 700;
            cursor: pointer;
            transition: all 0.3s;
            font-family: inherit;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            position: relative;
            overflow: hidden;
        }
        .generate-btn:hover:not(:disabled) {
            transform: translateY(-2px);
            box-shadow: 0 10px 30px -5px rgba(244,63,94,0.4);
        }
        .generate-btn:disabled {
            opacity: 0.6;
            cursor: not-allowed;
        }

        /* Preview Area */
        .preview-area {
            aspect-ratio: 16/9;
            background: #0a0a0f;
            border-radius: 18px;
            border: 2px dashed #1e293b;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            position: relative;
            overflow: hidden;
            transition: all 0.3s;
        }
        .preview-area.generating {
            border-color: rgba(244,63,94,0.3);
            background: rgba(244,63,94,0.02);
        }
        .preview-area.result {
            border-color: rgba(34,197,94,0.3);
            border-style: solid;
        }

        /* States */
        .empty-state { text-align: center; padding: 50px; }
        .empty-state .icon { font-size: 52px; margin-bottom: 14px; opacity: 0.3; }
        .empty-state p { color: #475569; font-size: 15px; font-weight: 500; }
        .empty-state .sub { color: #334155; font-size: 12px; margin-top: 4px; }

        .generating-state {
            display: none;
            text-align: center;
            padding: 40px;
            width: 100%;
        }
        .spinner-wrap {
            position: relative;
            width: 90px;
            height: 90px;
            margin: 0 auto 24px;
        }
        .spinner-ring {
            position: absolute;
            inset: 0;
            border: 3px solid #1e293b;
            border-radius: 50%;
        }
        .spinner-active {
            position: absolute;
            inset: 0;
            border: 3px solid transparent;
            border-top-color: #f43f5e;
            border-radius: 50%;
            animation: spin 1s linear infinite;
        }
        .spinner-text {
            position: absolute;
            inset: 0;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 15px;
            font-weight: 700;
            color: #f43f5e;
        }
        .gen-status { color: #e2e8f0; font-size: 15px; font-weight: 600; margin: 0 0 4px 0; }
        .gen-sub { color: #475569; font-size: 12px; margin: 0; }
        .progress-track {
            width: 80%;
            height: 4px;
            background: #1e293b;
            border-radius: 2px;
            margin: 24px auto 0;
            overflow: hidden;
        }
        .progress-fill {
            height: 100%;
            width: 0%;
            background: linear-gradient(90deg, #f43f5e, #fb923c);
            border-radius: 2px;
            transition: width 0.4s ease;
        }

        /* Result State */
        .result-state {
            display: none;
            width: 100%;
            height: 100%;
            position: relative;
        }
        .video-placeholder {
            width: 100%;
            height: 100%;
            background: linear-gradient(135deg, #1a1a2e, #16213e);
            display: flex;
            align-items: center;
            justify-content: center;
            position: relative;
        }
        .play-overlay {
            position: absolute;
            inset: 0;
            background: rgba(0,0,0,0.3);
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: background 0.2s;
        }
        .play-overlay:hover { background: rgba(0,0,0,0.5); }
        .play-btn {
            width: 64px;
            height: 64px;
            border-radius: 50%;
            background: rgba(255,255,255,0.95);
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 26px;
            color: #0a0a0f;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
        }
        .result-actions {
            position: absolute;
            bottom: 0;
            left: 0;
            right: 0;
            padding: 14px;
            background: linear-gradient(transparent, rgba(0,0,0,0.8));
            display: flex;
            gap: 10px;
            justify-content: flex-end;
        }
        .action-btn {
            padding: 8px 14px;
            border: 1px solid rgba(255,255,255,0.2);
            border-radius: 10px;
            background: rgba(255,255,255,0.1);
            color: white;
            font-size: 12px;
            cursor: pointer;
            backdrop-filter: blur(4px);
            transition: all 0.2s;
            font-family: inherit;
        }
        .action-btn:hover { background: rgba(255,255,255,0.2); }

        /* Gallery */
        .gallery-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 14px;
        }
        .gallery-grid {
            display: grid;
            grid-template-columns: 1fr 1fr 1fr;
            gap: 10px;
        }
        .gallery-item {
            aspect-ratio: 16/9;
            background: linear-gradient(135deg, #1e1e2e, #2d2d44);
            border-radius: 12px;
            border: 1px solid rgba(255,255,255,0.05);
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: all 0.2s;
            position: relative;
            overflow: hidden;
        }
        .gallery-item:hover {
            transform: scale(1.04);
            border-color: rgba(244,63,94,0.3);
        }
        .gallery-label {
            position: absolute;
            bottom: 0;
            left: 0;
            right: 0;
            padding: 8px;
            background: linear-gradient(transparent, rgba(0,0,0,0.7));
            font-size: 10px;
            color: #94a3b8;
            text-align: center;
        }

        /* Info Bar */
        .info-bar {
            margin-top: 28px;
            padding: 16px 20px;
            background: rgba(244,63,94,0.05);
            border: 1px solid rgba(244,63,94,0.1);
            border-radius: 14px;
            display: flex;
            align-items: center;
            gap: 14px;
        }
        .info-bar .icon { font-size: 22px; }
        .info-bar h4 { margin: 0; font-size: 14px; color: #e2e8f0; font-weight: 600; }
        .info-bar p { margin: 2px 0 0 0; font-size: 12px; color: #64748b; }

        @keyframes spin { to { transform: rotate(360deg); } }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🎬 CinemaAI</h1>
            <p>Text-to-Video · Image-to-Video · Video-to-Video</p>
        </div>

        <div class="tabs">
            <button class="tab active" onclick="setMode('text', this)">📝 Text to Video</button>
            <button class="tab" onclick="setMode('image', this)">🖼️ Image to Video</button>
            <button class="tab" onclick="setMode('video', this)">🎞️ Video to Video</button>
        </div>

        <div class="grid">
            <!-- Controls -->
            <div>
                <div class="form-group">
                    <label>Prompt</label>
                    <textarea id="prompt-input" placeholder="A cinematic drone shot flying over a misty mountain valley at sunrise, golden light breaking through clouds..."></textarea>
                    <div class="hint-row">
                        <span class="hint" id="prompt-hint">✨ AI-enhanced prompt active</span>
                        <span class="hint" id="char-count">0 / 500</span>
                    </div>
                </div>

                <div class="form-group">
                    <label>Negative Prompt <span style="color:#475569;font-weight:400">(Optional)</span></label>
                    <input type="text" id="negative-prompt" placeholder="blurry, low quality, distorted, watermark...">
                </div>

                <div class="row">
                    <div class="form-group">
                        <label>Duration</label>
                        <select id="duration">
                            <option value="4">4 seconds</option>
                            <option value="8" selected>8 seconds</option>
                            <option value="16">16 seconds</option>
                        </select>
                    </div>
                    <div class="form-group">
                        <label>Resolution</label>
                        <select id="resolution">
                            <option value="480">480p</option>
                            <option value="720" selected>720p HD</option>
                            <option value="1080">1080p FHD</option>
                        </select>
                    </div>
                </div>

                <div class="row">
                    <div class="form-group">
                        <label>Aspect Ratio</label>
                        <select id="aspect" onchange="updateAspect()">
                            <option value="16:9" selected>16:9 Widescreen</option>
                            <option value="9:16">9:16 Vertical</option>
                            <option value="1:1">1:1 Square</option>
                            <option value="4:3">4:3 Classic</option>
                        </select>
                    </div>
                    <div class="form-group">
                        <label>Motion</label>
                        <select id="motion">
                            <option value="low">Low (Static)</option>
                            <option value="medium" selected>Medium (Balanced)</option>
                            <option value="high">High (Dynamic)</option>
                        </select>
                    </div>
                </div>

                <div class="form-group">
                    <label>Cinematic Style</label>
                    <div class="presets">
                        <button class="preset-btn active" onclick="setStyle(this, 'cinematic')">🎬 Cinematic</button>
                        <button class="preset-btn" onclick="setStyle(this, 'anime')">🎨 Anime</button>
                        <button class="preset-btn" onclick="setStyle(this, '3d')">🧊 3D Render</button>
                        <button class="preset-btn" onclick="setStyle(this, 'realistic')">📷 Photoreal</button>
                        <button class="preset-btn" onclick="setStyle(this, 'cyberpunk')">🌃 Cyberpunk</button>
                    </div>
                </div>

                <button class="generate-btn" id="generate-btn" onclick="startGeneration()">
                    <span id="btn-text">✨ Generate Video</span>
                    <span id="btn-icon">⚡</span>
                </button>
            </div>

            <!-- Preview -->
            <div>
                <div class="preview-area" id="preview-area">
                    <div class="empty-state" id="empty-state">
                        <div class="icon">🎬</div>
                        <p>Your video will appear here</p>
                        <div class="sub">Enter a prompt and click generate</div>
                    </div>

                    <div class="generating-state" id="generating-state">
                        <div class="spinner-wrap">
                            <div class="spinner-ring"></div>
                            <div class="spinner-active"></div>
                            <div class="spinner-text" id="progress-text">0%</div>
                        </div>
                        <p class="gen-status" id="gen-status">Initializing model...</p>
                        <p class="gen-sub" id="gen-substatus">Loading diffusion pipeline</p>
                        <div class="progress-track">
                            <div class="progress-fill" id="progress-bar"></div>
                        </div>
                    </div>

                    <div class="result-state" id="result-state">
                        <div class="video-placeholder">
                            <div style="text-align:center">
                                <div style="font-size:64px;margin-bottom:12px">🎞️</div>
                                <p style="color:#94a3b8;font-size:15px;font-weight:600;margin:0">Video Generated</p>
                                <p style="color:#475569;font-size:12px;margin-top:4px">8s · 720p · Cinematic</p>
                            </div>
                            <div class="play-overlay">
                                <div class="play-btn">▶</div>
                            </div>
                        </div>
                        <div class="result-actions">
                            <button class="action-btn">⬇️ Download</button>
                            <button class="action-btn">🔄 Regenerate</button>
                        </div>
                    </div>
                </div>

                <div style="margin-top:20px">
                    <div class="gallery-header">
                        <label>Recent Generations</label>
                        <span class="hint">3 videos</span>
                    </div>
                    <div class="gallery-grid">
                        <div class="gallery-item">
                            <span style="font-size:24px">🏔️</span>
                            <div class="gallery-label">Mountain flyover</div>
                        </div>
                        <div class="gallery-item">
                            <span style="font-size:24px">🌊</span>
                            <div class="gallery-label">Ocean waves</div>
                        </div>
                        <div class="gallery-item">
                            <span style="font-size:24px">🌃</span>
                            <div class="gallery-label">Neon city</div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="info-bar">
            <span class="icon">💡</span>
            <div>
                <h4>Pro Tip: Add camera movements</h4>
                <p>Try "slow dolly zoom in", "aerial drone shot", or "handheld camera shake" for dynamic results.</p>
            </div>
        </div>
    </div>

    <script>
        let isGenerating = false;
        let currentStyle = 'cinematic';

        const generationSteps = [
            { status: 'Initializing model...', sub: 'Loading diffusion pipeline', progress: 5 },
            { status: 'Analyzing prompt...', sub: 'Optimizing with AI enhancement', progress: 15 },
            { status: 'Generating keyframes...', sub: 'Frame 1 of 48', progress: 25 },
            { status: 'Generating keyframes...', sub: 'Frame 12 of 48', progress: 40 },
            { status: 'Generating keyframes...', sub: 'Frame 24 of 48', progress: 55 },
            { status: 'Generating keyframes...', sub: 'Frame 36 of 48', progress: 70 },
            { status: 'Generating keyframes...', sub: 
