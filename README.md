[Untitled-1.html](https://github.com/user-attachments/files/35042198/Untitled-1.html)
<!DOCTYPE html>
<html lang="vi" class="dark">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Heart Code Studio ASCII </title>
  <script src="https://cdn.tailwindcss.com"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500;700&display=swap" rel="stylesheet">
  <script>
    tailwind.config = {
      darkMode: 'class',
      theme: {
        extend: {
          fontFamily: {
            sans: ['Inter', 'sans-serif'],
            mono: ['JetBrains Mono', 'Courier New', 'monospace'],
          },
          colors: {
            studio: {
              bg: '#08080c',
              panel: '#111119',
              card: '#181824',
              border: '#262638',
              pink: '#ff3b6b',
              green: '#00ff88',
              blue: '#38bdf8',
              amber: '#fbbf24'
            }
          }
        }
      }
    }
  </script>
  <style>
    ::-webkit-scrollbar { width: 6px; height: 6px; }
    ::-webkit-scrollbar-track { background: #08080c; }
    ::-webkit-scrollbar-thumb { background: #262638; border-radius: 9999px; }
    ::-webkit-scrollbar-thumb:hover { background: #3b3b54; }

    .stage-grid {
      background-image: radial-gradient(#1f1f30 1px, transparent 1px);
      background-size: 22px 22px;
    }

    input[type="range"] {
      -webkit-appearance: none;
      background: #262638;
      height: 5px;
      border-radius: 999px;
    }
    input[type="range"]::-webkit-slider-thumb {
      -webkit-appearance: none;
      width: 14px;
      height: 14px;
      border-radius: 50%;
      background: var(--accent-color, #ff3b6b);
      cursor: pointer;
      box-shadow: 0 0 8px var(--accent-color, #ff3b6b);
    }

    pre#asciiStage {
      line-height: 0.92;
      letter-spacing: 1.4px;
      user-select: none;
    }

    /* Đảm bảo khung QR trắng rõ nét để camera quét dễ dàng */
    #qrcodeBox img, #qrcodeBox canvas {
      margin: 0 auto;
      border-radius: 8px;
    }
  </style>
</head>
<body class="bg-studio-bg text-slate-200 font-sans h-screen flex flex-col overflow-hidden" style="--accent-color: #ff3b6b;">

  <!-- ===================================================================== -->
  <!-- THANH ĐIỀU HƯỚNG TRÊN CÙNG (TOP BAR)                                  -->
  <!-- ===================================================================== -->
  <header id="topBar" class="h-[60px] bg-studio-panel border-b border-studio-border px-3 md:px-5 flex items-center justify-between gap-2 shrink-0 z-30">
    <!-- Trái: Logo -->
    <div class="flex items-center gap-2.5 shrink-0">
      <div class="w-8 h-8 rounded-xl bg-gradient-to-br from-studio-pink to-purple-600 flex items-center justify-center text-white font-mono font-bold shadow-lg shadow-studio-pink/25">
        ◈
      </div>
      <div class="flex flex-col">
        <span class="font-mono font-bold text-sm md:text-base tracking-tight text-white">Heart Code Studio ASCII</span>
        <span class="hidden sm:inline text-[10px] text-slate-400 font-mono">3D Art & Interactive Greeting Cards</span>
      </div>
    </div>

    <!-- Giữa: 2 Tab chuyển đổi chế độ chính -->
    <nav id="mainModeNav" class="flex items-center bg-studio-bg p-1 rounded-xl border border-studio-border shrink-0">
      <button onclick="switchMainMode('card')" id="nav-card" class="px-3 md:px-4 py-1.5 rounded-lg text-xs font-semibold whitespace-nowrap transition-all flex items-center gap-1.5 bg-studio-card text-white border border-studio-border shadow-sm">
        <span class="text-studio-pink">💌</span>
        <span>Tạo Thiệp</span>
      </button>
      <button onclick="switchMainMode('studio')" id="nav-studio" class="px-3 md:px-4 py-1.5 rounded-lg text-xs font-semibold whitespace-nowrap transition-all flex items-center gap-1.5 text-slate-400 hover:text-white border border-transparent">
        <span>🎨</span>
        <span>Thư viện</span>
      </button>
    </nav>

    <!-- Phải: Xem thử góc nhìn người nhận & Chia sẻ Link/QR -->
    <div class="flex items-center gap-2 shrink-0">
      <button onclick="toggleRecipientPreview(true)" class="px-3 py-1.5 rounded-xl bg-studio-card hover:bg-[#222234] border border-studio-border text-xs font-medium text-slate-200 flex items-center gap-1.5 transition" title="Mô phỏng giao diện khi người nhận mở Link hoặc quét Mã QR">
        <span>👁️</span>
        <span class="hidden lg:inline"></span>
      </button>
      <button onclick="openShareModal()" class="px-3.5 py-1.5 rounded-xl bg-studio-pink hover:bg-rose-500 text-white text-xs font-semibold shadow-lg shadow-studio-pink/25 flex items-center gap-1.5 transition">
        <span>🔗</span>
        <span class="hidden sm:inline">Chia sẻ Link & QR</span>
      </button>
    </div>
  </header>

  <!-- Thanh nổi thoát chế độ Xem thử Người nhận -->
  <div id="recipientExitBar" class="hidden fixed top-4 right-4 z-50 flex items-center gap-2 bg-studio-panel/90 backdrop-blur-md border border-studio-border px-3.5 py-2 rounded-full shadow-2xl">
    <span class="text-[11px] font-mono text-slate-300"> </span>
    <button onclick="toggleRecipientPreview(false)" class="px-2.5 py-1 rounded-full bg-studio-pink text-white text-[11px] font-semibold hover:bg-rose-500 transition">
      ✕ Quay lại 
    </button>
  </div>

  <!-- ===================================================================== -->
  <!-- KHÔNG GIAN LÀM VIỆC CHÍNH                                             -->
  <!-- ===================================================================== -->
  <div class="flex-1 flex overflow-hidden relative">

    <!-- =================================================================== -->
    <!-- CỘT TRÁI (CHỈ HIỂN THỊ Ở CHẾ ĐỘ 2: KHO TÁC PHẨM & SÁNG TẠO)         -->
    <!-- =================================================================== -->
    <aside id="studioLeftPanel" class="hidden w-full md:w-[360px] bg-studio-panel border-r border-studio-border flex-col h-full shrink-0 z-20">
      <!-- 3 Tab nguồn dữ liệu -->
      <div class="p-3 border-b border-studio-border">
        <div class="grid grid-cols-3 gap-1 bg-studio-bg p-1 rounded-xl border border-studio-border text-[11px] font-semibold">
          <button onclick="switchStudioSource('presets')" id="srcTab-presets" class="py-1.5 px-2 rounded-lg bg-studio-card text-white border border-studio-border transition">
            Tab A: Mẫu sẵn
          </button>
          <button onclick="switchStudioSource('html')" id="srcTab-html" class="py-1.5 px-2 rounded-lg text-slate-400 hover:text-white border border-transparent transition">
            Tab B: Code HTML
          </button>
          <button onclick="switchStudioSource('image')" id="srcTab-image" class="py-1.5 px-2 rounded-lg text-slate-400 hover:text-white border border-transparent transition">
            Tab C: Ảnh → ASCII
          </button>
        </div>
      </div>

      <!-- Nội dung từng Tab nguồn -->
      <div class="flex-1 overflow-y-auto p-4 space-y-4">

        <!-- TAB A: MẪU CÓ SẴN -->
        <div id="sourcePane-presets" class="space-y-4">
          <div>
            <h3 class="text-xs font-bold uppercase tracking-wider text-white mb-1">Thư viện mô hình 3D & Hiệu ứng</h3>
            <p class="text-[11px] text-slate-400">Chọn mẫu để xem trực tiếp và tinh chỉnh thông số.</p>
          </div>

          <div class="grid grid-cols-2 gap-2.5">
            <button onclick="selectBuiltInModel('bouquet')" data-model="bouquet" class="model-card p-3 rounded-2xl bg-studio-card border-2 border-studio-pink text-left transition hover:bg-[#202030]">
              <div class="text-2xl mb-1.5">💐</div>
              <div class="text-xs font-bold text-white">Bó Hoa 3D</div>
              <div class="text-[10px] text-slate-400 font-mono">Hoa nở từ gốc + Nơ ruy băng</div>
            </button>
            <button onclick="selectBuiltInModel('xmas')" data-model="xmas" class="model-card p-3 rounded-2xl bg-studio-bg border border-studio-border text-left transition hover:bg-studio-card">
              <div class="text-2xl mb-1.5">🎄</div>
              <div class="text-xs font-bold text-white">Cây Thông Noel</div>
              <div class="text-[10px] text-slate-400 font-mono">Đèn nháy + Hộp quà & Tuyết</div>
            </button>
            <button onclick="selectBuiltInModel('blackhole')" data-model="blackhole" class="model-card p-3 rounded-2xl bg-studio-bg border border-studio-border text-left transition hover:bg-studio-card">
              <div class="text-2xl mb-1.5">🕳️</div>
              <div class="text-xs font-bold text-white">Hố Đen 3D</div>
              <div class="text-[10px] text-slate-400 font-mono">Singularity + Thấu kính hấp dẫn</div>
            </button>
            <button onclick="selectBuiltInModel('multilang_heart')" data-model="multilang_heart" class="model-card p-3 rounded-2xl bg-studio-bg border border-studio-border text-left transition hover:bg-studio-card">
              <div class="text-2xl mb-1.5">💖</div>
              <div class="text-xs font-bold text-white">Trái Tim Đa Ngôn Ngữ</div>
              <div class="text-[10px] text-slate-400 font-mono">I Love U bằng 80+ thứ tiếng</div>
            </button>
            <button onclick="selectBuiltInModel('bouquet_v2')" data-model="bouquet_v2" class="model-card p-3 rounded-2xl bg-studio-bg border border-studio-border text-left transition hover:bg-studio-card">
              <div class="text-2xl mb-1.5">🌹</div>
              <div class="text-xs font-bold text-white">Bó Hoa Nghệ Thuật v2</div>
              <div class="text-[10px] text-slate-400 font-mono">Hồng, Cúc, Tulip + Giấy xếp nếp</div>
            </button>
            <button onclick="selectBuiltInModel('multilang_heart_v2')" data-model="multilang_heart_v2" class="model-card p-3 rounded-2xl bg-studio-bg border border-studio-border text-left transition hover:bg-studio-card">
              <div class="text-2xl mb-1.5">💗</div>
              <div class="text-xs font-bold text-white">Tim Ngôn Ngữ 3D v2</div>
              <div class="text-[10px] text-slate-400 font-mono">Bề mặt ẩn 3D + Bloom đa tầng</div>
            </button>
          </div>

          <!-- Danh sách tác phẩm HTML đã lưu -->
          <div class="pt-2 border-t border-studio-border">
            <div class="text-[11px] font-bold uppercase tracking-wider text-slate-400 mb-2">Mẫu đã lưu</div>
            <div id="savedHtmlList" class="space-y-1.5 max-h-36 overflow-y-auto">
              <!-- Render tự động bằng JS -->
            </div>
          </div>
        </div>

        <!-- TAB B: NHÚNG CODE HTML SANDBOX -->
        <div id="sourcePane-html" class="hidden space-y-3">
          <div>
            <h3 class="text-xs font-bold uppercase tracking-wider text-white mb-0.5">Nhúng Code HTML / Canvas</h3>
            <p class="text-[11px] text-slate-400">Dán mã HTML/JS hoặc tải tệp .html để chạy trực tiếp.</p>
          </div>

          <input id="customHtmlTitle" type="text" value="♥ ASCII HTML SANDBOX ♥" placeholder="Tên tác phẩm..." class="w-full bg-studio-bg border border-studio-border rounded-xl px-3 py-2 text-xs text-white focus:outline-none focus:border-studio-pink">

          <textarea id="htmlCodeEditor" rows="8" placeholder="Dán mã HTML vào đây..." class="w-full bg-studio-bg border border-studio-border rounded-xl p-2.5 font-mono text-[11px] text-emerald-300 focus:outline-none focus:border-studio-pink"><!DOCTYPE html>
<html>
<body style="margin:0;background:#08080c;color:#ff3b6b;display:flex;align-items:center;justify-content:center;height:100vh;font-family:monospace;">
  <h2 style="text-shadow:0 0 12px #ff3b6b;">♥ ASCII HTML SANDBOX ♥</h2>
</body>
</html></textarea>

          <div class="grid grid-cols-2 gap-2">
            <button onclick="runCustomHtmlCode(false)" class="py-2 px-3 rounded-xl bg-studio-card hover:bg-[#222234] border border-studio-border text-xs font-semibold text-white transition">
              ▶ Chạy thử
            </button>
            <button onclick="runCustomHtmlCode(true)" class="py-2 px-3 rounded-xl bg-studio-pink/20 hover:bg-studio-pink/30 border border-studio-pink/50 text-xs font-semibold text-pink-200 transition">
              💾 Lưu tác phẩm
            </button>
          </div>

          <label class="block w-full py-2 px-3 rounded-xl bg-studio-bg hover:bg-studio-card border border-dashed border-studio-border text-center text-[11px] text-slate-300 cursor-pointer transition">
            📂 Tải tệp .HTML từ máy tính
            <input type="file" accept=".html,.htm" onchange="handleHtmlFileUpload(event)" class="hidden">
          </label>
        </div>

        <!-- TAB C: ẢNH SANG ASCII -->
        <div id="sourcePane-image" class="hidden space-y-3.5">
          <div class="flex items-center justify-between">
            <div>
              <h3 class="text-xs font-bold uppercase tracking-wider text-white mb-0.5">Ảnh → Nghệ thuật ASCII</h3>
              <p class="text-[10px] text-slate-400 font-mono">Engine: ascii-magic-art glyph looks</p>
            </div>
            <button onclick="copyAsciiArtText()" class="px-2.5 py-1 rounded-lg bg-studio-card hover:bg-[#222234] border border-studio-border text-[10px] font-semibold text-slate-200 transition" title="Sao chép văn bản ASCII">
              📋 Copy ASCII
            </button>
          </div>

          <label class="block w-full py-3.5 px-3 rounded-2xl bg-studio-card hover:bg-[#202030] border-2 border-dashed border-studio-pink/60 text-center cursor-pointer transition">
            <div class="text-lg mb-0.5">🖼️</div>
            <div class="text-xs font-semibold text-white">Chọn ảnh từ máy (JPG, PNG, WebP)</div>
            <div class="text-[10px] text-slate-400 mt-0.5">Tự động chuyển đổi sang ký tự theo thời gian thực</div>
            <input type="file" accept="image/*" onchange="handleImageUpload(event)" class="hidden">
          </label>

          <button onclick="loadSamplePortrait()" class="w-full py-1.5 px-3 rounded-xl bg-studio-bg hover:bg-studio-card border border-studio-border text-xs text-slate-300 transition">
            ✨ Thử với ảnh minh họa có sẵn
          </button>

          <!-- Bộ chọn Phong cách ký tự (Look) từ ascii-magic-art -->
          <div class="space-y-1.5">
            <div class="flex justify-between items-center text-xs">
              <span class="text-slate-300 font-medium">Bộ ký tự (Glyph Look)</span>
              <span id="imgLookPreview" class="font-mono text-[10px] text-studio-pink">.,-~:;=!*#$@</span>
            </div>
            <select id="selectAsciiLook" onchange="setAsciiLook(this.value)" class="w-full bg-studio-bg border border-studio-border rounded-xl px-2.5 py-2 text-xs text-white focus:outline-none focus:border-studio-pink">
              <option value="ascii">✦ ASCII Chuẩn (.,-~:;=!*#$@)</option>
              <option value="dense">✦ Dense ASCII Siêu chi tiết (70 mức sáng)</option>
              <option value="dots">● Halftone Dots (·•●)</option>
              <option value="blocks">█ Blocks Khối đổ bóng (░▒▓█)</option>
              <option value="squares">■ Squares Ô vuông (·▪■)</option>
              <option value="dither">▞ Dither Lưới đan (▚▞ + Bayer)</option>
              <option value="numbers">09 Numbers Ma trận số (1723456980)</option>
              <option value="binary">01 Binary Nhị phân (01)</option>
              <option value="letters">Aa Letters Chữ cái (ilcoeaxkdbAHMW)</option>
              <option value="lines">═ Lines Nét kẻ (╌─━═)</option>
              <option value="custom">✎ Tự nhập bộ ký tự riêng...</option>
            </select>

            <div id="customCharsBox" class="hidden pt-1">
              <input id="inputCustomChars" type="text" value=" .oO@" maxlength="32" oninput="renderImageToAscii()" placeholder="Nhập ký tự từ nhạt đến đậm..." class="w-full bg-studio-bg border border-studio-border rounded-xl px-2.5 py-1.5 font-mono text-xs text-emerald-300 focus:outline-none focus:border-studio-pink">
            </div>
          </div>

          <div class="space-y-3 pt-1">
            <div>
              <div class="flex justify-between text-xs mb-1">
                <span class="text-slate-300">Độ chi tiết (Số cột ký tự)</span>
                <span id="imgColsLbl" class="font-mono text-studio-pink">92 cột</span>
              </div>
              <input type="range" min="40" max="150" step="4" value="92" oninput="imgAsciiCols = parseInt(this.value); document.getElementById('imgColsLbl').textContent = this.value + ' cột'; renderImageToAscii();" class="w-full">
            </div>

            <div>
              <div class="flex justify-between text-xs mb-1">
                <span class="text-slate-300">Độ tương phản</span>
                <span id="imgContrastLbl" class="font-mono text-studio-pink">120%</span>
              </div>
              <input type="range" min="50" max="220" step="5" value="120" oninput="imgContrast = parseInt(this.value); document.getElementById('imgContrastLbl').textContent = this.value + '%'; renderImageToAscii();" class="w-full">
            </div>

            <!-- Công tắc Giữ màu gốc / Đơn sắc & Đảo ngược sáng tối -->
            <div class="grid grid-cols-2 gap-2">
              <div class="bg-studio-bg p-2 rounded-xl border border-studio-border flex flex-col gap-1.5">
                <span class="text-[10px] text-slate-400">Màu ký tự</span>
                <div class="grid grid-cols-2 bg-studio-panel p-0.5 rounded-lg border border-studio-border text-[10px]">
                  <button onclick="setImgColorMode(false)" id="btnImgMono" class="py-1 rounded-md bg-studio-card text-white font-medium">Đơn sắc</button>
                  <button onclick="setImgColorMode(true)" id="btnImgFullColor" class="py-1 rounded-md text-slate-400 hover:text-white font-medium">Màu gốc</button>
                </div>
              </div>

              <div class="bg-studio-bg p-2 rounded-xl border border-studio-border flex flex-col gap-1.5">
                <span class="text-[10px] text-slate-400">Nền sáng / tối</span>
                <button onclick="toggleImgInvert()" id="btnImgInvert" class="w-full py-1 rounded-lg bg-studio-panel hover:bg-studio-card border border-studio-border text-[10px] text-slate-200 font-medium transition">
                  🌓 Đảo sáng: Tắt
                </button>
              </div>
            </div>
          </div>
        </div>

      </div>

      <!-- Thanh hành động dưới cùng bên trái của Chế độ 2 -->
      <div class="p-3.5 border-t border-studio-border bg-studio-bg/80">
        <button onclick="applyAssetToGreetingCard()" class="w-full py-2.5 px-4 rounded-xl bg-gradient-to-r from-studio-pink to-rose-500 hover:from-rose-500 hover:to-pink-500 text-white text-xs font-bold shadow-lg shadow-studio-pink/25 flex items-center justify-center gap-2 transition">
          <span>Dùng tác phẩm này làm Thiệp</span>
          <span>→</span>
        </button>
      </div>
    </aside>

    <!-- =================================================================== -->
    <!-- SÂN KHẤU TRUNG TÂM (CENTER STAGE - 3D / HTML SANDBOX / IMAGE ASCII)  -->
    <!-- =================================================================== -->
    <main id="centerStage" class="flex-1 relative stage-grid flex flex-col items-center justify-center overflow-hidden">

      <!-- Nhãn Tên người nhận (Hiển thị ở Chế độ 1 & Chế độ Người nhận) -->
      <div id="stageRecipientHeader" class="absolute top-4 left-4 right-4 z-20 flex flex-wrap items-center justify-between gap-2 pointer-events-none">
        <div class="bg-studio-panel/85 backdrop-blur-md border border-studio-border px-3.5 py-1.5 rounded-full flex items-center gap-2 shadow-lg">
          <span id="stageThemeDot" class="w-2 h-2 rounded-full bg-studio-pink animate-ping"></span>
          <span class="text-xs text-slate-400">Gửi tặng:</span>
          <span id="stageRecipientName" class="text-xs font-bold text-white">my love</span>
        </div>

        <div id="stageControlsHint" class="hidden sm:flex items-center gap-2 bg-studio-panel/75 backdrop-blur-md border border-studio-border px-3 py-1 rounded-full text-[11px] font-mono text-slate-400">
          <span>🖱️ Kéo trái: Xoay 360°</span>
          <span>•</span>
          <span>Cuộn: Thu phóng</span>
        </div>
      </div>

      <!-- 1. Lớp hiển thị Mẫu 3D Tương Tác (4 Tác phẩm tích hợp) -->
      <div id="presetStageWrapper" class="w-full h-full flex items-center justify-center overflow-hidden">
        <iframe id="presetStageFrame" sandbox="allow-scripts" class="w-full h-full border-0"></iframe>
      </div>

      <!-- 2. Lớp hiển thị Ảnh ASCII -->
      <div id="asciiContainer" class="hidden w-full h-full flex items-center justify-center overflow-hidden p-4">
        <pre id="asciiStage" class="font-mono text-[8px] sm:text-[10px] md:text-[12px] text-studio-pink transition-colors duration-200" style="text-shadow: 0 0 10px rgba(255,59,107,0.45);"></pre>
      </div>

      <!-- 3. Lớp hiển thị Mã HTML Sandbox (Chỉ bật khi chạy Code HTML ở Tab B) -->
      <div id="htmlSandboxWrapper" class="hidden w-full h-full max-w-3xl max-h-[72vh] rounded-2xl overflow-hidden border border-studio-border shadow-2xl bg-black relative">
        <iframe id="htmlSandboxFrame" sandbox="allow-scripts" class="w-full h-full border-0"></iframe>
      </div>

      <!-- Nút nổi Tương tác [R] + Hộp lời chúc (Chế độ 1) -->
      <div id="cardStageFooter" class="absolute bottom-4 left-4 right-4 z-20 flex flex-col items-center gap-2.5 pointer-events-none">
        <!-- Cụm nút tương tác nhanh (Tự động ẩn khi ở góc nhìn Người nhận) -->
        <div id="stageQuickControls" class="flex items-center gap-2 pointer-events-auto">
          <button onclick="triggerModelReplay()" id="btnMorphR" class="px-4 py-1.5 rounded-full bg-studio-panel/90 hover:bg-studio-card backdrop-blur-md border border-studio-pink/60 text-xs font-mono font-semibold text-white shadow-lg shadow-studio-pink/15 flex items-center gap-2 transition active:scale-95">
            <span>✨ Start</span>
            <kbd class="px-1.5 py-0.5 text-[10px] rounded bg-studio-pink text-white font-bold">R</kbd>
          </button>

          <button onclick="toggleMusicPlay()" id="btnStageMusic" class="px-3 py-1.5 rounded-full bg-studio-panel/90 hover:bg-studio-card backdrop-blur-md border border-studio-border text-xs font-mono text-slate-300 flex items-center gap-1.5 transition">
            <span id="musicWaveIcon">🎵</span>
            <span id="stageMusicLabel">Phát nhạc</span>
          </button>
        </div>

        <!-- Khung lời chúc hiển thị tĩnh hoàn chỉnh -->
        <div class="w-full max-w-xl bg-studio-panel/85 backdrop-blur-xl border border-white/10 px-5 py-3 rounded-2xl shadow-2xl text-center pointer-events-auto">
          <p id="typewriterOutput" class="font-mono text-xs md:text-sm text-slate-200 leading-relaxed min-h-[22px]"></p>
        </div>
      </div>

    </main>

    <!-- =================================================================== -->
    <!-- CỘT PHẢI: BẢNG ĐIỀU KHIỂN THIỆP (CREATOR PANEL - CHẾ ĐỘ 1)          -->
    <!-- =================================================================== -->
    <aside id="cardRightPanel" class="w-full md:w-[340px] bg-studio-panel border-l border-studio-border h-full overflow-y-auto shrink-0 z-20 p-4 space-y-4">
      
      <div class="flex items-center justify-between border-b border-studio-border pb-3">
        <div>
          <h2 class="text-xs font-bold uppercase tracking-wider text-white">Thiết kế Thiệp </h2>
          <p class="text-[11px] text-slate-400 mt-0.5">Tùy chỉnh nội dung, màu sắc & âm nhạc</p>
        </div>
      </div>

      <!-- 1. Mô hình đang dùng -->
      <div class="bg-studio-bg p-3 rounded-2xl border border-studio-border space-y-2">
        <div class="flex items-center justify-between">
          <span class="text-[11px] font-semibold text-slate-400">Tác phẩm trên thiệp:</span>
          <button onclick="switchMainMode('studio')" class="text-[11px] text-studio-blue hover:underline font-medium">
            + Kho mẫu / Ảnh / Code →
          </button>
        </div>
        <div class="grid grid-cols-2 gap-1.5 text-xs">
          <button onclick="quickSelectCardModel('bouquet')" id="qModel-bouquet" class="q-model-btn py-1.5 px-2.5 rounded-xl bg-studio-card border border-studio-pink text-white font-medium truncate text-left">
            💐 Bó hoa 3D
          </button>
          <button onclick="quickSelectCardModel('xmas')" id="qModel-xmas" class="q-model-btn py-1.5 px-2.5 rounded-xl bg-studio-panel border border-studio-border text-slate-400 hover:text-white truncate text-left">
            🎄 Cây thông Noel
          </button>
          <button onclick="quickSelectCardModel('blackhole')" id="qModel-blackhole" class="q-model-btn py-1.5 px-2.5 rounded-xl bg-studio-panel border border-studio-border text-slate-400 hover:text-white truncate text-left">
            🕳️ Hố đen 3D
          </button>
          <button onclick="quickSelectCardModel('multilang_heart')" id="qModel-multilang_heart" class="q-model-btn py-1.5 px-2.5 rounded-xl bg-studio-panel border border-studio-border text-slate-400 hover:text-white truncate text-left">
            💖 Tim đa ngôn ngữ
          </button>
          <button onclick="quickSelectCardModel('bouquet_v2')" id="qModel-bouquet_v2" class="q-model-btn py-1.5 px-2.5 rounded-xl bg-studio-panel border border-studio-border text-slate-400 hover:text-white truncate text-left">
            🌹 Bó hoa v2
          </button>
          <button onclick="quickSelectCardModel('multilang_heart_v2')" id="qModel-multilang_heart_v2" class="q-model-btn py-1.5 px-2.5 rounded-xl bg-studio-panel border border-studio-border text-slate-400 hover:text-white truncate text-left">
            💗 Tim ngôn ngữ v2
          </button>
        </div>
        <div id="activeCustomAssetBadge" class="hidden text-[11px] font-mono text-emerald-400 bg-emerald-500/10 border border-emerald-500/30 px-2.5 py-1.5 rounded-xl">
          ✨ Đang dùng tác phẩm từ Thư Viện
        </div>
      </div>

      <!-- 2. Thông tin người nhận & Lời chúc -->
      <div class="bg-studio-bg p-3 rounded-2xl border border-studio-border space-y-2.5">
        <div>
          <label class="block text-[11px] font-semibold text-slate-400 mb-1">Tên người nhận</label>
          <input id="inputRecipient" type="text" value="my love" oninput="updateCardTexts()" placeholder="Nhập tên người nhận..." class="w-full bg-studio-panel border border-studio-border rounded-xl px-2.5 py-1.5 text-xs text-white focus:outline-none focus:border-studio-pink">
        </div>

        <div>
          <label class="block text-[11px] font-semibold text-slate-400 mb-1">Chữ hiệu ứng 3D trên sân khấu</label>
          <input id="inputMorphWord" type="text" value="FOR YOU" maxlength="28" oninput="updateModelCaptionText(this.value)" placeholder="VD: FOR YOU, HAPPY BIRTHDAY..." class="w-full bg-studio-panel border border-studio-border rounded-xl px-2.5 py-1.5 font-mono text-xs text-pink-200 focus:outline-none focus:border-studio-pink">
        </div>

        <div id="heartWordsControl" class="hidden">
          <div class="flex items-center justify-between mb-1">
            <label class="text-[11px] font-semibold text-slate-400">Chữ dệt bên trong trái tim</label>
            <button type="button" onclick="resetHeartInnerWords()" class="text-[10px] text-studio-blue hover:underline">Mặc định 80+ thứ tiếng</button>
          </div>
          <input id="inputHeartWords" type="text" value="" oninput="updateHeartInnerWords(this.value)" placeholder="VD: MINH ANH ♥ HOÀNG NAM, YÊU EM..." class="w-full bg-studio-panel border border-studio-border rounded-xl px-2.5 py-1.5 font-mono text-xs text-pink-200 focus:outline-none focus:border-studio-pink">
        </div>

        <div>
          <label class="block text-[11px] font-semibold text-slate-400 mb-1">Lời nhắn chúc mừng</label>
          <textarea id="inputLongMessage" rows="3" oninput="startTypewriter(this.value)" placeholder="Nhập lời chúc gửi tặng..." class="w-full bg-studio-panel border border-studio-border rounded-xl p-2 text-xs text-slate-200 focus:outline-none focus:border-studio-pink">Chúc bạn một ngày thật rực rỡ, ngập tràn niềm vui và những điều kỳ diệu nhất! ✨</textarea>
        </div>
      </div>

      <!-- 3. Màu chủ đạo & Tốc độ xoay -->
      <div class="bg-studio-bg p-3 rounded-2xl border border-studio-border space-y-2.5">
        <div>
          <span class="block text-[11px] font-semibold text-slate-400 mb-1.5">Tông màu ánh sáng</span>
          <div class="grid grid-cols-4 gap-1.5 text-[11px]">
            <button onclick="setThemeColor('pink','#ff3b6b','rgba(255,59,107,0.45)')" id="colorBtn-pink" class="color-theme-btn py-1.5 rounded-xl bg-studio-panel border border-[#ff3b6b] text-white font-medium">Hồng</button>
            <button onclick="setThemeColor('green','#00ff88','rgba(0,255,136,0.4)')" id="colorBtn-green" class="color-theme-btn py-1.5 rounded-xl bg-studio-panel border border-studio-border text-slate-300 font-medium">Lục</button>
            <button onclick="setThemeColor('blue','#38bdf8','rgba(56,189,248,0.45)')" id="colorBtn-blue" class="color-theme-btn py-1.5 rounded-xl bg-studio-panel border border-studio-border text-slate-300 font-medium">Xanh</button>
            <button onclick="setThemeColor('amber','#fbbf24','rgba(251,191,36,0.45)')" id="colorBtn-amber" class="color-theme-btn py-1.5 rounded-xl bg-studio-panel border border-studio-border text-slate-300 font-medium">Vàng</button>
          </div>
        </div>

        <div>
          <div class="flex justify-between text-[11px] mb-1">
            <span class="text-slate-400 font-semibold">Tốc độ xoay 3D</span>
            <span id="cardSpeedLbl" class="font-mono text-studio-pink">1.0x</span>
          </div>
          <input id="cardSpeedSlider" type="range" min="0" max="2.5" step="0.1" value="1.0" oninput="updateModelSpeed(this.value)" class="w-full">
        </div>

        <div id="blackholeExtraControls" class="hidden pt-1 border-t border-studio-border">
          <span class="block text-[11px] font-semibold text-slate-400 mb-1.5">Chế độ kết xuất Hố Đen</span>
          <div class="grid grid-cols-3 gap-1.5 text-[11px]">
            <button onclick="setBlackholeRenderMode(0)" id="bhMode-0" class="bh-mode-btn py-1.5 rounded-lg bg-studio-panel border border-studio-border text-slate-400">Chấm tròn</button>
            <button onclick="setBlackholeRenderMode(1)" id="bhMode-1" class="bh-mode-btn py-1.5 rounded-lg bg-studio-panel border border-studio-border text-slate-400">Ô vuông</button>
            <button onclick="setBlackholeRenderMode(2)" id="bhMode-2" class="bh-mode-btn py-1.5 rounded-lg bg-studio-card border border-studio-pink text-white font-semibold">ASCII</button>
          </div>
        </div>
      </div>

      <!-- 4. Nhạc nền thiệp -->
      <div class="bg-studio-bg p-3 rounded-2xl border border-studio-border space-y-2">
        <div class="flex items-center justify-between">
          <span class="text-[11px] font-semibold text-slate-400">Âm nhạc nền</span>
          <button onclick="toggleMusicPlay()" id="panelMusicBtn" class="px-2.5 py-1 rounded-lg bg-studio-card hover:bg-[#222234] border border-studio-border text-[11px] font-semibold text-white transition">
            ▶ Nghe thử
          </button>
        </div>

        <select id="selectPresetMusic" onchange="changePresetTrack(this.value)" class="w-full bg-studio-panel border border-studio-border rounded-xl px-2.5 py-2 text-xs text-white focus:outline-none focus:border-studio-pink">
          <option value="musicbox">🎼 Giai điệu Hộp nhạc Dịu dàng</option>
          <option value="lullaby">🌙 Khúc hát Ru Đêm sao</option>
          <option value="birthday">🎂 Giai điệu Chúc mừng Sinh nhật</option>
        </select>

        <input id="inputMusicUrl" type="text" placeholder="Hoặc dán đường link tệp nhạc (.mp3)..." class="w-full bg-studio-panel border border-studio-border rounded-xl px-2.5 py-1.5 text-[11px] text-slate-300 focus:outline-none focus:border-studio-pink">

        <label class="block w-full py-1.5 px-2.5 rounded-xl bg-studio-panel hover:bg-studio-card border border-dashed border-studio-border text-center text-[11px] text-slate-300 cursor-pointer transition">
          📂 Tải tệp MP3 từ máy tính
          <input type="file" accept="audio/*" onchange="handleLocalAudioUpload(event)" class="hidden">
        </label>

        <!-- THÊM MỚI: Danh sách nhạc đã tải lên & lưu trữ -->
        <div id="savedAudioSection" class="hidden pt-2 border-t border-studio-border/80 space-y-1.5">
          <div class="flex items-center justify-between">
            <span class="text-[10px] font-semibold uppercase tracking-wider text-slate-400">Nhạc đã lưu trong máy:</span>
            <span id="savedAudioCount" class="text-[10px] font-mono text-studio-pink">0 bài</span>
          </div>
          <div id="savedAudioList" class="space-y-1 max-h-28 overflow-y-auto pr-0.5"></div>
      </div>

      <!-- 5. Nút Tạo Link & Mã QR -->
      <button onclick="openShareModal()" class="w-full py-3 px-4 rounded-xl bg-gradient-to-r from-studio-pink to-rose-500 hover:from-rose-500 hover:to-pink-500 text-white text-xs font-bold shadow-lg shadow-studio-pink/30 flex items-center justify-center gap-2 transition">
        <span>🔗 Tạo Link & Mã QR Chia Sẻ</span>
      </button>

    </aside>
  </div>

  <!-- ===================================================================== -->
  <!-- HỘP THOẠI TẠO LINK & MÃ QR CHIA SẺ (SHARE MODAL)                      -->
  <!-- ===================================================================== -->
  <div id="shareModal" class="hidden fixed inset-0 z-50 bg-black/80 backdrop-blur-sm flex items-center justify-center p-4">
    <div class="bg-studio-panel border border-studio-border rounded-3xl w-full max-w-md p-5 shadow-2xl space-y-4">
      <div class="flex items-center justify-between border-b border-studio-border pb-3">
        <div>
          <h3 class="text-base font-bold text-white">Chia sẻ Thiệp qua Link & Mã QR</h3>
          <p class="text-xs text-slate-400">Người nhận mở link hoặc quét QR sẽ vào thẳng thiệp</p>
        </div>
        <button onclick="closeShareModal()" class="w-8 h-8 rounded-full bg-studio-card text-slate-400 hover:text-white flex items-center justify-center">
          ✕
        </button>
      </div>

      <!-- Khung hiển thị Mã QR thật -->
      <div class="bg-studio-bg p-4 rounded-2xl border border-studio-border flex flex-col items-center text-center space-y-3">
        <div id="qrcodeBox" class="bg-white p-3 rounded-2xl shadow-lg"></div>
        <div class="text-[11px] text-slate-400">
          Quét mã bằng Camera điện thoại hoặc Zalo để mở thiệp
        </div>
        <button onclick="downloadQrPng()" class="px-4 py-1.5 rounded-xl bg-studio-card hover:bg-[#232336] border border-studio-border text-xs font-semibold text-white transition">
          ⬇ Tải ảnh Mã QR (.PNG)
        </button>
      </div>

      <!-- Đường link đã nén tham số -->
      <div class="space-y-1.5">
        <label class="text-xs font-medium text-slate-300">Đường dẫn thiệp đã mã hóa cấu hình:</label>
        <div class="flex gap-2">
          <input id="encodedShareLink" type="text" readonly class="flex-1 bg-studio-bg border border-studio-border rounded-xl px-3 py-2 font-mono text-[11px] text-slate-300 focus:outline-none">
          <button onclick="copyShareLink()" class="px-3.5 py-2 rounded-xl bg-studio-pink hover:bg-rose-500 text-white text-xs font-bold shrink-0 transition">
            Copy Link
          </button>
        </div>
      </div>
    </div>
  </div>

  <!-- Toast thông báo -->
  <div id="toast" class="fixed top-[72px] left-1/2 -translate-x-1/2 z-50 bg-studio-card border border-studio-border text-white text-xs font-medium px-4 py-2 rounded-full shadow-2xl opacity-0 pointer-events-none transition-all duration-300">
    Thông báo
  </div>

  <script>
    // =========================================================================
    // 1. TRẠNG THÁI TOÀN CỤC & THAM CHIẾU DOM
    // =========================================================================
    let currentMainMode = 'card';      // 'card' | 'studio'
    let currentStudioSource = 'presets'; // 'presets' | 'html' | 'image'
    let activeRenderType = 'preset';   // 'preset' | 'html' | 'image'
    let currentModelId = 'bouquet';
    let themeKey = 'pink';
    let themeHex = '#ff3b6b';
    let themeGlow = 'rgba(255,59,107,0.45)';
    let rotSpeed = 1.0;
    let isRecipientView = false;
    let pendingAutoMusic = false;

    const centerStage = document.getElementById('centerStage');
    const presetStageWrapper = document.getElementById('presetStageWrapper');
    const presetStageFrame = document.getElementById('presetStageFrame');
    const asciiContainer = document.getElementById('asciiContainer');
    const asciiStage = document.getElementById('asciiStage');
    const htmlSandboxWrapper = document.getElementById('htmlSandboxWrapper');
    const htmlSandboxFrame = document.getElementById('htmlSandboxFrame');

    // =========================================================================
    // 2. KHO MẪU 3D TƯƠNG TÁC (BOUQUET, XMAS, BLACKHOLE, MULTILANG HEART)
    // =========================================================================
    const PRESET_HTML_TEMPLATES = {
      bouquet: `<!DOCTYPE html>
<html lang="vi">
<head>
<meta charset="utf-8">
<style>
  html,body{height:100%;margin:0;background:#08080c;display:flex;align-items:center;justify-content:center;overflow:hidden}
  canvas{max-width:100%;max-height:100%;cursor:grab;display:block}
  canvas:active{cursor:grabbing}
</style>
</head>
<body>
<canvas id="c"></canvas>
<script>
const COLS=88,ROWS=74,CW=17,CH=26,AS=CH/CW,S=COLS*0.235,BASE=0.80,TS=5.2;
const cv=document.getElementById('c'),g=cv.getContext('2d');
cv.width=COLS*CW;cv.height=ROWS*CH;
let seed=77;const R=()=>(seed=(seed*16807)%2147483647)/2147483647;
const P=[];
const add=(x,y,z,n,c,k,a,ch)=>P.push({x,y,z,nx:n[0],ny:n[1],nz:n[2],c,k,a,ch,ph:R()*6.28});

const THEMES={
  pink:{
    rose1:[255,48,102],rose2:[255,115,155],core:[255,215,95],
    stem:[42,165,88],leaf:[58,195,105],wrap:[235,215,225],wrapEdge:[255,160,195],
    rib:[255,75,130],baby:[255,245,250],halo:'255,85,135',
    msg:[[255,95,140],[255,205,110],[255,150,195]]
  },
  green:{
    rose1:[20,225,135],rose2:[135,255,195],core:[235,255,145],
    stem:[32,145,78],leaf:[50,185,98],wrap:[215,245,230],wrapEdge:[110,240,175],
    rib:[0,230,130],baby:[240,255,248],halo:'40,245,155',
    msg:[[0,255,136],[140,255,180],[255,225,110]]
  },
  blue:{
    rose1:[45,175,255],rose2:[145,220,255],core:[240,248,255],
    stem:[38,155,130],leaf:[55,185,155],wrap:[215,235,250],wrapEdge:[125,205,255],
    rib:[56,189,248],baby:[242,250,255],halo:'70,195,255',
    msg:[[56,189,248],[165,230,255],[255,210,120]]
  },
  amber:{
    rose1:[255,165,35],rose2:[255,210,105],core:[255,248,190],
    stem:[58,162,78],leaf:[75,188,92],wrap:[248,232,205],wrapEdge:[255,195,95],
    rib:[251,185,36],baby:[255,250,235],halo:'255,190,65',
    msg:[[251,191,36],[255,125,85],[255,235,145]]
  }
};
let curTheme='pink',MSG='FOR YOU',rotSpeed=1.0,t=0,last=0,rotY=0,tiltX=0.26,zoom=1.0,drag=false,lx=0,ly=0;

// 1. Giấy gói bó hoa hình phễu (Cone wrap + cổ giấy xòe)
for(let i=0;i<2400;i++){
  const u=R(),y=0.35+u*1.75;
  const flare=u>0.72?Math.pow((u-0.72)/0.28,1.5)*0.42:0;
  const rr=0.14+u*0.72+flare;
  const ang=R()*6.283;
  const backLift=(Math.sin(ang)<0?Math.abs(Math.sin(ang))*0.28*u:0);
  const isEdge=u>0.93||Math.abs(((ang*4)%6.283)-3.14)<0.18;
  add(rr*Math.cos(ang),y+backLift,rr*Math.sin(ang),[Math.cos(ang),0.25,Math.sin(ang)],null,isEdge?'we':'w',0.15+u*0.35,isEdge?'=':'/\\:'[(R()*3)|0]);
}

// 2. Thắt eo & Nơ ruy băng lụa với 2 dải thả rủ
for(let i=0;i<420;i++){
  const u=i/420,ang=u*6.283*3,rr=0.25+0.02*Math.sin(u*14);
  add(rr*Math.cos(ang),0.84+u*0.14,rr*Math.sin(ang),[Math.cos(ang),0,Math.sin(ang)],null,'r',0.32,'=');
}
for(const sg of[-1,1]){
  for(let i=0;i<180;i++){
    const th=R()*6.283,rr=0.17*Math.sqrt(R());
    add(sg*0.24+rr*Math.cos(th),0.91+rr*Math.sin(th)*0.68,0.26+Math.cos(th)*0.05,[sg*0.3,0.2,1],null,'r',0.38,'@');
  }
  for(let i=0;i<130;i++){
    const u=i/130,tx=sg*(0.12+u*0.26+Math.sin(u*5)*0.04),ty=0.86-u*0.58,tz=0.26+u*0.08;
    add(tx+(R()-.5)*0.04,ty,tz,[0,0,1],null,'r',0.42+u*0.15,'#');
  }
}

// 3. Cành hoa & Lá xanh bao quanh
const blooms=[
  {x:0.00,y:2.58,z:0.04,r:0.46,a:0.52,type:'rose'},
  {x:-0.52,y:2.38,z:0.32,r:0.41,a:0.60,type:'rose'},
  {x:0.54,y:2.40,z:0.28,r:0.42,a:0.63,type:'rose'},
  {x:-0.34,y:2.28,z:-0.42,r:0.39,a:0.67,type:'rose'},
  {x:0.38,y:2.30,z:-0.38,r:0.40,a:0.70,type:'rose'},
  {x:0.00,y:2.10,z:0.56,r:0.38,a:0.73,type:'rose'},
  {x:-0.72,y:2.04,z:-0.08,r:0.36,a:0.76,type:'tulip'},
  {x:0.74,y:2.06,z:-0.05,r:0.36,a:0.79,type:'tulip'},
  {x:-0.42,y:1.96,z:0.50,r:0.34,a:0.82,type:'tulip'},
  {x:0.44,y:1.98,z:0.48,r:0.34,a:0.84,type:'tulip'}
];

for(const fl of blooms){
  for(let i=0;i<150;i++){
    const u=i/150,y=0.12+u*(fl.y-0.12),w=Math.pow(u,1.35);
    add(fl.x*w+(R()-.5)*0.03,y,fl.z*w+(R()-.5)*0.03,[fl.x,0.2,fl.z],null,'s',u*0.5,'|');
  }
  if(fl.type==='rose'){
    // Hoa hồng xoắn ốc nhiều lớp cánh
    for(let i=0;i<820;i++){
      const u=i/820,ang=u*6.283*5.5+R()*0.25,rad=Math.pow(u,0.72)*fl.r;
      const cup=Math.pow(rad/fl.r,1.55)*0.30-Math.cos(ang*3+u*8)*0.035;
      const lx=rad*Math.cos(ang),lz=rad*Math.sin(ang);
      const isCore=u<0.14;
      const shade=u<0.45?'p1':'p2';
      add(fl.x+lx,fl.y+cup,fl.z+lz,[lx*1.4,0.85,lz*1.4],null,isCore?'c':shade,fl.a+u*0.36,isCore?'*':(u<0.5?'@':'#'));
    }
  }else{
    // Hoa Tulip chụm cánh thanh thoát
    for(let i=0;i<560;i++){
      const v=R(),ang=R()*6.283,rr=fl.r*0.72*Math.sin(v*Math.PI*0.78);
      const petalWave=1+0.12*Math.cos(ang*3);
      const lx=rr*petalWave*Math.cos(ang),lz=rr*petalWave*Math.sin(ang),ly=(v-0.25)*fl.r*1.15;
      const isCore=v>0.75&&rr<fl.r*0.25;
      add(fl.x+lx,fl.y+ly,fl.z+lz,[lx,0.4,lz],null,isCore?'c':'p1',fl.a+v*0.32,isCore?'*':'O');
    }
  }
}

// Lá xanh & Hoa bi (Baby's breath) điểm xuyết
for(let l=0;l<14;l++){
  const ang=l*(6.283/14)+0.2,dist=0.68+R()*0.28;
  for(let i=0;i<95;i++){
    const u=i/95,w=Math.sin(u*Math.PI)*0.20;
    const r=0.35+u*dist,side=(R()-.5)*w;
    const x=r*Math.cos(ang)-side*Math.sin(ang),z=r*Math.sin(ang)+side*Math.cos(ang),y=1.68+u*0.42;
    add(x,y,z,[Math.cos(ang),0.6,Math.sin(ang)],null,'l',0.48+u*0.3,'#');
  }
}
for(let b=0;b<26;b++){
  const ang=R()*6.283,rr=0.35+R()*0.62,by=2.0+R()*0.65;
  for(let k=0;k<8;k++){
    add(rr*Math.cos(ang)+(R()-.5)*0.12,by+(R()-.5)*0.12,rr*Math.sin(ang)+(R()-.5)*0.12,[0,1,0],null,'b',0.82+R()*0.18,'*+'[(k&1)]);
  }
}

const zb=new Float32Array(COLS*ROWS),chA=new Array(COLS*ROWS),coA=new Array(COLS*ROWS);
const rgb=(c,b,w=0)=>{const f=i=>{const v=c[i]*b;return Math.min(255,v+(255-v)*w)|0};return \`rgb(\${f(0)},\${f(1)},\${f(2)})\`};

cv.addEventListener('pointerdown',e=>{drag=true;lx=e.clientX;ly=e.clientY;cv.setPointerCapture(e.pointerId)});
cv.addEventListener('pointermove',e=>{if(!drag)return;rotY+=(e.clientX-lx)*0.01;tiltX=Math.max(-0.6,Math.min(1.1,tiltX+(e.clientY-ly)*0.008));lx=e.clientX;ly=e.clientY});
cv.addEventListener('pointerup',()=>drag=false);
cv.addEventListener('wheel',e=>{e.preventDefault();zoom=Math.max(0.55,Math.min(2.1,zoom*Math.exp(-e.deltaY*0.001)))},{passive:false});

window.addEventListener('message',e=>{
  const d=e.data||{};
  if(d.type==='SET_THEME'&&THEMES[d.theme])curTheme=d.theme;
  if(d.type==='SET_SPEED')rotSpeed=Number(d.speed)||0;
  if(d.type==='SET_TEXT')MSG=(d.text||'').toUpperCase();
  if(d.type==='REPLAY')t=0;
});

function frame(now){
  const dt=Math.min(0.05,(now-last)/1000||0);last=now;t+=dt;
  if(!drag)rotY+=dt*0.52*rotSpeed;
  const c=Math.cos(rotY),s=Math.sin(rotY),ct=Math.cos(tiltX),st=Math.sin(tiltX),tg=t/TS,th=THEMES[curTheme];
  zb.fill(-1e9);chA.fill(null);
  for(const p of P){
    if(tg<p.a)continue;
    const x=p.x*c+p.z*s,z=-p.x*s+p.z*c;
    const cx=(COLS/2+x*S*zoom)|0,cy=(ROWS*BASE-(p.y*ct-z*st)*S*zoom/AS)|0;
    if(cx<0||cx>=COLS||cy<0||cy>=ROWS)continue;
    const nx=p.nx*c+p.nz*s,nz=-p.nx*s+p.nz*c,b=0.35+0.65*Math.max(0,nx*0.4+p.ny*0.6+nz*0.7);
    const fr=Math.max(0,1-(tg-p.a)/0.07),id=cy*COLS+cx,zz=z*ct+p.y*st;
    let baseCol=th.stem;
    if(p.k==='p1')baseCol=th.rose1;
    else if(p.k==='p2')baseCol=th.rose2;
    else if(p.k==='c')baseCol=th.core;
    else if(p.k==='l')baseCol=th.leaf;
    else if(p.k==='w')baseCol=th.wrap;
    else if(p.k==='we')baseCol=th.wrapEdge;
    else if(p.k==='r')baseCol=th.rib;
    else if(p.k==='b')baseCol=th.baby;
    if(zz>zb[id]){zb[id]=zz;chA[id]=fr>0.42?'*':p.ch;coA[id]=rgb(baseCol,0.52+b*0.78,fr*0.85)}
  }
  g.fillStyle='#08080c';g.fillRect(0,0,cv.width,cv.height);

  // Quầng sáng mềm phía sau bó hoa
  g.save();g.globalCompositeOperation='lighter';
  const hx=COLS/2*CW,hy=(ROWS*BASE-2.15*ct*S*zoom/AS)*CH,hr=180*zoom;
  const gr=g.createRadialGradient(hx,hy,10,hx,hy,hr);
  gr.addColorStop(0,\`rgba(\${th.halo},0.22)\`);
  gr.addColorStop(1,\`rgba(\${th.halo},0)\`);
  g.fillStyle=gr;g.fillRect(hx-hr,hy-hr,hr*2,hr*2);
  g.restore();

  g.font=(CW*1.28)+'px "JetBrains Mono",Consolas,monospace';g.textAlign='center';g.textBaseline='middle';
  for(let i=0;i<chA.length;i++)if(chA[i]){g.fillStyle=coA[i];g.fillText(chA[i],(i%COLS)*CW+CW/2,((i/COLS)|0)*CH+CH/2)}
  if(MSG){
    const step=MSG.length>22?1.35:(MSG.length>16?1.65:2.0);
    const shown=Math.max(0,Math.min(MSG.length,((t-0.35)*16)|0)),m0=(COLS-MSG.length*step)/2;
    g.font='bold '+(CW*(MSG.length>20?1.25:1.5))+'px "JetBrains Mono",Consolas,monospace';
    for(let i=0;i<shown;i++){
      if(MSG[i]===' ')continue;
      const col=th.msg[i%th.msg.length];
      g.fillStyle=rgb(col,0.98);
      g.shadowColor=rgb(col,1);g.shadowBlur=12;
      g.fillText(MSG[i],(m0+i*step)*CW+CW/2,60.5*CH);
    }
    g.shadowBlur=0;
  }
  requestAnimationFrame(frame);
}
requestAnimationFrame(frame);
<\/script>
</body>
</html>`,

      xmas: `<!DOCTYPE html>
<html lang="vi">
<head>
<meta charset="utf-8">
<style>
  html,body{height:100%;margin:0;background:#05070d;display:flex;align-items:center;justify-content:center;overflow:hidden}
  canvas{max-width:100%;max-height:100%;cursor:grab;display:block}
  canvas:active{cursor:grabbing}
</style>
</head>
<body>
<canvas id="c"></canvas>
<script>
const COLS=88,ROWS=74,CW=18,CH=28,AS=CH/CW,S=COLS*0.22,HT=3.4,TS=6,BASE=0.76;
const cv=document.getElementById('c'),g=cv.getContext('2d');
cv.width=COLS*CW;cv.height=ROWS*CH;
let seed=11;const R=()=>(seed=(seed*16807)%2147483647)/2147483647;
const rad=y=>{const u=(HT-y),f=(u*2.3)%1;return 1.35*u/(HT-0.4)*(0.74+0.26*f)};
const aOf=y=>1.0+Math.max(0,Math.min(1,(y-0.6)/2.55))*0.6;
const P=[];
const add=(x,y,z,n,c,k,a,o)=>P.push(Object.assign({x,y,z,nx:n[0],ny:n[1],nz:n[2],c,k,a,ph:R()*6.28,v:0.75+R()*0.35},o));

const XMAS_THEMES={
  pink:{foliage:[235,85,145],halo:'255,120,180',msg:[[255,105,160],[255,215,90],[255,165,210]]},
  green:{foliage:[35,155,65],halo:'120,255,150',msg:[[255,196,70],[225,70,70],[80,200,110]]},
  blue:{foliage:[45,145,235],halo:'100,210,255',msg:[[56,189,248],[180,235,255],[255,210,95]]},
  amber:{foliage:[215,145,45],halo:'255,205,90',msg:[[251,191,36],[255,110,75],[255,240,160]]}
};
let curTheme='pink',MSG='MERRY CHRISTMAS',rotSpeed=1.0,rotY=0,tiltX=0.25,zoom=1.0,drag=false,lx=0,ly=0;

for(let i=0;i<6200;i++){const u=Math.pow(R(),0.9),y=HT-u*2.9,a=R()*6.283,rr=rad(y)*(0.55+0.45*Math.sqrt(R()));
 add(rr*Math.cos(a),y,rr*Math.sin(a),[Math.cos(a),0.45,Math.sin(a)],[R()*35,R()*55,R()*35],'f',(1-u))}
for(let i=0;i<240;i++){const a=R()*6.283;add(0.14*Math.cos(a),R()*0.6,0.14*Math.sin(a),[Math.cos(a),0,Math.sin(a)],[130,85,45],'t',0)}
const GLD=[255,196,70];
for(let s=0;s<2;s++)for(let i=0;i<1050;i++){const u=i/1050,y=3.15-u*2.55,an=u*5.5*6.283+s*3.1416,yy=y+(R()-.5)*0.09,rr=rad(yy)+0.07+(R()-.5)*0.04;
 add(rr*Math.cos(an),yy,rr*Math.sin(an),[Math.cos(an),0.3,Math.sin(an)],GLD,'g',aOf(yy))}
const pal=[[235,55,55],[70,120,255],[255,200,60],[240,240,240]];
for(let b=0;b<30;b++){const y=0.7+R()*2.3,a=R()*6.283,rr=rad(y)*0.95,cx=rr*Math.cos(a),cz=rr*Math.sin(a),col=pal[(R()*4)|0],ap=aOf(y)+0.03+R()*0.08;
 for(let i=0;i<34;i++){const d=[R()-.5,R()-.5,R()-.5],m=Math.hypot(...d)||1;
  add(cx+d[0]/m*0.08,y+d[1]/m*0.08,cz+d[2]/m*0.08,[d[0]/m,d[1]/m,d[2]/m],col,'b',ap)}}
const lp=[[255,230,120],[255,120,90],[120,200,255],[150,255,150]];
for(let i=0;i<165;i++){const y=0.6+R()*2.7,a=R()*6.283,rr=rad(y)*1.03;
 add(rr*Math.cos(a),y,rr*Math.sin(a),[Math.cos(a),0.4,Math.sin(a)],lp[(R()*4)|0],'l',aOf(y)+R()*0.1)}
for(let i=0;i<340;i++){const th=R()*6.283,k=Math.abs((((th+1.5708)*5/6.283)%1)*2-1),r=(0.13+0.17*k)*Math.sqrt(R());
 add(r*Math.cos(th),3.58+r*Math.sin(th),(R()-.5)*0.12,[0,0,1],[255,215,80],'s',1.72)}

function surf(cx,cz,y0,ang,w,h,d,N,a,fn){
 const ca=Math.cos(ang),sa=Math.sin(ang),A=[w*d,h*w,h*w,h*d,h*d],T=A.reduce((s,x)=>s+x);
 for(let i=0;i<N;i++){let f=0,r=R()*T;while(f<4&&r>A[f]){r-=A[f];f++}
  let x=(R()-.5)*w,y=R()*h,z=(R()-.5)*d,n,u,v;
  if(f==0){y=h;n=[0,1,0];u=x;v=z}else if(f==1){z=d/2;n=[0,0,1];u=x;v=y}else if(f==2){z=-d/2;n=[0,0,-1];u=x;v=y}else if(f==3){x=w/2;n=[1,0,0];u=z;v=y}else{x=-w/2;n=[-1,0,0];u=z;v=y}
  const q=fn(f,u,v);
  add(cx+x*ca+z*sa,y0+y,cz-x*sa+z*ca,[n[0]*ca+n[2]*sa,n[1],-n[0]*sa+n[2]*ca],q[0],'p',a,{ch:q[1]})}}
function gift(o){
 const {cx,cz,y0,ang,w,h,d,col,col2,rib,pat,a,tag}=o,rw=0.055,lh=0.07,ca=Math.cos(ang),sa=Math.sin(ang),ar=(w*d+2*h*w+2*h*d)*1500;
 const lid=col.map(c=>Math.min(255,c*1.2+25));
 surf(cx,cz,y0,ang,w,h-lh,d,ar|0,a,(f,u,v)=>{
  if(Math.abs(u)<rw||(f==0&&Math.abs(v)<rw))return[rib,'+'];
  if(pat==1){const du=((u*9)%1+1)%1-.5,dv=((v*9)%1+1)%1-.5;if(du*du+dv*dv<0.07)return[col2,'O']}
  if(pat==2&&(Math.floor((u+v)*8)&1))return[col2,'/'];
  if(pat==3&&((Math.floor(u*7)+Math.floor(v*7))&1))return[col2,'#'];
  return[col,'%#X'[(R()*3)|0]]});
 surf(cx,cz,y0+h-lh,ang,w+0.05,lh+0.01,d+0.05,(ar*0.3)|0,a,(f,u,v)=>(Math.abs(u)<rw||(f==0&&Math.abs(v)<rw))?[rib,'+']:[lid,'#']);
 const L=(x,y,z,n,c,ch)=>add(cx+x*ca+z*sa,y0+h+y,cz-x*sa+z*ca,[n[0]*ca+n[2]*sa,n[1],-n[0]*sa+n[2]*ca],c,'p',a+0.02,{ch});
 const bs=Math.min(1,w/0.4+0.4);
 for(const sg of[-1,1])for(let i=0;i<70;i++){const th=R()*6.283,rr=0.45+0.55*R();
  L(sg*0.12*bs+0.1*bs*rr*Math.cos(th),0.01+0.075*bs+0.065*bs*rr*Math.sin(th),(R()-.5)*0.06,[sg*0.3,0.5,1],rib,'@O'[(R()*2)|0])}
 for(let i=0;i<36;i++){const v=[R()-.5,R()-.5,R()-.5],m=Math.hypot(...v)||1;L(v[0]/m*0.045,0.06+v[1]/m*0.045,v[2]/m*0.045,[v[0]/m,v[1]/m,v[2]/m],rib,'@')}
 if(tag)for(let i=0;i<40;i++){const x=w*0.16+R()*0.13,y=0.05+R()*0.11,ink=R()<0.25;L(x,y-h,d/2+0.012,[0,0,1],ink?[200,50,50]:[245,245,245],ink?'-':'=')}}
const WHT=[235,235,235],RED=[225,50,50],BLU=[60,110,240],GRN=[40,170,80];
[
 {cx:-0.95,cz:0.8,y0:0,ang:0.35,w:0.62,h:0.46,d:0.5,col:RED,col2:[255,130,130],rib:GLD,pat:1,tag:1},
 {cx:-0.95,cz:0.8,y0:0.46,ang:-0.2,w:0.3,h:0.26,d:0.3,col:WHT,col2:RED,rib:RED,pat:0},
 {cx:1.0,cz:0.75,y0:0,ang:-0.4,w:0.5,h:0.58,d:0.46,col:BLU,col2:[150,190,255],rib:WHT,pat:2,tag:1},
 {cx:0.25,cz:1.3,y0:0,ang:0.15,w:0.55,h:0.34,d:0.5,col:GRN,col2:[120,220,150],rib:RED,pat:3,tag:1},
 {cx:-0.45,cz:1.4,y0:0,ang:0.5,w:0.3,h:0.55,d:0.3,col:[150,80,210],col2:[205,160,255],rib:GLD,pat:1},
 {cx:1.4,cz:0.1,y0:0,ang:0.8,w:0.38,h:0.3,d:0.38,col:[225,165,45],col2:WHT,rib:RED,pat:0},
 {cx:1.25,cz:1.2,y0:0,ang:-0.6,w:0.3,h:0.22,d:0.3,col:[40,190,200],col2:WHT,rib:RED,pat:1}
].forEach((o,i)=>{o.a=1.0+i*0.07;gift(o)});

const snow=[];for(let i=0;i<220;i++)snow.push({x:R()*COLS,y:R()*ROWS,v:2+R()*5,ph:R()*6.28,c:'.+*.'[(R()*4)|0]});
const ramp=" .:-~=+*xvo%&#@";
const zb=new Float32Array(COLS*ROWS),chA=new Array(COLS*ROWS),coA=new Array(COLS*ROWS);
let t=0,last=0;
const rgb=(c,b,w=0)=>{const f=i=>{const v=c[i]*b;return Math.min(255,v+(255-v)*w)|0};return \`rgb(\${f(0)},\${f(1)},\${f(2)})\`};

cv.addEventListener('pointerdown',e=>{drag=true;lx=e.clientX;ly=e.clientY;cv.setPointerCapture(e.pointerId)});
cv.addEventListener('pointermove',e=>{if(!drag)return;rotY+=(e.clientX-lx)*0.01;tiltX=Math.max(-0.5,Math.min(1.0,tiltX+(e.clientY-ly)*0.008));lx=e.clientX;ly=e.clientY});
cv.addEventListener('pointerup',()=>drag=false);
cv.addEventListener('wheel',e=>{e.preventDefault();zoom=Math.max(0.55,Math.min(2.0,zoom*Math.exp(-e.deltaY*0.001)))},{passive:false});

window.addEventListener('message',e=>{
  const d=e.data||{};
  if(d.type==='SET_THEME'&&XMAS_THEMES[d.theme])curTheme=d.theme;
  if(d.type==='SET_SPEED')rotSpeed=Number(d.speed)||0;
  if(d.type==='SET_TEXT')MSG=(d.text||'').toUpperCase();
  if(d.type==='REPLAY')t=0;
});

function halo(yw,col,al,ct,st){
  g.save();const px=COLS/2*CW,py=(ROWS*BASE-yw*ct*S*zoom/AS)*CH,rx=Math.max(90,(rad(Math.min(yw,3.3))+0.15)*S*zoom*CW*1.2);
  g.translate(px,py);g.scale(1,0.4);const gr=g.createRadialGradient(0,0,0,0,0,rx);gr.addColorStop(0,\`rgba(\${col},\${al})\`);gr.addColorStop(1,\`rgba(\${col},0)\`);
  g.fillStyle=gr;g.fillRect(-rx,-rx,rx*2,rx*2);g.restore();
}
function frame(now){
 const dt=Math.min(0.05,(now-last)/1000||0);last=now;t+=dt;
 if(!drag)rotY+=dt*0.55*rotSpeed;
 const c=Math.cos(rotY),s=Math.sin(rotY),ct=Math.cos(tiltX),st=Math.sin(tiltX),tg=t/TS,th=XMAS_THEMES[curTheme];
 zb.fill(-1e9);chA.fill(null);
 for(const p of P){if(tg<p.a)continue;
  const x=p.x*c+p.z*s,z=-p.x*s+p.z*c,cx=(COLS/2+x*S*zoom)|0,cy=(ROWS*BASE-(p.y*ct-z*st)*S*zoom/AS)|0;
  if(cx<0||cx>=COLS||cy<0||cy>=ROWS)continue;
  const nx=p.nx*c+p.nz*s,nz=-p.nx*s+p.nz*c,df=Math.max(0,nx*0.45+p.ny*0.55+nz*0.7),b=0.3+0.7*df;
  const id=cy*COLS+cx,fr=Math.max(0,1-(tg-p.a)/(p.k=='f'?0.05:0.09)),wh=fr*0.9;let ch,col,zz=z*ct+p.y*st;
  switch(p.k){
   case'f':{const bb=Math.min(1,b*p.v);ch=fr>0.55?'#':fr>0.2?'@':ramp[2+((bb*(ramp.length-3))|0)];const fc=[th.foliage[0]+p.c[0],th.foliage[1]+p.c[1],th.foliage[2]+p.c[2]];col=rgb(fc,0.45+bb*0.9,wh);break}
   case't':ch=b>0.6?'#':'|';col=rgb(p.c,0.5+b*0.6);break;
   case'g':ch=fr>0.3?'@':b>0.75?'@':b>0.5?'#':'=';col=rgb(p.c,0.8+b*0.5+0.15*Math.sin(t*5+p.ph),wh);zz+=0.15;break;
   case'b':ch=fr>0.3?'@':b>0.8?'@':b>0.55?'O':'0';col=rgb(p.c,0.6+b*0.6,wh);zz+=0.2;break;
   case'l':{const tw=0.5+0.5*Math.sin(t*3+p.ph);ch=tw>0.6?'*':'+';col=rgb(p.c,0.3+tw*0.9,wh);zz+=0.3;break}
   case's':ch=p.ph>3?'#':'@';col=rgb(p.c,0.9+0.15*Math.sin(t*4),wh);zz+=0.3;break;
   default:ch=p.ch;col=rgb(p.c,0.55+b*0.65,wh);zz+=0.05;
  }
  if(zz>zb[id]){zb[id]=zz;chA[id]=ch;coA[id]=col}}
 g.fillStyle='#05070d';g.fillRect(0,0,cv.width,cv.height);
 g.save();g.globalCompositeOperation='lighter';
 if(tg<1.03)halo(0.5+2.9*Math.min(tg,1),th.halo,0.28,ct,st);
 if(tg>=0.97&&tg<1.7)halo(0.6+Math.max(0,Math.min(1,(tg-1)/0.6))*2.55,'255,205,90',0.26,ct,st);
 g.restore();
 g.font=(CW*1.35)+'px Menlo,Consolas,"DejaVu Sans Mono",monospace';g.textAlign='center';g.textBaseline='middle';
 g.fillStyle='rgba(150,170,200,0.14)';
 for(let r=57;r<63;r++)for(let q=0;q<COLS;q++)if(((q*7+r*13)%5)==0)g.fillText('-',q*CW+CW/2,r*CH+CH/2);
 for(const f of snow){f.y+=f.v*dt;f.x+=Math.sin(t+f.ph)*dt*2;if(f.y>ROWS){f.y=-1;f.x=R()*COLS}
  g.fillStyle='rgba(230,240,255,'+(0.35+f.v/12)+')';g.fillText(f.c,((f.x+COLS)%COLS)*CW,f.y*CH)}
 for(let i=0;i<chA.length;i++)if(chA[i]){g.fillStyle=coA[i];g.fillText(chA[i],(i%COLS)*CW+CW/2,((i/COLS)|0)*CH+CH/2)}
 if(MSG){
    const step=MSG.length>22?1.35:(MSG.length>16?1.7:2.1);
    const shown=Math.max(0,Math.min(MSG.length,((t-0.35)*16)|0)),m0=(COLS-MSG.length*step)/2;
    g.font='bold '+(CW*(MSG.length>20?1.3:1.6))+'px Menlo,Consolas,monospace';
    for(let i=0;i<shown;i++){
      if(MSG[i]===' ')continue;
      const mc=th.msg[i%th.msg.length];
      g.fillStyle=rgb(mc,0.98);
      g.shadowColor=rgb(mc,1);g.shadowBlur=12;
      g.fillText(MSG[i],(m0+i*step)*CW+CW/2,60.5*CH);
    }
    g.shadowBlur=0;
  }
 requestAnimationFrame(frame)}
requestAnimationFrame(frame);
<\/script>
</body>
</html>`,

      blackhole: `<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8" />
  <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.9.0/p5.min.js"><\/script>
  <style>
    * { margin:0; padding:0; box-sizing:border-box; }
    html,body { width:100%; height:100%; background:#06060a; overflow:hidden; font-family:'JetBrains Mono','Courier New',monospace; user-select:none; touch-action:none; }
    canvas { display:block; position:fixed; inset:0; cursor:grab; }
  </style>
</head>
<body>
<script>
const ASCII_CHARS = " .'\`^\\",:;Il!i><~+_-?][}{1)(|/tfjrxnuvczXYUJCLQ0OZmwqpdbkhao*#MW&8%B@$";
const STYLES = {
  pink:  { glow:[255,59,107], photon:[255,180,210], msg:[255,95,145], colorFn: h => ({ r:255, g:lerp(35,195,pow(h,1.5)), b:lerp(95,235,pow(h,2.2)) }) },
  green: { glow:[0,255,136],  photon:[170,255,210], msg:[0,255,136],   colorFn: h => ({ r:lerp(10,165,pow(h,2.8)), g:lerp(95,255,pow(h,0.85)), b:lerp(45,195,pow(h,2.2)) }) },
  blue:  { glow:[56,189,248], photon:[195,240,255], msg:[56,189,248],  colorFn: h => ({ r:lerp(95,220,pow(h,2.1)), g:lerp(75,235,pow(h,1.15)), b:255 }) },
  amber: { glow:[255,95,20],  photon:[255,210,145], msg:[251,191,36],  colorFn: h => ({ r:255, g:lerp(45,238,pow(h,1.35)), b:lerp(12,205,pow(h,3.1)) }) }
};
let pg, cols=0, rows=0, particles=[], M, velX=0, velY=0, dragging=false, zoom=1.0, zoomTarget=1.0, bloom=0;
let spinSpeed=1.0, curTheme='pink', renderMode=2, glitchTimer=0, MSG='FOR YOU';
const cellSize=9, TOTAL_PARTICLES=1500, TILT0=0.34;
const resetMatrix = () => [[1,0,0],[0,Math.cos(TILT0),-Math.sin(TILT0)],[0,Math.sin(TILT0),Math.cos(TILT0)]];

window.addEventListener('message', e => {
  const d = e.data || {};
  if (d.type === 'SET_THEME' && STYLES[d.theme]) { curTheme = d.theme; glitchTimer = 12; }
  if (d.type === 'SET_SPEED') spinSpeed = Number(d.speed) || 0;
  if (d.type === 'SET_TEXT') MSG = (d.text || '').toUpperCase();
  if (d.type === 'SET_BH_MODE') { renderMode = Number(d.mode); glitchTimer = 16; }
  if (d.type === 'GLITCH') glitchTimer = 22;
  if (d.type === 'REPLAY') { bloom = 0; glitchTimer = 14; }
});

function setup() {
  const cnv = createCanvas(windowWidth, windowHeight);
  textFont('Courier New'); textAlign(CENTER, CENTER);
  M = resetMatrix();
  cols = max(12, floor(width / cellSize));
  rows = max(12, floor(height / cellSize));
  pg = createGraphics(cols, rows); pg.pixelDensity(1);
  for (let i = 0; i < TOTAL_PARTICLES; i++) {
    let r = pow(random(), 1.65) * 0.42 + 0.13;
    particles.push({ r, angle: random(TWO_PI), baseSpeed: (0.018 / pow(r, 0.85)) * random(0.85, 1.15), yOffset: randomGaussian(0, 0.012 * r), isHalo: random() < 0.23, size: random(0.85, 2.2) });
  }
  const el = cnv.elt;
  el.addEventListener('pointerdown', e => { dragging = true; velX = 0; velY = 0; el.setPointerCapture(e.pointerId); });
  el.addEventListener('pointermove', e => { if (!dragging) return; const ay = -e.movementX * 0.008, ax = e.movementY * 0.008; rotateView(ay, ax); velY = ay; velX = ax; });
  el.addEventListener('pointerup', () => dragging = false);
  el.addEventListener('wheel', e => { e.preventDefault(); zoomTarget = constrain(zoomTarget * Math.exp(-e.deltaY * 0.0012), 0.4, 3.8); }, { passive: false });
}

function draw() {
  background(6, 6, 10);
  bloom = lerp(bloom, 1.0, 0.05);
  if (!dragging && (abs(velX) > 0.0001 || abs(velY) > 0.0001)) { rotateView(velY, velX); velX *= 0.93; velY *= 0.93; }
  zoom = lerp(zoom, zoomTarget, 0.14);
  if (glitchTimer > 0) glitchTimer--;

  const st = STYLES[curTheme];
  pg.background(0); pg.push(); pg.translate(cols * 0.5, rows * 0.46);
  let scaleUnit = min(cols, rows) * bloom * zoom * 0.92;
  let horizonR = scaleUnit * 0.125;
  pg.noStroke();
  for (let i = 6; i >= 1; i--) {
    pg.fill(st.glow[0], st.glow[1], st.glow[2], map(i, 1, 6, 44, 6));
    pg.ellipse(0, 0, horizonR * (2.2 + i * 0.55), horizonR * (1.1 + i * 0.25));
  }
  for (let p of particles) {
    p.angle += p.baseSpeed * spinSpeed;
    let x3d = cos(p.angle) * p.r, z3d = sin(p.angle) * p.r, y3d = p.yOffset;
    if (p.isHalo) { y3d = -abs(sin(p.angle)) * p.r * 0.85 * (p.angle % TWO_PI > PI ? 1 : -1); z3d *= 0.35; }
    let rx = M[0][0]*x3d + M[0][1]*y3d + M[0][2]*z3d;
    let ry = M[1][0]*x3d + M[1][1]*y3d + M[1][2]*z3d;
    let fz = M[2][0]*x3d + M[2][1]*y3d + M[2][2]*z3d;
    let fov = 1.4 / (1.4 + fz), sx = rx * scaleUnit * fov, sy = ry * scaleUnit * fov;
    if (fz > 0 && sqrt(sx*sx + sy*sy) < horizonR * 0.96) continue;
    let col = st.colorFn(constrain(1.0 - (p.r - 0.13) / 0.39, 0, 1));
    let doppler = map(M[2][0] * -sin(p.angle) + M[2][2] * cos(p.angle), 1, -1, 0.72, 1.26);
    pg.fill(constrain(col.r*doppler,0,255), constrain(col.g*doppler,0,255), constrain(col.b*doppler,0,255), 215);
    pg.circle(sx, sy, p.size * fov * 1.15);
  }
  pg.fill(0); pg.stroke(st.photon[0], st.photon[1], st.photon[2], 185); pg.strokeWeight(1.1);
  pg.circle(0, 0, horizonR * 1.92); pg.pop();

  pg.loadPixels(); noStroke();
  for (let y = 0; y < rows; y++) {
    let gShift = (glitchTimer > 0 && random() < 0.28) ? random(-28, 28) * (glitchTimer / 18) : 0;
    for (let x = 0; x < cols; x++) {
      let idx = (y * cols + x) * 4, r = pg.pixels[idx], g = pg.pixels[idx+1], b = pg.pixels[idx+2];
      let lum = (0.299*r + 0.587*g + 0.114*b) / 255;
      if (lum < 0.035) continue;
      let px = x * cellSize + cellSize * 0.5 + gShift, py = y * cellSize + cellSize * 0.5;
      fill(r, g, b);
      if (renderMode === 0) circle(px, py, lum * cellSize * 1.16);
      else if (renderMode === 1) { rectMode(CENTER); rect(px, py, cellSize*(0.54+lum*0.44), cellSize*(0.54+lum*0.44), 1.5); }
      else { textSize(cellSize * 1.14); text(ASCII_CHARS[floor(constrain(lum,0,0.999)*ASCII_CHARS.length)], px, py); }
    }
  }
  if (MSG) {
    drawingContext.save();
    drawingContext.shadowColor = \`rgb(\${st.msg[0]},\${st.msg[1]},\${st.msg[2]})\`;
    drawingContext.shadowBlur = 14;
    fill(st.msg[0], st.msg[1], st.msg[2]);
    textSize(max(16, min(24, width * 0.024)));
    text(MSG.split('').join(' '), width * 0.5, height * 0.77);
    drawingContext.restore();
  }
}

function rotateView(ay, ax) {
  const cy = Math.cos(ay), sy = Math.sin(ay), cx = Math.cos(ax), sx = Math.sin(ax);
  const mul = (a, b) => a.map((r, i) => [0,1,2].map(j => r[0]*b[0][j] + r[1]*b[1][j] + r[2]*b[2][j]));
  M = mul([[1,0,0],[0,cx,-sx],[0,sx,cx]], mul([[cy,0,sy],[0,1,0],[-sy,0,cy]], M));
}
function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
  cols = max(12, floor(width / cellSize)); rows = max(12, floor(height / cellSize));
  if (pg) pg.remove(); pg = createGraphics(cols, rows); pg.pixelDensity(1);
}
<\/script>
</body>
</html>`,

      multilang_heart: `<!DOCTYPE html>
<html lang="vi">
<head>
<meta charset="utf-8">
<style>
  html,body{height:100%;margin:0;background:#08080c;display:flex;align-items:center;justify-content:center;overflow:hidden}
  canvas{max-width:100%;max-height:100%;cursor:grab;display:block}
  canvas:active{cursor:grabbing}
</style>
</head>
<body>
<canvas id="c"></canvas>
<script>
const LANG_LIST=[
  "ANH YÊU EM","I LOVE YOU","EM YÊU ANH","JE T'AIME","TE AMO","TI AMO","ICH LIEBE DICH",
  "사랑해","愛してる","我爱你","Я ТЕБЯ ЛЮБЛЮ","أحبك","Σ' ΑΓΑΠΩ","IK HOU VAN JOU","EU TE AMO",
  "SENİ SEVİYORUM","ฉันรักคุณ","MAHAL KITA","AKU CINTA KAMU","KOCHAM CIĘ","MILUJI TĚ",
  "SZERETLEK","TE IUBESC","JAG ÄLSKAR DIG","RAKASTAN SINUA","JEG ELSKER DEG","ALOFA IA TE OE",
  "ALOHA AU IA OE","SAYA CINTA KAMU","MI AMAS VIN","VOLIM TE","LJUBIM TE","ОБИЧАМ ТЕ",
  "Я КОХАЮ ТЕБЕ","Я ЦЯБЕ КАХАЮ","AŠ TAVE MYLIU","ES TEVI MĪLU","MA ARMASTAN SIND","ÉG ELSKA ÞIG",
  "TÁ GRÁ AGAM DUIT","RWy'N DY GARU DI","INĦOBBOK","T'ESTIMO","MAITE ZAITUT","EU TE AMO",
  "NAKU PENDA","NGIYAKUTHANDA","EK HET JOU LIEF","MIELAS","SARANGHAEYO","AISHITERU","WO AI NI",
  "BONG SRALANH OUN","KHOI HAK JAO","CHIT PA DE","MAIN TUMSE PYAR KARTA HOON","AMI TOMAKE BHALOBASHI",
  "NAAN UNNAI KADHALIKIREN","NENU NINNU PREMISTUNNANU","NJAN NINNESNEHIKKUNNU","MI TUJHYAVAR PREM KARTO",
  "HU TANE PREM KARU CHU","MAIN TENU PYAR KARDA HAAN","DOSTET DARAM","SENI SEVAMAN","MEN SENI SUYEM",
  "BIJÎ EVÎN","ANI OHEV OTACH","YES KEZ SIRUM EM","MIQVARXAR","MEN SENI SEVAMAN","KUVA","IS-SE-IN"
];

const THEMES={
  pink:{
    bands:[[255,55,115],[255,115,165],[255,175,205],[255,215,135]],
    spark:[255,130,180],halo:'255,65,120',msg:[255,85,135]
  },
  green:{
    bands:[[0,255,136],[95,255,180],[175,255,210],[225,255,140]],
    spark:[110,255,185],halo:'0,255,136',msg:[0,255,136]
  },
  blue:{
    bands:[[56,189,248],[125,215,255],[185,235,255],[235,248,255]],
    spark:[140,220,255],halo:'56,189,248',msg:[56,189,248]
  },
  amber:{
    bands:[[251,191,36],[255,140,65],[255,215,115],[255,245,175]],
    spark:[255,205,95],halo:'251,191,36',msg:[251,191,36]
  }
};

const cv=document.getElementById('c'),g=cv.getContext('2d');
let curTheme='pink',MSG='FOR YOU',CUSTOM_WORDS=null,rotSpeed=1.0,rotY=0,tiltX=0.14,zoom=1.0,drag=false,lx=0,ly=0,t=0,last=0;

// Dệt bề mặt trái tim 3D dáng icon ❤️ (hai bầu tròn đầy & khe lõm đỉnh sâu rõ nét)
let PTS=[];
function buildHeartMesh(){
  PTS=[];
  const activeList=(CUSTOM_WORDS&&CUSTOM_WORDS.length>0)?CUSTOM_WORDS:LANG_LIST;
  const RINGS=28;
  for(let r=1;r<RINGS;r++){
    const u=(r/RINGS)*Math.PI; // 0 (khe lõm đỉnh tim) -> PI (đỉnh nhọn đáy tim)
    const sinU=Math.sin(u),cosU=Math.cos(u);
    const hx2d=16*Math.pow(sinU,3);
    // Tăng biên độ sóng cos(2u), cos(3u) để tạo khe lõm sâu ở đỉnh tim giống icon ❤️
    const hy2d=12.6*cosU-6.4*Math.cos(2*u)-2.6*Math.cos(3*u)-1.1*Math.cos(4*u);
    const ringRadius=Math.abs(hx2d);
    const zDepth=7.4*Math.pow(sinU,1.35);
    const charsPerRing=Math.max(14,Math.round(ringRadius*4.3));

    let ribbon="";
    let langIdx=(r*3)%activeList.length;
    while(ribbon.length<charsPerRing+18){
      ribbon+=activeList[langIdx%activeList.length]+"  •  ";
      langIdx++;
    }

    for(let k=0;k<charsPerRing;k++){
      const v=(k/charsPerRing)*Math.PI*2;
      const ch=ribbon[k%ribbon.length];
      if(ch===' ')continue;
      const x=hx2d*Math.cos(v)*0.061;
      const y=hy2d*0.061+0.20;
      const z=zDepth*Math.sin(v)*0.061;
      PTS.push({
        x,y,z,ch,
        ci:r%4,
        growOrder:1-(r/RINGS) // Mọc từ đáy nhọn lên hai bầu tim
      });
    }
  }
}
buildHeartMesh();

// Hạt tim nhỏ bay lơ lửng xung quanh
const SPARKS=[];
for(let i=0;i<55;i++){
  const ang=Math.random()*6.283,rad=0.65+Math.random()*0.95;
  SPARKS.push({
    x:Math.cos(ang)*rad,
    y:-0.8+Math.random()*1.8,
    z:Math.sin(ang)*rad,
    v:0.25+Math.random()*0.45,
    ph:Math.random()*6.28,
    ch:i%3===0?'♥':(i%2===0?'*':'+')
  });
}

function resize(){cv.width=window.innerWidth;cv.height=window.innerHeight}
window.addEventListener('resize',resize);resize();

cv.addEventListener('pointerdown',e=>{drag=true;lx=e.clientX;ly=e.clientY;cv.setPointerCapture(e.pointerId)});
cv.addEventListener('pointermove',e=>{if(!drag)return;rotY+=(e.clientX-lx)*0.0085;tiltX=Math.max(-0.85,Math.min(0.85,tiltX+(e.clientY-ly)*0.007));lx=e.clientX;ly=e.clientY});
cv.addEventListener('pointerup',()=>drag=false);
cv.addEventListener('wheel',e=>{e.preventDefault();zoom=Math.max(0.5,Math.min(2.2,zoom*Math.exp(-e.deltaY*0.001)))},{passive:false});

window.addEventListener('message',e=>{
  const d=e.data||{};
  if(d.type==='SET_THEME'&&THEMES[d.theme])curTheme=d.theme;
  if(d.type==='SET_SPEED')rotSpeed=Number(d.speed)||0;
  if(d.type==='SET_TEXT')MSG=(d.text||'').toUpperCase();
  if(d.type==='SET_HEART_WORDS'){
    const list=Array.isArray(d.words)?d.words.map(s=>String(s).trim()).filter(Boolean):[];
    CUSTOM_WORDS=list.length>0?list:null;
    buildHeartMesh();
  }
  if(d.type==='REPLAY')t=0;
});

function frame(now){
  const dt=Math.min(0.05,(now-last)/1000||0);last=now;t+=dt;
  if(!drag)rotY+=dt*0.55*rotSpeed;

  // Nhịp đập trái tim kép (lub-dub)
  const pulse=Math.pow(Math.max(0,Math.sin(t*3.5)),6)*0.075+Math.pow(Math.max(0,Math.sin(t*3.5-0.45)),10)*0.04;
  const beat=1+pulse;
  const growProg=Math.min(1.2,t/1.8);

  const cy=Math.cos(rotY),sy=Math.sin(rotY),cx=Math.cos(tiltX),sx=Math.sin(tiltX);
  const th=THEMES[curTheme],sc=Math.min(cv.width,cv.height)*0.33*zoom*beat;

  g.fillStyle='#08080c';g.fillRect(0,0,cv.width,cv.height);

  // Quầng hào quang nhịp đập phía sau trái tim
  g.save();g.globalCompositeOperation='lighter';
  const hr=sc*1.35;
  const gr=g.createRadialGradient(cv.width*0.5,cv.height*0.44,10,cv.width*0.5,cv.height*0.44,hr);
  gr.addColorStop(0,\`rgba(\${th.halo},\${0.18+pulse*1.2})\`);
  gr.addColorStop(1,\`rgba(\${th.halo},0)\`);
  g.fillStyle=gr;g.fillRect(0,0,cv.width,cv.height);
  g.restore();

  const drawList=[];

  for(const p of PTS){
    if(growProg<p.growOrder)continue;
    const x1=p.x*cy+p.z*sy,z1=-p.x*sy+p.z*cy;
    const y2=p.y*cx-z1*sx,z2=p.y*sx+z1*cx;
    const fov=2.5/(2.5+z2);
    drawList.push({
      sx:cv.width*0.5+x1*sc*fov,
      sy:cv.height*0.44-y2*sc*fov,
      z:z2,fov,ch:p.ch,ci:p.ci,isSpark:false
    });
  }

  for(const sp of SPARKS){
    sp.y+=sp.v*dt;
    if(sp.y>1.25)sp.y=-1.15;
    const x1=sp.x*cy+sp.z*sy,z1=-sp.x*sy+sp.z*cy;
    const y2=sp.y*cx-z1*sx,z2=sp.y*sx+z1*cx;
    const fov=2.5/(2.5+z2);
    drawList.push({
      sx:cv.width*0.5+x1*sc*fov,
      sy:cv.height*0.44-y2*sc*fov,
      z:z2,fov,ch:sp.ch,ci:0,isSpark:true,ph:sp.ph
    });
  }

  // Sắp xếp từ xa đến gần (Painter's algorithm) để mặt trước che mặt sau chuẩn 3D
  drawList.sort((a,b)=>b.z-a.z);

  g.textAlign='center';g.textBaseline='middle';
  for(const item of drawList){
    if(item.isSpark){
      const tw=0.35+0.65*(0.5+0.5*Math.sin(t*4+item.ph));
      g.font=\`\${Math.max(10,14*item.fov*zoom)|0}px "JetBrains Mono",monospace\`;
      g.fillStyle=\`rgba(\${th.spark[0]},\${th.spark[1]},\${th.spark[2]},\${tw*0.75})\`;
      g.shadowBlur=0;
      g.fillText(item.ch,item.sx,item.sy);
      continue;
    }

    // Mặt trước sáng rõ & phát sáng, mặt sau mờ dần tạo chiều sâu 3D
    const frontFactor=Math.max(0.14,Math.min(1,(1.05-item.z)/1.85));
    const col=th.bands[item.ci];
    const fSize=Math.max(9,13.5*item.fov*zoom)|0;
    g.font=\`bold \${fSize}px "JetBrains Mono",Consolas,monospace\`;
    g.fillStyle=\`rgba(\${col[0]},\${col[1]},\${col[2]},\${frontFactor})\`;
    if(frontFactor>0.75){
      g.shadowColor=\`rgb(\${col[0]},\${col[1]},\${col[2]})\`;
      g.shadowBlur=7;
    }else{
      g.shadowBlur=0;
    }
    g.fillText(item.ch,item.sx,item.sy);
  }
  g.shadowBlur=0;

  if(MSG){
    const col=th.msg;
    g.font=\`bold \${Math.max(16,Math.min(24,cv.width*0.025))|0}px "JetBrains Mono",monospace\`;
    g.fillStyle=\`rgb(\${col[0]},\${col[1]},\${col[2]})\`;
    g.shadowColor=\`rgb(\${col[0]},\${col[1]},\${col[2]})\`;g.shadowBlur=14;
    g.fillText(MSG.split('').join(' '),cv.width*0.5,cv.height*0.77);
    g.shadowBlur=0;
  }
  requestAnimationFrame(frame);
}
requestAnimationFrame(frame);
<\/script>
</body>
</html>`,

      bouquet_v2: `<!DOCTYPE html>
<html lang="vi">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@500;700&display=swap" rel="stylesheet">
<style>
  *{box-sizing:border-box;-webkit-tap-highlight-color:transparent}
  html,body{margin:0;padding:0;width:100%;height:100%;background:#08080c;overflow:hidden;user-select:none}
  canvas{display:block;width:100%;height:100%;cursor:grab}
  canvas:active{cursor:grabbing}
</style>
</head>
<body>
<canvas id="bouquetCanvas"></canvas>
<script>
const canvas=document.getElementById('bouquetCanvas');
const ctx=canvas.getContext('2d',{alpha:false});
const RAMP=" .,:;+*?%S#@";
const TAU=Math.PI*2;

const THEMES={
  pink:{
    bg:'#08080c',wrapPrimary:[222,184,145],wrapEdge:[255,225,195],ribbon:[244,63,94],stem:[46,139,87],leaf:[60,179,113],
    flowers:[[235,35,75],[255,105,160],[255,182,193],[255,215,90],[248,250,255]],
    sparkle:[255,220,130],msgColors:[[255,105,160],[255,215,90],[244,63,94]]
  },
  green:{
    bg:'#08080c',wrapPrimary:[195,235,215],wrapEdge:[235,255,245],ribbon:[0,230,130],stem:[35,135,75],leaf:[55,185,105],
    flowers:[[0,225,125],[110,255,180],[185,255,215],[235,255,140],[245,255,250]],
    sparkle:[130,255,190],msgColors:[[0,255,136],[140,255,180],[235,255,140]]
  },
  blue:{
    bg:'#08080c',wrapPrimary:[190,220,245],wrapEdge:[230,245,255],ribbon:[56,189,248],stem:[38,145,120],leaf:[55,185,155],
    flowers:[[34,175,255],[125,215,255],[168,125,255],[250,210,115],[240,253,255]],
    sparkle:[125,225,255],msgColors:[[56,189,248],[165,230,255],[192,132,252]]
  },
  amber:{
    bg:'#08080c',wrapPrimary:[225,180,130],wrapEdge:[255,225,175],ribbon:[245,158,11],stem:[65,135,65],leaf:[95,168,75],
    flowers:[[249,115,22],[251,191,36],[235,65,75],[253,230,110],[255,250,235]],
    sparkle:[253,224,71],msgColors:[[251,191,36],[249,115,22],[254,240,138]]
  }
};

const state={
  theme:'pink',speed:1.0,zoom:1.0,rotY:0.0,rotX:0.16,
  isDragging:false,lastMouseX:0,lastMouseY:0,t:0.0,message:"FOR YOU"
};

function mulberry32(a){
  return function(){
    let t=a+=0x6D2B79F5;
    t=Math.imul(t^t>>>15,t|1);
    t^=t+Math.imul(t^t>>>7,t|61);
    return((t^t>>>14)>>>0)/4294967296;
  };
}

let points=[];
function buildBouquet(){
  const rng=mulberry32(20261114);
  points=[];
  const Y_BOTTOM=-1.25,Y_WAIST=-0.38,Y_WRAP_TOP=0.42,Y_DOME_TOP=1.02;
  const getGrowTime=(y,extra=0)=>0.15+Math.max(0,Math.min(1,(y-Y_BOTTOM)/(Y_DOME_TOP-Y_BOTTOM)))*4.6+extra;

  for(let i=0;i<5400;i++){
    const u=Math.pow(rng(),0.78),y=Y_BOTTOM+u*(Y_WRAP_TOP-Y_BOTTOM);
    let radius=y<Y_WAIST?0.04+0.18*((y-Y_BOTTOM)/(Y_WAIST-Y_BOTTOM)):0.22+0.56*Math.pow((y-Y_WAIST)/(Y_WRAP_TOP-Y_WAIST),1.15);
    const theta=rng()*TAU;
    const folds=0.032*Math.sin(theta*9.0+u*2.5)*(0.3+u);
    const backLift=(y>Y_WAIST)?Math.max(0,-Math.sin(theta))*0.26*((y-Y_WAIST)/(Y_WRAP_TOP-Y_WAIST)):0;
    const finalY=y+backLift;
    if((y>Y_WAIST+0.28)&&(Math.sin(theta)>0.35)&&rng()<0.78)continue;
    const r=radius+folds+(rng()-0.5)*0.015;
    const isRim=(u>0.93)||(Math.abs(Math.sin(theta*9.0))>0.94);
    points.push({x:r*Math.cos(theta),y:finalY,z:r*Math.sin(theta),colorType:isRim?'wrapEdge':'wrapPrimary',colorIdx:0,shade:0.65+0.35*u+(rng()-0.5)*0.15,tOn:getGrowTime(finalY),kind:0,phase:rng()*TAU,customChar:isRim?':':null});
  }

  for(let i=0;i<950;i++){
    const theta=rng()*TAU,bandH=(rng()-0.5)*0.085,r=0.235+0.015*Math.sin(theta*9.0),y=Y_WAIST+bandH;
    points.push({x:r*Math.cos(theta),y,z:r*Math.sin(theta),colorType:'ribbon',colorIdx:0,shade:0.9+0.25*Math.cos(bandH*30),tOn:getGrowTime(y,0.25),kind:2,phase:rng()*TAU,customChar:'@'});
  }

  for(let i=0;i<1100;i++){
    const side=i%2===0?1:-1;
    if(i<1100*0.62){
      const s=rng()*TAU,loopW=0.25*Math.sin(s),loopH=0.11*Math.sin(s*2.0)+(rng()-0.5)*0.035;
      const y=Y_WAIST+loopH;
      points.push({x:side*Math.abs(loopW)+side*0.03,y,z:0.25+0.08*Math.sin(s)+(rng()-0.5)*0.025,colorType:'ribbon',colorIdx:0,shade:1.05,tOn:getGrowTime(y,0.35),kind:2,phase:rng()*TAU,customChar:'#'});
    }else{
      const u=rng();
      points.push({x:side*(0.04+u*0.16+0.025*Math.sin(u*8)),y:Y_WAIST-u*0.44,z:0.25+0.04*Math.sin(u*6)+(rng()-0.5)*0.02,colorType:'ribbon',colorIdx:0,shade:0.92-u*0.2,tOn:getGrowTime(Y_WAIST,0.4+u*0.3),kind:2,phase:rng()*TAU,customChar:'*'});
    }
  }

  const flowerHeads=[];
  const tiers=[{count:1,ringR:0.00,y:0.94,scale:1.22,type:'rose'},{count:6,ringR:0.26,y:0.84,scale:1.08,type:'rose'},{count:12,ringR:0.48,y:0.66,scale:0.96,type:'mixed'}];
  let headIndex=0;
  for(const tier of tiers){
    for(let k=0;k<tier.count;k++){
      const ang=(k/tier.count)*TAU+(tier.ringR*2.1)+(rng()-0.5)*0.22;
      const r=tier.ringR+(rng()-0.5)*0.05;
      const hx=r*Math.cos(ang),hz=r*Math.sin(ang),hy=tier.y+(rng()-0.5)*0.06;
      const fType=tier.type==='mixed'?(k%3===0?'daisy':(k%3===1?'rose':'tulip')):tier.type;
      flowerHeads.push({x:hx,y:hy,z:hz,scale:tier.scale,type:fType,colorIdx:headIndex%4});
      headIndex++;
      for(let s=0;s<85;s++){
        const su=s/85,sy=(Y_BOTTOM+0.12)+su*(hy-(Y_BOTTOM+0.12));
        const spread=sy<Y_WAIST?0.25*(1-(sy-Y_BOTTOM)/(Y_WAIST-Y_BOTTOM)):(sy-Y_WAIST)/(hy-Y_WAIST);
        points.push({x:hx*spread+(rng()-0.5)*0.012,y:sy,z:hz*spread+(rng()-0.5)*0.012,colorType:'stem',colorIdx:0,shade:0.75+0.25*su,tOn:getGrowTime(sy),kind:0,phase:rng()*TAU,customChar:'|'});
      }
    }
  }

  for(let l=0;l<22;l++){
    const ang=(l/22)*TAU+rng()*0.2,baseR=0.44+rng()*0.14,baseY=0.44+rng()*0.18,leafLen=0.26+rng()*0.12,leafWid=0.11+rng()*0.04,droop=0.08+rng()*0.08;
    const dirX=Math.cos(ang),dirZ=Math.sin(ang),perpX=-dirZ,perpZ=dirX;
    for(let p=0;p<110;p++){
      const u=rng(),v=(rng()-0.5)*2.0;
      if(Math.abs(v)>Math.sin(u*Math.PI))continue;
      const lx=dirX*(baseR+u*leafLen)+perpX*(v*leafWid),lz=dirZ*(baseR+u*leafLen)+perpZ*(v*leafWid),ly=baseY+Math.sin(u*Math.PI*0.75)*0.11-u*u*droop;
      points.push({x:lx,y:ly,z:lz,colorType:'leaf',colorIdx:0,shade:0.65+0.45*u-0.2*Math.abs(v),tOn:getGrowTime(ly,0.15),kind:0,phase:rng()*TAU,customChar:null});
    }
  }

  for(const head of flowerHeads){
    const radialLen=Math.hypot(head.x,head.z),tiltAng=radialLen*0.85,azim=Math.atan2(head.z,head.x);
    const cosT=Math.cos(tiltAng),sinT=Math.sin(tiltAng),cosA=Math.cos(azim),sinA=Math.sin(azim);
    const orientLocal=(lx,ly,lz)=>{
      const rx=lx*cosT+ly*sinT,ry=-lx*sinT+ly*cosT;
      return{x:head.x+(rx*cosA-lz*sinA),y:head.y+ry,z:head.z+(rx*sinA+lz*cosA)};
    };
    if(head.type==='rose'){
      for(let p=0;p<380;p++){
        const layer=Math.floor(rng()*4),u=(layer+rng())/4.0,theta=rng()*TAU*2.0,petalCup=Math.abs(Math.sin(theta*2.5+layer*1.1));
        const r=(0.03+u*0.135)*head.scale*(0.78+0.28*petalCup),cupHeight=(0.08*(1-u*0.55)+0.035*petalCup)*head.scale;
        const pt=orientLocal(r*Math.cos(theta),cupHeight,r*Math.sin(theta));
        points.push({x:pt.x,y:pt.y,z:pt.z,colorType:'flower',colorIdx:head.colorIdx,shade:0.65+0.45*u+0.15*petalCup,tOn:getGrowTime(pt.y,0.25+u*0.25),kind:1,phase:rng()*TAU,customChar:u>0.82?'@':null});
      }
    }else if(head.type==='daisy'){
      for(let c=0;c<70;c++){
        const r=Math.sqrt(rng())*0.052*head.scale,th=rng()*TAU,dome=Math.sqrt(Math.max(0,1-(r/(0.055*head.scale))**2))*0.03;
        const pt=orientLocal(r*Math.cos(th),dome,r*Math.sin(th));
        points.push({x:pt.x,y:pt.y,z:pt.z,colorType:'flower',colorIdx:3,shade:1.1,tOn:getGrowTime(pt.y,0.2),kind:2,phase:rng()*TAU,customChar:'@'});
      }
      for(let k=0;k<14;k++){
        const pAng=(k/14)*TAU;
        for(let j=0;j<18;j++){
          const u=rng(),v=(rng()-0.5)*Math.sin(u*Math.PI)*0.042,dist=(0.048+u*0.115)*head.scale;
          const pt=orientLocal(Math.cos(pAng)*dist-Math.sin(pAng)*v,Math.sin(u*Math.PI)*0.025*head.scale,Math.sin(pAng)*dist+Math.cos(pAng)*v);
          points.push({x:pt.x,y:pt.y,z:pt.z,colorType:'flower',colorIdx:4,shade:0.85+0.25*u,tOn:getGrowTime(pt.y,0.3+u*0.2),kind:1,phase:rng()*TAU,customChar:'*'});
        }
      }
    }else{
      for(let p=0;p<300;p++){
        const u=rng(),theta=rng()*TAU,r=(Math.sin(u*Math.PI*0.72)*0.11+0.015*Math.sin(theta*3.0))*head.scale;
        const pt=orientLocal(r*Math.cos(theta),u*0.17*head.scale,r*Math.sin(theta));
        points.push({x:pt.x,y:pt.y,z:pt.z,colorType:'flower',colorIdx:(head.colorIdx+1)%4,shade:0.72+0.4*u,tOn:getGrowTime(pt.y,0.25+u*0.2),kind:1,phase:rng()*TAU,customChar:null});
      }
    }
  }

  for(let b=0;b<650;b++){
    const th=rng()*TAU,r=0.15+rng()*0.48,y=0.56+(1-r/0.75)*0.42+(rng()-0.5)*0.12;
    points.push({x:r*Math.cos(th),y,z:r*Math.sin(th),colorType:'flower',colorIdx:4,shade:1.15,tOn:getGrowTime(y,0.45),kind:2,phase:rng()*TAU,customChar:'*'});
  }
  for(let sp=0;sp<240;sp++){
    const th=rng()*TAU,r=0.35+rng()*0.65,y=-0.8+rng()*2.0;
    points.push({x:r*Math.cos(th),y,z:r*Math.sin(th),colorType:'sparkle',colorIdx:0,shade:1.0,tOn:4.8+rng()*1.4,kind:3,phase:rng()*TAU,customChar:rng()>0.5?'✦':'.'});
  }
}

let width=0,height=0,dpr=1;
function resize(){
  dpr=Math.min(window.devicePixelRatio||1,2);
  width=window.innerWidth;height=window.innerHeight;
  canvas.width=Math.floor(width*dpr);canvas.height=Math.floor(height*dpr);
  ctx.setTransform(dpr,0,0,dpr,0,0);
}
window.addEventListener('resize',resize);
resize();
buildBouquet();

canvas.addEventListener('pointerdown',e=>{state.isDragging=true;state.lastMouseX=e.clientX;state.lastMouseY=e.clientY;canvas.setPointerCapture(e.pointerId)});
canvas.addEventListener('pointermove',e=>{if(!state.isDragging)return;state.rotY+=(e.clientX-state.lastMouseX)*0.008;state.rotX=Math.max(-0.65,Math.min(0.75,state.rotX+(e.clientY-state.lastMouseY)*0.006));state.lastMouseX=e.clientX;state.lastMouseY=e.clientY});
canvas.addEventListener('pointerup',()=>state.isDragging=false);
canvas.addEventListener('wheel',e=>{e.preventDefault();state.zoom=Math.max(0.55,Math.min(1.85,state.zoom-e.deltaY*0.001))},{passive:false});

window.addEventListener('message',e=>{
  const d=e.data||{};
  if(d.type==='SET_THEME'&&THEMES[d.theme])state.theme=d.theme;
  if(d.type==='SET_SPEED')state.speed=Number(d.speed)||0;
  if(d.type==='SET_TEXT')state.message=(d.text||'').toUpperCase();
  if(d.type==='REPLAY')state.t=0.0;
});

function getPointColor(pt,themeObj){
  switch(pt.colorType){
    case 'wrapPrimary':return themeObj.wrapPrimary;
    case 'wrapEdge':return themeObj.wrapEdge;
    case 'ribbon':return themeObj.ribbon;
    case 'stem':return themeObj.stem;
    case 'leaf':return themeObj.leaf;
    case 'sparkle':return themeObj.sparkle;
    default:return themeObj.flowers[pt.colorIdx%themeObj.flowers.length];
  }
}

let lastFrameTime=performance.now();
function render(now){
  const dt=Math.min(0.05,(now-lastFrameTime)/1000);
  lastFrameTime=now;
  state.t+=dt;
  if(!state.isDragging)state.rotY+=dt*0.55*state.speed;

  const t=state.t,themeObj=THEMES[state.theme];
  ctx.fillStyle=themeObj.bg;ctx.fillRect(0,0,width,height);

  const grad=ctx.createRadialGradient(width*0.5,height*0.45,20,width*0.5,height*0.45,Math.max(width,height)*0.52);
  const [gr,gg,gb]=themeObj.flowers[0];
  grad.addColorStop(0,\`rgba(\${gr},\${gg},\${gb},0.08)\`);
  grad.addColorStop(1,'rgba(0,0,0,0)');
  ctx.fillStyle=grad;ctx.fillRect(0,0,width,height);

  const isMobile=width<640,cw=isMobile?7.5:9.0,ch=isMobile?12.0:14.0;
  const cols=Math.floor(width/cw),rows=Math.floor(height/ch);
  const cy=Math.cos(state.rotY),sy=Math.sin(state.rotY),cx=Math.cos(state.rotX),sx=Math.sin(state.rotX);
  const camDist=3.25/state.zoom,fov=2.15,scaleFactor=Math.min(cols*0.44,rows*0.40);
  const totalCells=cols*rows;
  const zBuf=new Float32Array(totalCells);zBuf.fill(1e9);
  const charBuf=new Array(totalCells),colBuf=new Array(totalCells),glowBuf=new Uint8Array(totalCells);

  for(let i=0;i<points.length;i++){
    const pt=points[i];
    if(t<pt.tOn)continue;
    const fadeIn=Math.min(1.0,(t-pt.tOn)/0.35);
    let px=pt.x,py=pt.y,pz=pt.z;
    if(pt.kind===3){
      py=-0.9+(((py+0.9)+t*0.16)%2.1);
      px+=Math.sin(t*1.5+pt.phase)*0.06;
      pz+=Math.cos(t*1.3+pt.phase)*0.06;
    }
    const x1=px*cy-pz*sy,z1=px*sy+pz*cy;
    const y2=py*cx-z1*sx,z2=py*sx+z1*cx;
    const depth=camDist-z2;
    if(depth<=0.35)continue;
    const ix=Math.round((x1*fov/depth)*scaleFactor+cols*0.5);
    const iy=Math.round((-y2*fov/depth)*scaleFactor+rows*0.47);
    if(ix<0||ix>=cols||iy<0||iy>=rows)continue;
    const cellIdx=iy*cols+ix;
    if(depth<zBuf[cellIdx]){
      zBuf[cellIdx]=depth;
      let twinkle=1.0;
      if(pt.kind===1)twinkle=0.88+0.22*Math.sin(t*3.5+pt.phase);
      else if(pt.kind===2||pt.kind===3)twinkle=0.72+0.45*Math.sin(t*5.8+pt.phase);
      const intensity=pt.shade*Math.max(0.38,Math.min(1.3,(camDist+1.1-depth)/1.9))*fadeIn*twinkle;
      const baseRgb=getPointColor(pt,themeObj);
      const r=Math.min(255,Math.round(baseRgb[0]*intensity));
      const g=Math.min(255,Math.round(baseRgb[1]*intensity));
      const b=Math.min(255,Math.round(baseRgb[2]*intensity));
      let chGlyph=pt.customChar;
      if(!chGlyph){
        const lum=(0.299*r+0.587*g+0.114*b)/255;
        chGlyph=RAMP[Math.max(1,Math.min(RAMP.length-1,Math.floor(lum*(RAMP.length-1))))];
      }
      charBuf[cellIdx]=chGlyph;
      colBuf[cellIdx]=\`rgb(\${r},\${g},\${b})\`;
      glowBuf[cellIdx]=(pt.kind>=1&&intensity>0.85)?1:0;
    }
  }

  const T_MSG=2.2;
  if(t>=T_MSG&&state.message.trim().length>0){
    const spacedMsg=state.message.split('').join(' ');
    const msgRow=Math.min(rows-7,Math.floor(rows*0.79));
    const startCol=Math.max(1,Math.floor((cols-spacedMsg.length)/2));
    const shownChars=Math.min(spacedMsg.length,Math.floor((t-T_MSG)*20));
    for(let i=0;i<shownChars;i++){
      const chGlyph=spacedMsg[i];
      if(chGlyph===' ')continue;
      const cIdx=startCol+i;
      if(cIdx>=0&&cIdx<cols&&msgRow>=0&&msgRow<rows){
        const cellIdx=msgRow*cols+cIdx;
        const rgb=themeObj.msgColors[(Math.floor(i/2)+Math.floor(t*2))%themeObj.msgColors.length];
        const pulse=0.85+0.25*Math.sin(t*5+i*0.6);
        charBuf[cellIdx]=chGlyph;
        colBuf[cellIdx]=\`rgb(\${Math.min(255,Math.round(rgb[0]*pulse))},\${Math.min(255,Math.round(rgb[1]*pulse))},\${Math.min(255,Math.round(rgb[2]*pulse))})\`;
        glowBuf[cellIdx]=1;
      }
    }
  }

  ctx.font=\`700 \${Math.floor(ch*0.95)}px "JetBrains Mono",monospace\`;
  ctx.textAlign='center';ctx.textBaseline='middle';
  for(let r=0;r<rows;r++){
    const py=r*ch+ch*0.5;
    for(let c=0;c<cols;c++){
      const idx=r*cols+c,glyph=charBuf[idx];
      if(!glyph)continue;
      ctx.fillStyle=colBuf[idx];
      if(glowBuf[idx]){ctx.shadowColor=colBuf[idx];ctx.shadowBlur=8}else{ctx.shadowBlur=0}
      ctx.fillText(glyph,c*cw+cw*0.5,py);
    }
  }
  ctx.shadowBlur=0;
  requestAnimationFrame(render);
}
requestAnimationFrame(render);
<\/script>
</body>
</html>`,

      multilang_heart_v2: `<!DOCTYPE html>
<html lang="vi">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<style>
  html,body{height:100%;margin:0;background:#08080c;display:flex;align-items:center;justify-content:center;overflow:hidden}
  canvas{max-width:100%;max-height:100%;cursor:grab;display:block}
  canvas:active{cursor:grabbing}
</style>
</head>
<body>
<canvas id="c"></canvas>
<script>
const W=480,H=570,SC=2,PXU=190,HH=490,CXs=W/2,CYs=236,Y0c=0.14,FS=10,TAU=Math.PI*2,KZ=2.5,DY=0.058,NS=1440,KS=7,GT=0.022,GP=0.07,OMEGA=1.15;
const FONT='"Segoe UI","Noto Sans","Helvetica Neue",Arial,"Noto Sans CJK SC","Noto Sans Devanagari","Noto Sans Arabic","Noto Sans Thai",sans-serif';
const WORDS=["I love you","Anh y\u00EAu em","Em y\u00EAu anh","Te amo","Te quiero","Je t'aime","Ich liebe dich","Ti amo","Eu te amo","\u042F \u0442\u0435\u0431\u044F \u043B\u044E\u0431\u043B\u044E","Seni seviyorum","\u03A3' \u03B1\u03B3\u03B1\u03C0\u03CE","Mahal kita","Aloha wau i\u0101 \u02BBoe","Volim te","Jeg elsker dig","Jeg elsker deg","Ik hou van jou","Min\u00E4 rakastan sinua","Szeretlek","Jag \u00E4lskar dig","Kocham Ci\u0119","Te iubesc","Milujem \u0165a","Ljubim te","Nakupenda","In\u0127obbok","Miluji t\u011B","\u042F \u0442\u0435\u0431\u0435 \u043A\u043E\u0445\u0430\u044E","\u041E\u0431\u0438\u0447\u0430\u043C \u0442\u0435","T\u00EB dua","Es tevi m\u012Blu","A\u0161 tave myliu","Mina armastan sind","\u00C9g elska \u00FEig","T\u00E1 gr\u00E1 agam duit","Rwy'n dy garu di","Tha gaol agam ort","Maite zaitut","T'estimo","Qu\u00E9rote","Mi amas vin","Aku cinta kamu","Saya cintakan awak","Ek is lief vir jou","Ngiyakuthanda","Ina son ka","Waan ku jeclahay","Aroha au ki a koe","Ou te alofa ia te oe","Munakuyki","Rohayhu","Nimitztlazohtla","Mwen renmen w","Ech hunn dech g\u00E4r","Ik h\u00E2ld fan dy","Da garout a ran","Ti tengu caru","Ti v\u00F2gghiu beni","Ez te hez dikim","M\u0259n s\u0259ni sevir\u0259m","Men seni sevaman","\u041C\u0435\u043D \u0441\u0435\u043D\u0456 \u0441\u04AF\u0439\u0435\u043C\u0456\u043D","\u0411\u0438 \u0447\u0430\u043C\u0434 \u0445\u0430\u0439\u0440\u0442\u0430\u0439","\u10DB\u10D8\u10E7\u10D5\u10D0\u10E0\u10EE\u10D0\u10E0","\u0535\u057D \u0584\u0565\u0566 \u057D\u056B\u0580\u0578\u0582\u0574 \u0565\u0574","\u0422\u0435 \u0441\u0430\u043A\u0430\u043C",
"\u6211\u7231\u4F60","\u611B\u3057\u3066\u308B","\uC0AC\uB791\uD574","\u092E\u0948\u0902 \u0924\u0941\u092E\u0938\u0947 \u092A\u094D\u092F\u093E\u0930 \u0915\u0930\u0924\u093E \u0939\u0942\u0901","\u0623\u062D\u0628\u0643","\u062F\u0648\u0633\u062A\u062A \u062F\u0627\u0631\u0645","\u05D0\u05E0\u05D9 \u05D0\u05D5\u05D4\u05D1 \u05D0\u05D5\u05EA\u05DA","\u0E09\u0E31\u0E19\u0E23\u0E31\u0E01\u0E40\u0E18\u0E2D","\u0986\u09AE\u09BF \u09A4\u09CB\u09AE\u09BE\u0995\u09C7 \u09AD\u09BE\u09B2\u09CB\u09AC\u09BE\u09B8\u09BF","\u0BA8\u0BBE\u0BA9\u0BCD \u0B89\u0BA9\u0BCD\u0BA9\u0BC8 \u0B95\u0BBE\u0BA4\u0BB2\u0BBF\u0B95\u0BCD\u0B95\u0BBF\u0BB1\u0BC7\u0BA9\u0BCD","\u0C28\u0C47\u0C28\u0C41 \u0C28\u0C3F\u0C28\u0C4D\u0C28\u0C41 \u0C2A\u0C4D\u0C30\u0C47\u0C2E\u0C3F\u0C38\u0C4D\u0C24\u0C41\u0C28\u0C4D\u0C28\u0C3E\u0C28\u0C41","\u0645\u062C\u06BE\u06D2 \u062A\u0645 \u0633\u06D2 \u0645\u062D\u0628\u062A \u06C1\u06D2","\u092E \u0924\u093F\u092E\u0940\u0932\u093E\u0908 \u092E\u093E\u092F\u093E \u0917\u0930\u094D\u091B\u0941","\u0A2E\u0A48\u0A02 \u0A24\u0A48\u0A28\u0A42\u0A70 \u0A2A\u0A3F\u0A06\u0A30 \u0A15\u0A30\u0A26\u0A3E \u0A39\u0A3E\u0A02","\u1781\u17D2\u1789\u17BB\u17C6\u179F\u17D2\u179A\u17A1\u17B6\u1789\u17CB\u17A2\u17D2\u1793\u1780","\u092E\u0940 \u0924\u0941\u091D\u094D\u092F\u093E\u0935\u0930 \u092A\u094D\u0930\u0947\u092E \u0915\u0930\u0924\u094B","\u0AB9\u0AC1\u0A82 \u0AA4\u0AA8\u0AC7 \u0AAA\u0ACD\u0AB0\u0AC7\u0AAE \u0A95\u0AB0\u0AC1\u0A82 \u0A9B\u0AC1\u0A82","\u0D1E\u0D3E\u0D7B \u0D28\u0D3F\u0D28\u0D4D\u0D28\u0D46 \u0D38\u0D4D\u0D28\u0D47\u0D39\u0D3F\u0D15\u0D4D\u0D15\u0D41\u0D28\u0D4D\u0D28\u0D41","\u0DB8\u0DB8 \u0D94\u0DB6\u0DA7 \u0D86\u0DAF\u0DBB\u0DD9\u0DBA\u0DD2"];
let sd=9;const rnd=()=>{sd=sd+0x6D2B79F5|0;let a=Math.imul(sd^sd>>>15,1|sd);a=a+Math.imul(a^a>>>7,61|a)^a;return((a^a>>>14)>>>0)/4294967296};
const clamp=(v,a,b)=>Math.min(b,Math.max(a,v));
const cv=document.getElementById('c'),g=cv.getContext('2d');cv.width=W*SC;cv.height=H*SC;
const mk=(w,h)=>{const c=document.createElement('canvas');c.width=w;c.height=h;return c};
const st=mk(W*SC,H*SC),sg=st.getContext('2d'),hc=mk(W*SC,HH*SC),hg=hc.getContext('2d'),g4=mk(W*SC/4,HH*SC/4),g4c=g4.getContext('2d'),g16=mk(W*SC/16,HH*SC/16),g16c=g16.getContext('2d');

const PALETTES={
  pink:{stops:[[0,[28,6,26]],[0.3,[140,40,118]],[0.62,[232,128,200]],[1,[255,238,250]]],glow:'rgba(255,120,200,0.65)',txt:'rgb(245,232,244)'},
  green:{stops:[[0,[6,26,18]],[0.3,[20,130,82]],[0.62,[65,225,155]],[1,[225,255,240]]],glow:'rgba(0,255,136,0.65)',txt:'rgb(225,255,240)'},
  blue:{stops:[[0,[6,18,32]],[0.3,[28,105,175]],[0.62,[95,195,250]],[1,[230,248,255]]],glow:'rgba(56,189,248,0.65)',txt:'rgb(230,248,255)'},
  amber:{stops:[[0,[30,18,6]],[0.3,[165,98,22]],[0.62,[245,180,65]],[1,[255,246,220]]],glow:'rgba(251,191,36,0.65)',txt:'rgb(255,246,220)'}
};
let curTheme='pink',MSG='I LOVE U IN EVERY LANGUAGE',rotSpeed=1.0,LUT=[];

function rebuildTheme(){
  const pal=PALETTES[curTheme]||PALETTES.pink;
  const STOPS=pal.stops;
  function ramp(v,w){
    let i=0;while(i<STOPS.length-2&&v>STOPS[i+1][0])i++;
    const[a,ca]=STOPS[i],[b,cb]=STOPS[i+1],f=clamp((v-a)/(b-a),0,1);
    return\`rgb(\${Math.min(255,(ca[0]+(cb[0]-ca[0])*f)*(1-w)+255*w)|0},\${Math.min(255,(ca[1]+(cb[1]-ca[1])*f)*(1-w)+255*w)|0},\${Math.min(255,(ca[2]+(cb[2]-ca[2])*f)*(1-w)+255*w)|0})\`;
  }
  LUT=[];
  for(let vi=0;vi<=48;vi++){const row=[];for(let wi=0;wi<=4;wi++)row.push(ramp(vi/48,wi/4*0.6));LUT.push(row)}
  sg.setTransform(SC,0,0,SC,0,0);sg.fillStyle='#08080c';sg.fillRect(0,0,W,H);
  if(MSG){
    sg.font='300 18px "Segoe UI Light","Segoe UI","Helvetica Neue",Arial,sans-serif';sg.textAlign='center';sg.textBaseline='middle';
    if('letterSpacing' in sg)sg.letterSpacing='2.5px';
    sg.shadowColor=pal.glow;sg.shadowBlur=14;sg.fillStyle=pal.txt;sg.fillText(MSG,W/2,475);
    sg.shadowBlur=0;
  }
}
rebuildTheme();

const F=(x,y,z)=>{z*=KZ;const a=x*x+2.25*z*z+y*y-1;return a*a*a-(x*x+0.1125*z*z)*y*y*y};
function normal(x,y,z){const e=0.002,f=F(x,y,z),gx=(F(x+e,y,z)-f)/e,gy=(F(x,y+e,z)-f)/e,gz=(F(x,y,z+e)-f)/e,m=Math.hypot(gx,gy,gz)||1;return[gx/m,gy/m,gz/m]}
hg.font='600 '+FS+'px '+FONT;
const RTL=/[\u0590-\u08FF]/,order=WORDS.map(w=>({w,r:rnd()})).sort((a,b)=>a.r-b.r).map(o=>o.w),SEQ=[],PS=[];
order.forEach(p=>{const parts=RTL.test(p)?[p]:p.split(' ');PS.push(SEQ.length);parts.forEach((s,i)=>SEQ.push({s,w:hg.measureText(s).width/PXU,first:i==0}))});
const ITEMS=[];let rowN=0;
function ring(ox,y,ri){
 const P=[];
 for(let k=0;k<NS;k++){const th=-k*TAU/NS,dx=Math.cos(th),dz=Math.sin(th);let f=-1;
  for(let r=0.02;r<1.5;r+=0.02){if(F(ox+dx*r,y,dz*r)>0){let a=r-0.02,b=r;for(let q=0;q<6;q++){const m=(a+b)/2;if(F(ox+dx*m,y,dz*m)>0)b=m;else a=m}f=(a+b)/2;break}}
  if(f<0)return;P.push([ox+dx*f,dz*f])}
 const cum=[0];for(let k=1;k<NS;k++)cum.push(cum[k-1]+Math.hypot(P[k][0]-P[k-1][0],P[k][1]-P[k-1][1]));
 const L=cum[NS-1]+Math.hypot(P[0][0]-P[NS-1][0],P[0][1]-P[NS-1][1]);
 const pos=sq=>{sq=((sq%L)+L)%L;let lo=0,hi=NS-1;while(lo<hi){const m=(lo+hi+1)>>1;if(cum[m]<=sq)lo=m;else hi=m-1}
  const a=P[lo],b=P[(lo+1)%NS],e=lo<NS-1?cum[lo+1]:L,f=e>cum[lo]?(sq-cum[lo])/(e-cum[lo]):0;return[a[0]+(b[0]-a[0])*f,a[1]+(b[1]-a[1])*f]};
 const start=PS[(ri*5+(ri>>1))%PS.length];let best=null,sumW=0,gaps=0;
 for(let i=0;i<60;i++){const tk=SEQ[(start+i)%SEQ.length];sumW+=tk.w;gaps+=i?(tk.first?GP:GT):0;const k=(L-gaps-GP)/sumW;if(k<0.72)break;if(k<=1.45){const sc=Math.abs(Math.log(k));if(!best||sc<best.sc)best={n:i+1,k,sc}}}
 if(!best)return;
 let s=0;
 for(let i=0;i<best.n;i++){const tk=SEQ[(start+i)%SEQ.length];if(i)s+=tk.first?GP:GT;const wk=tk.w*best.k,sm=new Float32Array(KS*4);
  for(let q=0;q<KS;q++){const sq=s+wk*q/(KS-1),p=pos(sq),p1=pos(sq+0.012),p0=pos(sq-0.012),tx=p1[0]-p0[0],tz=p1[1]-p0[1],tm=Math.hypot(tx,tz)||1;sm[q*4]=p[0];sm[q*4+1]=p[1];sm[q*4+2]=-tz/tm;sm[q*4+3]=tx/tm}
  const c=pos(s+wk/2),n=normal(c[0],y,c[1]);
  ITEMS.push({s:tk.s,tpx:tk.w*PXU,kv:clamp(best.k,0.8,1.12),y,sm,nx:n[0],ny:n[1],nz:n[2],rb:rnd()});s+=wk}
}
for(let j=0;;j++){const y=-0.97+j*DY;if(y>1.26)break;
 if(F(0,y,0)<=0)ring(0,y,rowN++);
 else{let x1=-1,x2=-1;for(let x=0.01;x<1.4;x+=0.005){const f=F(x,y,0);if(x1<0&&f<=0)x1=x;else if(x1>=0&&f>0){x2=x;break}}
  if(x1>=0&&x2>=0){const c=(x1+x2)/2;ring(c,y,rowN);ring(-c,y,rowN+9);rowN++}}}

const LN=Math.hypot(-0.45,0.5,0.75);
let angle=0,last=0,drag=false,lx=0,zoom=1.0;
cv.addEventListener('pointerdown',e=>{drag=true;lx=e.clientX;cv.setPointerCapture(e.pointerId)});
cv.addEventListener('pointermove',e=>{if(!drag)return;angle+=(e.clientX-lx)*0.012;lx=e.clientX});
cv.addEventListener('pointerup',()=>drag=false);
cv.addEventListener('wheel',e=>{e.preventDefault();zoom=clamp(zoom*Math.exp(-e.deltaY*0.001),0.6,1.65)},{passive:false});

window.addEventListener('message',e=>{
  const d=e.data||{};
  if(d.type==='SET_THEME'&&PALETTES[d.theme]){curTheme=d.theme;rebuildTheme()}
  if(d.type==='SET_SPEED')rotSpeed=Number(d.speed)||0;
  if(d.type==='SET_TEXT'){MSG=(d.text||'').toUpperCase();rebuildTheme()}
  if(d.type==='REPLAY'){angle=0}
});

function heart(a,nowSec){
 const ca=Math.cos(a),sa=Math.sin(a);
 hg.setTransform(1,0,0,1,0,0);hg.clearRect(0,0,hc.width,hc.height);hg.textAlign='center';hg.textBaseline='middle';hg.font='600 '+FS+'px '+FONT;
 for(const it of ITEMS){let xmin=1e9,xmax=-1e9,cnt=0,nzs=0;const sm=it.sm;
  for(let i=0;i<KS;i++){const nzw=-sm[i*4+2]*sa+sm[i*4+3]*ca;if(nzw>0.02){const xw=sm[i*4]*ca+sm[i*4+1]*sa;if(xw<xmin)xmin=xw;if(xw>xmax)xmax=xw;cnt++;nzs+=nzw}}
  if(cnt<2)continue;const wpx=(xmax-xmin)*PXU;if(wpx<1.2)continue;
  const nzm=nzs/cnt,nxw=it.nx*ca+it.nz*sa,df=Math.max(0,(nxw*-0.45+it.ny*0.5+nzm*0.75)/LN),rim=Math.pow(1-nzm,2.2),tr=0.45*rim*(0.4+0.6*df);
  const v=clamp((0.1+0.95*Math.pow(df,1.1)+tr)*(0.84+0.28*it.rb)*(0.96+0.04*Math.sin(nowSec*2+it.rb*9)),0,1);
  hg.setTransform(SC*zoom*wpx/it.tpx,0,0,SC*zoom*it.kv,SC*(CXs+(xmin+xmax)/2*PXU*zoom),SC*(CYs-(it.y-Y0c)*PXU*zoom));
  hg.fillStyle=LUT[(v*48)|0][clamp(Math.round(tr/0.45*4),0,4)];hg.fillText(it.s,0,0)}
}

function frame(now){requestAnimationFrame(frame);const dt=Math.min(0.05,(now-last)/1000||0);last=now;
 if(!drag)angle+=dt*OMEGA*rotSpeed;
 heart(angle,now/1000);
 g4c.imageSmoothingQuality=g16c.imageSmoothingQuality='high';
 g4c.clearRect(0,0,g4.width,g4.height);g4c.drawImage(hc,0,0,g4.width,g4.height);
 g16c.clearRect(0,0,g16.width,g16.height);g16c.drawImage(g4,0,0,g16.width,g16.height);
 g.setTransform(1,0,0,1,0,0);g.globalCompositeOperation='source-over';g.globalAlpha=1;g.drawImage(st,0,0);
 g.imageSmoothingEnabled=true;g.imageSmoothingQuality='high';
 g.globalCompositeOperation='lighter';g.gba=0;g.drawImage(g16,0,0,hc.width,hc.height);g.drawImage(g16,0,0,hc.width,hc.height);g.globalAlpha=0.8;g.drawImage(g4,0,0,hc.width,hc.height);
 g.globalCompositeOperation='source-over';g.globalAlpha=1;g.drawImage(hc,0,0)}
requestAnimationFrame(frame);
<\/script>
</body>
</html>`
    };

    // =========================================================================
    // 3. ĐIỀU HƯỚNG 2 CHẾ ĐỘ CHÍNH & 3 TAB NGUỒN DỮ LIỆU
    // =========================================================================
    function switchMainMode(mode) {
      currentMainMode = mode;
      const isCard = mode === 'card';

      document.getElementById('nav-card').className = isCard
        ? 'px-3 md:px-4 py-1.5 rounded-lg text-xs font-semibold whitespace-nowrap transition-all flex items-center gap-1.5 bg-studio-card text-white border border-studio-border shadow-sm'
        : 'px-3 md:px-4 py-1.5 rounded-lg text-xs font-semibold whitespace-nowrap transition-all flex items-center gap-1.5 text-slate-400 hover:text-white border border-transparent';
      document.getElementById('nav-studio').className = !isCard
        ? 'px-3 md:px-4 py-1.5 rounded-lg text-xs font-semibold whitespace-nowrap transition-all flex items-center gap-1.5 bg-studio-card text-white border border-studio-border shadow-sm'
        : 'px-3 md:px-4 py-1.5 rounded-lg text-xs font-semibold whitespace-nowrap transition-all flex items-center gap-1.5 text-slate-400 hover:text-white border border-transparent';

      const studioLeft = document.getElementById('studioLeftPanel');
      const cardRight = document.getElementById('cardRightPanel');
      const cardFooter = document.getElementById('cardStageFooter');
      const recHeader = document.getElementById('stageRecipientHeader');

      if (isCard) {
        studioLeft.classList.add('hidden');
        studioLeft.classList.remove('flex');
        if (!isRecipientView) cardRight.classList.remove('hidden');
        cardFooter.classList.remove('hidden');
        recHeader.classList.remove('hidden');
      } else {
        studioLeft.classList.remove('hidden');
        studioLeft.classList.add('flex');
        cardRight.classList.add('hidden');
        cardFooter.classList.add('hidden');
        recHeader.classList.add('hidden');
      }
    }

    function switchStudioSource(src) {
      currentStudioSource = src;
      ['presets', 'html', 'image'].forEach(k => {
        const active = k === src;
        document.getElementById('srcTab-' + k).className = active
          ? 'py-1.5 px-2 rounded-lg bg-studio-card text-white border border-studio-border transition'
          : 'py-1.5 px-2 rounded-lg text-slate-400 hover:text-white border border-transparent transition';
        document.getElementById('sourcePane-' + k).classList.toggle('hidden', !active);
      });

      if (src === 'presets') {
        selectBuiltInModel(currentModelId);
      } else if (src === 'html') {
        runCustomHtmlCode(false);
      } else if (src === 'image') {
        if (!loadedImageObj) loadSamplePortrait();
        else renderImageToAscii();
      }
    }

    function activateStageLayer(type) {
      activeRenderType = type;
      presetStageWrapper.classList.toggle('hidden', type !== 'preset');
      htmlSandboxWrapper.classList.toggle('hidden', type !== 'html');
      asciiContainer.classList.toggle('hidden', type !== 'image');
    }

    // =========================================================================
    // 4. ĐỒNG BỘ MÔ HÌNH 3D VỚI BẢNG THIẾT KẾ THIỆP & KHO SÁNG TẠO
    // =========================================================================
    let activePresetBlobUrl = null;
   let activeSandboxBlobUrl = null;

   function setIframeHtmlSafe(iframeEl, htmlString, isPreset) {
     if (!iframeEl) return;
     try {
       const blob = new Blob([htmlString], { type: 'text/html;charset=utf-8' });
       const url = URL.createObjectURL(blob);
       if (isPreset) {
         if (activePresetBlobUrl) URL.revokeObjectURL(activePresetBlobUrl);
         activePresetBlobUrl = url;
       } else {
         if (activeSandboxBlobUrl) URL.revokeObjectURL(activeSandboxBlobUrl);
         activeSandboxBlobUrl = url;
       }
       iframeEl.removeAttribute('srcdoc');
       iframeEl.src = url;
     } catch (err) {
       iframeEl.srcdoc = htmlString;
     }
   }
    function postToPresetFrame(payload) {
      if (presetStageFrame && presetStageFrame.contentWindow) {
        presetStageFrame.contentWindow.postMessage(payload, '*');
      }
    }

    function syncAllModelParams() {
      const morphInput = document.getElementById('inputMorphWord');
      const word = morphInput ? morphInput.value.trim() : 'FOR YOU';
      const heartWordsInput = document.getElementById('inputHeartWords');
      const rawHeartWords = heartWordsInput ? heartWordsInput.value.trim() : '';
      const wordsArr = rawHeartWords ? rawHeartWords.split(/[,|;\n]+/).map(s => s.trim()).filter(Boolean) : [];
      postToPresetFrame({ type: 'SET_THEME', theme: themeKey });
      postToPresetFrame({ type: 'SET_SPEED', speed: rotSpeed });
      postToPresetFrame({ type: 'SET_TEXT', text: word });
      postToPresetFrame({ type: 'SET_HEART_WORDS', words: wordsArr });
    }

    function loadPresetIframe(modelId) {
      currentModelId = modelId;
      activateStageLayer('preset');
      const tpl = PRESET_HTML_TEMPLATES[modelId] || PRESET_HTML_TEMPLATES.bouquet;
      presetStageFrame.onload = () => syncAllModelParams();
      setIframeHtmlSafe(presetStageFrame, tpl, true);

      const bhExtra = document.getElementById('blackholeExtraControls');
      if (bhExtra) bhExtra.classList.toggle('hidden', modelId !== 'blackhole');

      const heartCtrl = document.getElementById('heartWordsControl');
      if (heartCtrl) heartCtrl.classList.toggle('hidden', modelId !== 'multilang_heart');
    }

    function selectBuiltInModel(modelId) {
      loadPresetIframe(modelId);
      document.querySelectorAll('.model-card').forEach(card => {
        const isSel = card.getAttribute('data-model') === modelId;
        card.className = isSel
          ? 'model-card p-3 rounded-2xl bg-studio-card border-2 border-studio-pink text-left transition hover:bg-[#202030]'
          : 'model-card p-3 rounded-2xl bg-studio-bg border border-studio-border text-left transition hover:bg-studio-card';
      });
    }

    function quickSelectCardModel(modelId) {
      loadPresetIframe(modelId);
      const badge = document.getElementById('activeCustomAssetBadge');
      if (badge) badge.classList.add('hidden');
      document.querySelectorAll('.q-model-btn').forEach(btn => {
        const isSel = btn.id === 'qModel-' + modelId;
        btn.className = isSel
          ? 'q-model-btn py-1.5 px-2.5 rounded-xl bg-studio-card border border-studio-pink text-white font-medium truncate text-left'
          : 'q-model-btn py-1.5 px-2.5 rounded-xl bg-studio-panel border border-studio-border text-slate-400 hover:text-white truncate text-left';
      });
    }

    function updateModelSpeed(val) {
      rotSpeed = parseFloat(val);
      const lbl = rotSpeed.toFixed(1) + 'x';
      const s1 = document.getElementById('cardSpeedLbl');
      const s2 = document.getElementById('studioSpeedLbl');
      const sl1 = document.getElementById('cardSpeedSlider');
      const sl2 = document.getElementById('studioSpeedSlider');
      if (s1) s1.textContent = lbl;
      if (s2) s2.textContent = lbl;
      if (sl1) sl1.value = rotSpeed;
      if (sl2) sl2.value = rotSpeed;
      postToPresetFrame({ type: 'SET_SPEED', speed: rotSpeed });
    }

    function updateModelCaptionText(val) {
      postToPresetFrame({ type: 'SET_TEXT', text: val.trim() });
    }

    function updateHeartInnerWords(val) {
      const wordsArr = val.split(/[,|;\n]+/).map(s => s.trim()).filter(Boolean);
      postToPresetFrame({ type: 'SET_HEART_WORDS', words: wordsArr });
    }

    function resetHeartInnerWords() {
      const inp = document.getElementById('inputHeartWords');
      if (inp) inp.value = '';
      postToPresetFrame({ type: 'SET_HEART_WORDS', words: [] });
      showToast('Đã khôi phục 80+ ngôn ngữ trong trái tim!');
    }

    function triggerModelReplay() {
      postToPresetFrame({ type: 'REPLAY' });
    }

    function setBlackholeRenderMode(modeIdx) {
      postToPresetFrame({ type: 'SET_BH_MODE', mode: modeIdx });
      [0, 1, 2].forEach(i => {
        const btn = document.getElementById('bhMode-' + i);
        if (!btn) return;
        btn.className = i === modeIdx
          ? 'bh-mode-btn py-1.5 rounded-lg bg-studio-card border border-studio-pink text-white font-semibold'
          : 'bh-mode-btn py-1.5 rounded-lg bg-studio-panel border border-studio-border text-slate-400';
      });
    }

    // =========================================================================
    // 5. TAB B: NHÚNG & LƯU MÃ HTML SANDBOX
    // =========================================================================
    const HTML_STORAGE_KEY = 'AsciiStudioSavedHtmlPieces_v1';
   let savedHtmlPieces = [];
   try {
     const rawSaved = localStorage.getItem(HTML_STORAGE_KEY);
     if (rawSaved) savedHtmlPieces = JSON.parse(rawSaved) || [];
   } catch (e) {
     savedHtmlPieces = [];
   }

   function persistSavedHtmlPieces() {
     try {
       localStorage.setItem(HTML_STORAGE_KEY, JSON.stringify(savedHtmlPieces));
     } catch (e) {
       console.warn('Không thể lưu vào localStorage:', e);
     }
   }

   function handleHtmlFileUpload(e) {
     const file = e.target.files[0];
     if (!file) return;
     const reader = new FileReader();
     reader.onload = ev => {
       document.getElementById('customHtmlTitle').value = file.name.replace(/\.html?$/i, '');
       document.getElementById('htmlCodeEditor').value = ev.target.result;
       runCustomHtmlCode(false);
     };
     reader.readAsText(file);
   }

   function runCustomHtmlCode(saveToList) {
     const titleEl = document.getElementById('customHtmlTitle');
     const editorEl = document.getElementById('htmlCodeEditor');
     const title = (titleEl ? titleEl.value.trim() : '') || 'Tác phẩm HTML';
     const code = editorEl ? editorEl.value : '';
     activateStageLayer('html');
     setIframeHtmlSafe(htmlSandboxFrame, code, false);

     if (saveToList) {
       savedHtmlPieces.unshift({ id: Date.now(), title, code });
       persistSavedHtmlPieces();
       renderSavedHtmlList();
       showToast('Đã lưu tác phẩm "' + title + '"!');
     }
   }

   function deleteSavedHtmlPiece(ev, id) {
     ev.stopPropagation();
     savedHtmlPieces = savedHtmlPieces.filter(x => x.id !== id);
     persistSavedHtmlPieces();
     renderSavedHtmlList();
     showToast('Đã xóa tác phẩm HTML!');
   }

   function renderSavedHtmlList() {
     const box = document.getElementById('savedHtmlList');
     if (!box) return;
     if (savedHtmlPieces.length === 0) {
       box.innerHTML = '<div class="text-[11px] text-slate-500 italic">Chưa có tác phẩm nào được lưu.</div>';
       return;
     }
     box.innerHTML = savedHtmlPieces.map(item => `
       <div onclick="loadSavedHtmlPiece(${item.id})" class="w-full text-left px-2.5 py-1.5 rounded-lg bg-studio-bg hover:bg-studio-card border border-studio-border text-xs text-slate-200 flex items-center justify-between gap-2 cursor-pointer transition">
         <span class="truncate">📄 ${item.title}</span>
         <div class="flex items-center gap-1.5 shrink-0">
           <span class="text-[10px] text-studio-blue">Mở</span>
           <button type="button" onclick="deleteSavedHtmlPiece(event, ${item.id})" class="text-[10px] text-slate-500 hover:text-rose-400 px-1" title="Xóa">✕</button>
         </div>
       </div>
     `).join('');
   }

   function loadSavedHtmlPiece(id) {
     const found = savedHtmlPieces.find(x => x.id === id);
     if (!found) return;
     document.getElementById('customHtmlTitle').value = found.title;
     document.getElementById('htmlCodeEditor').value = found.code;
     runCustomHtmlCode(false);
   }
   renderSavedHtmlList();
    

    // =========================================================================
    // 6. TAB C: CHUYỂN ẢNH SANG ASCII (TÍCH HỢP BỘ LOOK TỪ ASCII-MAGIC-ART)
    // =========================================================================
    let loadedImageObj = null;
    let imgAsciiCols = 92;
    let imgContrast = 120;
    let imgFullColor = false;
    let imgInvert = false;
    let currentAsciiLook = 'ascii';

    // Các bộ ký tự chuẩn (sắp xếp theo mật độ mực từ sáng đến tối) từ kailashsr38/ascii-magic-art
    const ASCII_MAGIC_LOOKS = {
      ascii:   " .,-~:;=!*#$@",
      dense:   " .'`^\",:;Il!i><~+_-?][}{1)(|/tfjrxnuvczXYUJCLQ0OZmwqpdbkhao*#MW&8%B@$",
      dots:    " ·•●",
      blocks:  " ░▒▓█",
      squares: " ·▪■",
      dither:  " ░▚▞▓█",
      numbers: " 1723456980",
      binary:  " 01",
      letters: " ilcoeaxkdbAHMW",
      lines:   " ╌─━═"
    };

    // Ma trận Bayer 4x4 cho hiệu ứng Dither sắc nét
    const BAYER_4X4 = [
      [ 0,  8,  2, 10],
      [12,  4, 14,  6],
      [ 3, 11,  1,  9],
      [15,  7, 13,  5]
    ];

    function setAsciiLook(lookKey) {
      currentAsciiLook = lookKey;
      const customBox = document.getElementById('customCharsBox');
      const previewEl = document.getElementById('imgLookPreview');
      if (lookKey === 'custom') {
        customBox.classList.remove('hidden');
        previewEl.textContent = 'Tùy chỉnh';
      } else {
        customBox.classList.add('hidden');
        const ramp = ASCII_MAGIC_LOOKS[lookKey] || ASCII_MAGIC_LOOKS.ascii;
        previewEl.textContent = ramp.trim().slice(0, 14);
      }
      renderImageToAscii();
    }

    function toggleImgInvert() {
      imgInvert = !imgInvert;
      const btn = document.getElementById('btnImgInvert');
      btn.textContent = imgInvert ? '🌓 Đảo sáng: Bật' : '🌓 Đảo sáng: Tắt';
      btn.className = imgInvert
        ? 'w-full py-1 rounded-lg bg-studio-card border border-studio-pink text-[10px] text-white font-semibold transition'
        : 'w-full py-1 rounded-lg bg-studio-panel hover:bg-studio-card border border-studio-border text-[10px] text-slate-200 font-medium transition';
      renderImageToAscii();
    }

    function copyAsciiArtText() {
      const text = asciiStage.textContent;
      if (!text || !text.trim()) {
        showToast('Chưa có nội dung ASCII để sao chép!');
        return;
      }
      const ta = document.createElement('textarea');
      ta.value = text;
      document.body.appendChild(ta);
      ta.select();
      document.execCommand('copy');
      document.body.removeChild(ta);
      showToast('Đã sao chép tranh ASCII vào bộ nhớ tạm!');
    }

    function handleImageUpload(e) {
      const file = e.target.files[0];
      if (!file) return;
      const reader = new FileReader();
      reader.onload = (ev) => {
        const img = new Image();
        img.onload = () => {
          loadedImageObj = img;
          renderImageToAscii();
        };
        img.src = ev.target.result;
      };
      reader.readAsDataURL(file);
    }

    function loadSamplePortrait() {
      const off = document.createElement('canvas');
      off.width = 200;
      off.height = 200;
      const octx = off.getContext('2d');
      octx.fillStyle = '#08080c';
      octx.fillRect(0, 0, 200, 200);

      // Vẽ chân dung nghệ thuật có chiều sâu ánh sáng để thử các Look của ascii-magic-art
      const grad = octx.createRadialGradient(92, 82, 12, 100, 100, 88);
      grad.addColorStop(0, '#ffffff');
      grad.addColorStop(0.48, '#ff5c8a');
      grad.addColorStop(0.82, '#7c3aed');
      grad.addColorStop(1, '#08080c');
      octx.fillStyle = grad;
      octx.beginPath();
      octx.arc(100, 100, 82, 0, Math.PI * 2);
      octx.fill();

      octx.fillStyle = '#08080c';
      octx.beginPath();
      octx.arc(76, 84, 9, 0, Math.PI * 2);
      octx.arc(124, 84, 9, 0, Math.PI * 2);
      octx.fill();

      octx.strokeStyle = '#08080c';
      octx.lineWidth = 6;
      octx.lineCap = 'round';
      octx.beginPath();
      octx.arc(100, 104, 28, 0.16 * Math.PI, 0.84 * Math.PI);
      octx.stroke();

      const img = new Image();
      img.onload = () => {
        loadedImageObj = img;
        renderImageToAscii();
      };
      img.src = off.toDataURL();
    }

    function setImgColorMode(fullColor) {
      imgFullColor = fullColor;
      document.getElementById('btnImgMono').className = !fullColor
        ? 'py-1 rounded-md bg-studio-card text-white font-medium'
        : 'py-1 rounded-md text-slate-400 hover:text-white font-medium';
      document.getElementById('btnImgFullColor').className = fullColor
        ? 'py-1 rounded-md bg-studio-card text-white font-medium'
        : 'py-1 rounded-md text-slate-400 hover:text-white font-medium';
      renderImageToAscii();
    }

    function getActiveAsciiRamp() {
      if (currentAsciiLook === 'custom') {
        const raw = document.getElementById('inputCustomChars').value;
        return raw && raw.length >= 2 ? raw : ' .oO@';
      }
      return ASCII_MAGIC_LOOKS[currentAsciiLook] || ASCII_MAGIC_LOOKS.ascii;
    }

    function renderImageToAscii() {
      if (!loadedImageObj) return;
      activateStageLayer('image');
      const ramp = getActiveAsciiRamp();
      const ratio = loadedImageObj.height / loadedImageObj.width;
      const cols = imgAsciiCols;
      const rows = Math.max(14, Math.round(cols * ratio * 0.54));

      // Tự động co giãn cỡ chữ pre#asciiStage cho vừa khung sân khấu
      const stageW = centerStage.clientWidth || 720;
      const stageH = centerStage.clientHeight || 560;
      const fontPx = Math.max(5, Math.min(13, Math.floor(Math.min((stageW * 0.86) / (cols * 0.62), (stageH * 0.82) / rows))));
      asciiStage.style.fontSize = fontPx + 'px';

      const off = document.createElement('canvas');
      off.width = cols;
      off.height = rows;
      const octx = off.getContext('2d');
      octx.filter = `contrast(${imgContrast}%)`;
      octx.drawImage(loadedImageObj, 0, 0, cols, rows);

      const data = octx.getImageData(0, 0, cols, rows).data;
      const useDither = currentAsciiLook === 'dither' || currentAsciiLook === 'binary';

      if (!imgFullColor) {
        let lines = [];
        for (let y = 0; y < rows; y++) {
          let rowStr = '';
          for (let x = 0; x < cols; x++) {
            const idx = (y * cols + x) * 4;
            let lum = (0.299 * data[idx] + 0.587 * data[idx + 1] + 0.114 * data[idx + 2]) / 255;
            if (imgInvert) lum = 1 - lum;
            if (useDither) {
              const threshold = (BAYER_4X4[y % 4][x % 4] / 16 - 0.5) * 0.28;
              lum = Math.max(0, Math.min(1, lum + threshold));
            }
            const charIdx = Math.min(ramp.length - 1, Math.floor(lum * (ramp.length - 1)));
            rowStr += ramp[charIdx];
          }
          lines.push(rowStr);
        }
        asciiStage.textContent = lines.join('\n');
      } else {
        let htmlLines = [];
        for (let y = 0; y < rows; y++) {
          let rowHtml = '';
          for (let x = 0; x < cols; x++) {
            const idx = (y * cols + x) * 4;
            const r = data[idx], g = data[idx + 1], b = data[idx + 2];
            let lum = (0.299 * r + 0.587 * g + 0.114 * b) / 255;
            if (imgInvert) lum = 1 - lum;
            if (useDither) {
              const threshold = (BAYER_4X4[y % 4][x % 4] / 16 - 0.5) * 0.28;
              lum = Math.max(0, Math.min(1, lum + threshold));
            }
            const charIdx = Math.min(ramp.length - 1, Math.floor(lum * (ramp.length - 1)));
            const rawCh = ramp[charIdx];
            const ch = rawCh === ' ' ? '&nbsp;' : (rawCh === '<' ? '&lt;' : (rawCh === '>' ? '&gt;' : rawCh));
            rowHtml += `<span style="color:rgb(${r},${g},${b})">${ch}</span>`;
          }
          htmlLines.push(rowHtml);
        }
        asciiStage.innerHTML = htmlLines.join('<br>');
      }
    }

    function applyAssetToGreetingCard() {
      switchMainMode('card');
      const badge = document.getElementById('activeCustomAssetBadge');
      if (activeRenderType === 'html' || activeRenderType === 'image') {
        badge.classList.remove('hidden');
        document.querySelectorAll('.q-model-btn').forEach(b => {
          b.className = 'q-model-btn py-1.5 px-2.5 rounded-xl bg-studio-panel border border-studio-border text-slate-400 hover:text-white truncate text-left';
        });
      } else {
        quickSelectCardModel(currentModelId);
      }
      showToast('Đã đưa lưu tác phẩm!');
    }

    // =========================================================================
    // 7. NỘI DUNG THIỆP, HIỂN THỊ LỜI CHÚC & XEM THỬ GÓC NHÌN NGƯỜI NHẬN
    // =========================================================================
    function startTypewriter(fullText) {
      const el = document.getElementById('typewriterOutput');
      el.textContent = fullText;
    }

    function updateCardTexts() {
      const name = document.getElementById('inputRecipient').value.trim() || 'Bạn';
      document.getElementById('stageRecipientName').textContent = name;
    }

    function setThemeColor(key, hex, glow) {
      themeKey = key;
      themeHex = hex;
      themeGlow = glow;
      document.body.style.setProperty('--accent-color', hex);
      asciiStage.style.color = hex;
      asciiStage.style.textShadow = `0 0 10px ${glow}`;
      document.getElementById('stageThemeDot').style.backgroundColor = hex;

      document.querySelectorAll('.color-theme-btn').forEach(btn => {
        btn.classList.remove('border-[#ff3b6b]', 'border-[#00ff88]', 'border-[#38bdf8]', 'border-[#fbbf24]', 'text-white');
        btn.classList.add('border-studio-border', 'text-slate-300');
      });
      const activeColorBtn = document.getElementById('colorBtn-' + key);
      if (activeColorBtn) {
        activeColorBtn.classList.remove('border-studio-border', 'text-slate-300');
        activeColorBtn.classList.add('text-white');
        activeColorBtn.style.borderColor = hex;
      }

      postToPresetFrame({ type: 'SET_THEME', theme: key });
    }

    function toggleRecipientPreview(enable) {
      isRecipientView = enable;
      if (enable) {
        switchMainMode('card');
        document.getElementById('topBar').classList.add('hidden');
        document.getElementById('cardRightPanel').classList.add('hidden');
        document.getElementById('stageControlsHint').classList.add('hidden');
        document.getElementById('stageQuickControls').classList.add('hidden');
        document.getElementById('recipientExitBar').classList.remove('hidden');
        startTypewriter(document.getElementById('inputLongMessage').value);
        postToPresetFrame({ type: 'REPLAY' });
        if (!isPlayingMusic) {
          pendingAutoMusic = true;
          toggleMusicPlay();
        }
      } else {
        pendingAutoMusic = false;
        document.getElementById('topBar').classList.remove('hidden');
        document.getElementById('cardRightPanel').classList.remove('hidden');
        document.getElementById('stageControlsHint').classList.remove('hidden');
        document.getElementById('stageQuickControls').classList.remove('hidden');
        document.getElementById('recipientExitBar').classList.add('hidden');
      }
    }

    // =========================================================================
    // 9. TRÌNH PHÁT GIAI ĐIỆU HỘP NHẠC (WEB AUDIO API)
    // =========================================================================
    let audioCtx = null;
    let isPlayingMusic = false;
    let musicLoopTimer = null;
    let customAudioEl = null;
    
    // --- THÊM MỚI TỪ ĐÂY ---
    let savedTracks = [];
    let activeSavedTrackId = null;

    const AUDIO_DB_NAME = 'AsciiStudioAudioDB';
    const AUDIO_STORE_NAME = 'tracks';

    // Mở kết nối IndexedDB trên trình duyệt
    function openAudioDB() {
      return new Promise((resolve, reject) => {
        if (!window.indexedDB) return reject(new Error('Trình duyệt không hỗ trợ IndexedDB'));
        const req = indexedDB.open(AUDIO_DB_NAME, 1);
        req.onupgradeneeded = (e) => {
          const db = e.target.result;
          if (!db.objectStoreNames.contains(AUDIO_STORE_NAME)) {
            db.createObjectStore(AUDIO_STORE_NAME, { keyPath: 'id' });
          }
        };
        req.onsuccess = () => resolve(req.result);
        req.onerror = () => reject(req.error);
      });
    }

    // Lưu tệp nhạc (Blob) vào IndexedDB
    async function saveAudioTrackToDB(trackObj) {
      try {
        const db = await openAudioDB();
        await new Promise((resolve, reject) => {
          const tx = db.transaction(AUDIO_STORE_NAME, 'readwrite');
          tx.objectStore(AUDIO_STORE_NAME).put(trackObj);
          tx.oncomplete = () => resolve();
          tx.onerror = () => reject(tx.error);
        });
      } catch (err) {
        console.warn('Không thể lưu nhạc vào IndexedDB:', err);
      }
    }

    // Xóa 1 bài nhạc khỏi IndexedDB
    async function deleteAudioTrackFromDB(id) {
      try {
        const db = await openAudioDB();
        await new Promise((resolve, reject) => {
          const tx = db.transaction(AUDIO_STORE_NAME, 'readwrite');
          tx.objectStore(AUDIO_STORE_NAME).delete(id);
          tx.oncomplete = () => resolve();
          tx.onerror = () => reject(tx.error);
        });
      } catch (err) {
        console.warn('Lỗi xóa tệp nhạc:', err);
      }
    }

    // Đọc toàn bộ nhạc đã lưu khi mở lại trang
    async function loadSavedAudioTracks() {
      try {
        const db = await openAudioDB();
        const items = await new Promise((resolve, reject) => {
          const tx = db.transaction(AUDIO_STORE_NAME, 'readonly');
          const req = tx.objectStore(AUDIO_STORE_NAME).getAll();
          req.onsuccess = () => resolve(req.result || []);
          req.onerror = () => reject(req.error);
        });
        savedTracks = items
          .sort((a, b) => b.createdAt - a.createdAt)
          .map(item => ({
            id: item.id,
            name: item.name,
            blob: item.blob,
            url: URL.createObjectURL(item.blob)
          }));
        renderSavedAudioList();
      } catch (err) {
        console.warn('Không thể tải danh sách nhạc đã lưu:', err);
      }
    }

    // Vẽ danh sách các bài nhạc đã lưu ra giao diện
    function renderSavedAudioList() {
      const section = document.getElementById('savedAudioSection');
      const listEl = document.getElementById('savedAudioList');
      const countEl = document.getElementById('savedAudioCount');
      if (!section || !listEl) return;

      if (savedTracks.length === 0) {
        section.classList.add('hidden');
        return;
      }

      section.classList.remove('hidden');
      if (countEl) countEl.textContent = savedTracks.length + ' bài';

      listEl.innerHTML = savedTracks.map(tr => {
        const isCurrent = tr.id === activeSavedTrackId;
        return `
          <div class="flex items-center justify-between gap-1.5 px-2 py-1.5 rounded-lg border text-[11px] transition ${
            isCurrent
              ? 'bg-studio-card border-studio-pink text-white'
              : 'bg-studio-panel border-studio-border text-slate-300 hover:bg-studio-card'
          }">
            <button type="button" onclick="selectSavedAudioTrack('${tr.id}')" class="flex-1 text-left truncate flex items-center gap-1.5" title="${tr.name}">
              <span>${isCurrent ? '🔊' : '🎵'}</span>
              <span class="truncate">${tr.name}</span>
            </button>
            <button type="button" onclick="removeSavedAudioTrack('${tr.id}')" class="text-slate-500 hover:text-rose-400 px-1 shrink-0" title="Xóa bài này">
              ✕
            </button>
          </div>
        `;
      }).join('');
    }

    // Chọn phát 1 bài trong danh sách đã lưu
    function selectSavedAudioTrack(id) {
      const track = savedTracks.find(t => t.id === id);
      if (!track) return;
      activeSavedTrackId = id;
      document.getElementById('inputMusicUrl').value = '';
      clearInterval(musicLoopTimer);
      if (customAudioEl) customAudioEl.pause();
      customAudioEl = new Audio(track.url);
      customAudioEl.loop = true;

      if (!isPlayingMusic) {
        toggleMusicPlay();
      } else {
        customAudioEl.play();
      }
      renderSavedAudioList();
      showToast('Đang phát nhạc đã lưu: ' + track.name);
    }

    // Xóa bài khỏi danh sách & giải phóng bộ nhớ
    async function removeSavedAudioTrack(id) {
      const idx = savedTracks.findIndex(t => t.id === id);
      if (idx === -1) return;
      const [removed] = savedTracks.splice(idx, 1);
      URL.revokeObjectURL(removed.url);
      await deleteAudioTrackFromDB(id);

      if (activeSavedTrackId === id) {
        activeSavedTrackId = null;
        if (customAudioEl) {
          customAudioEl.pause();
          customAudioEl = null;
        }
        if (isPlayingMusic) toggleMusicPlay();
      }
      renderSavedAudioList();
      showToast('Đã xóa "' + removed.name + '"');
    }
    // --- HẾT PHẦN THÊM MỚI ---

    const MELODIES = {
      musicbox:  [
    659.25,622.25,659.25,622.25,659.25,493.88,587.33,523.25,
    440,261.63,329.63,440,493.88,329.63,415.30,493.88,523.25,

    659.25,622.25,659.25,622.25,659.25,493.88,587.33,523.25,
    440,261.63,329.63,440,493.88,329.63,523.25,493.88,440,

    493.88,523.25,587.33,659.25,698.46,783.99,880,
    880,932.33,880,783.99,698.46,659.25,587.33,

    659.25,622.25,659.25,622.25,659.25,493.88,587.33,523.25,
    440,261.63,329.63,440,493.88,329.63,415.30,493.88,523.25
  ],
      lullaby:  [392.00, 493.88, 587.33, 493.88, 392.00, 440.00, 523.25, 659.25, 587.33],
      birthday: [392, 392, 440, 392, 523, 494,
  392, 392, 440, 392, 587, 523,
  392, 392, 784, 659, 523, 494, 440,
  698, 698, 659, 523, 587, 523]
    };

    function playNote(freq) {
      if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
      if (audioCtx.state === 'suspended') audioCtx.resume();
      const osc = audioCtx.createOscillator();
      const gain = audioCtx.createGain();
      osc.type = 'sine';
      osc.frequency.setValueAtTime(freq, audioCtx.currentTime);
      gain.gain.setValueAtTime(0.12, audioCtx.currentTime);
      gain.gain.exponentialRampToValueAtTime(0.0001, audioCtx.currentTime + 0.75);
      osc.connect(gain);
      gain.connect(audioCtx.destination);
      osc.start();
      osc.stop(audioCtx.currentTime + 0.76);
    }

    function toggleMusicPlay() {
      isPlayingMusic = !isPlayingMusic;
      const btnPanel = document.getElementById('panelMusicBtn');
      const btnStage = document.getElementById('stageMusicLabel');

      if (isPlayingMusic) {
        btnPanel.textContent = '❚❚ Dừng nhạc';
        btnStage.textContent = 'Đang phát nhạc';

        const urlInput = document.getElementById('inputMusicUrl').value.trim();
        if (customAudioEl) {
          customAudioEl.play();
        } else if (urlInput) {
          customAudioEl = new Audio(urlInput);
          customAudioEl.loop = true;
          customAudioEl.play().catch(() => showToast('Không phát được link nhạc này, chuyển sang hộp nhạc mẫu'));
        } else {
          const track = document.getElementById('selectPresetMusic').value;
          const notes = MELODIES[track] || MELODIES.musicbox;
          let step = 0;
          playNote(notes[0]);
          musicLoopTimer = setInterval(() => {
            step = (step + 1) % notes.length;
            playNote(notes[step]);
          }, 420);
        }
      } else {
        btnPanel.textContent = '▶ Nghe thử';
        btnStage.textContent = 'Phát nhạc';
        clearInterval(musicLoopTimer);
        if (customAudioEl) customAudioEl.pause();
      }
    }

    function changePresetTrack() {
      activeSavedTrackId = null;     // Bỏ chọn bài đã lưu khi quay về nhạc hộp mẫu
      customAudioEl = null;
      renderSavedAudioList();        // Cập nhật lại viền active trên danh sách
      if (isPlayingMusic) {
        toggleMusicPlay();
        toggleMusicPlay();
      }
    }

    async function handleLocalAudioUpload(e) {
      const file = e.target.files[0];
      if (!file) return;
      const id = 'trk_' + Date.now();
      const objUrl = URL.createObjectURL(file);

      // 1. Lưu tệp vào IndexedDB
      await saveAudioTrackToDB({
        id,
        name: file.name,
        blob: file,
        createdAt: Date.now()
      });

      // 2. Thêm lên đầu danh sách và cập nhật giao diện
      savedTracks.unshift({ id, name: file.name, blob: file, url: objUrl });
      activeSavedTrackId = id;
      renderSavedAudioList();

      // 3. Phát ngay bài vừa tải lên
      clearInterval(musicLoopTimer);
      if (customAudioEl) customAudioEl.pause();
      customAudioEl = new Audio(objUrl);
      customAudioEl.loop = true;
      if (!isPlayingMusic) toggleMusicPlay();
      else customAudioEl.play();

      e.target.value = ''; // Reset input để có thể chọn lại cùng 1 file nếu muốn
      showToast('Đã lưu & phát tệp nhạc: ' + file.name);
    }

    // =========================================================================
    // 10. TẠO LINK MÃ HÓA URL & VẼ MÃ QR THẬT
    // =========================================================================
    function buildEncodedShareUrl() {
      const heartInp = document.getElementById('inputHeartWords');
      const payload = {
        m: currentModelId,
        to: document.getElementById('inputRecipient').value.trim(),
        r: document.getElementById('inputMorphWord').value.trim(),
        hw: heartInp ? heartInp.value.trim() : '',
        msg: document.getElementById('inputLongMessage').value.trim(),
        c: themeKey,
        spd: rotSpeed,
        trk: document.getElementById('selectPresetMusic').value
      };
      const encoded = btoa(unescape(encodeURIComponent(JSON.stringify(payload))));
      const baseUrl = window.location.href.split('#')[0];
      return `${baseUrl}#card=${encoded}`;
    }

    function openShareModal() {
      const url = buildEncodedShareUrl();
      document.getElementById('encodedShareLink').value = url;

      const qrBox = document.getElementById('qrcodeBox');
      qrBox.innerHTML = '';
      new QRCode(qrBox, {
        text: url,
        width: 180,
        height: 180,
        colorDark: '#09090d',
        colorLight: '#ffffff',
        correctLevel: QRCode.CorrectLevel.M
      });

      document.getElementById('shareModal').classList.remove('hidden');
    }

    function closeShareModal() {
      document.getElementById('shareModal').classList.add('hidden');
    }

    function copyShareLink() {
      const inp = document.getElementById('encodedShareLink');
      inp.select();
      document.execCommand('copy');
      showToast('Đã sao chép đường link thiệp!');
    }

    function downloadQrPng() {
      const canvas = document.querySelector('#qrcodeBox canvas');
      if (!canvas) return;
      const link = document.createElement('a');
      link.download = 'thiep-ascii-qr.png';
      link.href = canvas.toDataURL('image/png');
      link.click();
      showToast('Đã tải ảnh Mã QR về máy!');
    }

    // Tự động khôi phục thiệp nếu mở từ URL có chứa #card=...
    function loadFromUrlHashIfPresent() {
      const hash = window.location.hash;
      if (!hash.startsWith('#card=')) return;
      try {
        const encoded = hash.replace('#card=', '');
        const data = JSON.parse(decodeURIComponent(escape(atob(encoded))));
        if (data.m) quickSelectCardModel(data.m);
        if (data.to) {
          document.getElementById('inputRecipient').value = data.to;
          updateCardTexts();
        }
        if (data.r) {
          document.getElementById('inputMorphWord').value = data.r;
          updateModelCaptionText(data.r);
        }
        if (data.hw && document.getElementById('inputHeartWords')) {
          document.getElementById('inputHeartWords').value = data.hw;
          updateHeartInnerWords(data.hw);
        }
        if (data.msg) {
          document.getElementById('inputLongMessage').value = data.msg;
        }
        if (typeof data.spd === 'number') {
          updateModelSpeed(data.spd);
        }
        if (data.c) {
          const palette = {
            pink: ['#ff3b6b', 'rgba(255,59,107,0.45)'],
            green: ['#00ff88', 'rgba(0,255,136,0.4)'],
            blue: ['#38bdf8', 'rgba(56,189,248,0.45)'],
            amber: ['#fbbf24', 'rgba(251,191,36,0.45)']
          };
          if (palette[data.c]) setThemeColor(data.c, palette[data.c][0], palette[data.c][1]);
        }
        toggleRecipientPreview(true);
      } catch (err) {
        console.warn('Không thể giải mã tham số thiệp từ URL:', err);
      }
    }

    // =========================================================================
    // 11. PHÍM TẮT R & THÔNG BÁO TOAST
    // =========================================================================
    window.addEventListener('keydown', (e) => {
      const tag = document.activeElement.tagName;
      if (tag === 'INPUT' || tag === 'TEXTAREA') return;

      if (e.key === 'r' || e.key === 'R') {
        triggerModelReplay();
      }
    });

    function showToast(msg) {
      const toast = document.getElementById('toast');
      toast.textContent = msg;
      toast.classList.remove('opacity-0');
      toast.classList.add('opacity-100');
      clearTimeout(window._toastTimer);
      window._toastTimer = setTimeout(() => {
        toast.classList.remove('opacity-100');
        toast.classList.add('opacity-0');
      }, 2400);
    }

    // Khởi chạy ban đầu với mẫu Bó Hoa 3D
    const initialMsgEl = document.getElementById('inputLongMessage');
    if (initialMsgEl) startTypewriter(initialMsgEl.value);
    loadSavedAudioTracks(); // <--- THÊM DÒNG NÀY ĐỂ NẠP NHẠC ĐÃ LƯU
    quickSelectCardModel('bouquet');
    loadFromUrlHashIfPresent();

    window.addEventListener('pointerdown', () => {
      if (audioCtx && audioCtx.state === 'suspended') {
        audioCtx.resume();
      }
      if (pendingAutoMusic && !isPlayingMusic) {
        pendingAutoMusic = false;
        toggleMusicPlay();
      }
    }, { once: true });
  </script>
</body>
</html>
