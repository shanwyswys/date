[code_artifact (3).html](https://github.com/user-attachments/files/33538638/code_artifact.3.html)
# date<!DOCTYPE html>
<html lang="zh-HK">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>小學一年級・雙月曆快樂學 (雙月曆互動小遊戲)</title>
  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Google Fonts: 圓潤易讀字型 -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Kosugi+Maru&family=M+PLUS+Rounded+1c:wght@500;700;800;900&family=Noto+Sans+TC:wght@500;700;900&display=swap" rel="stylesheet">
  
  <style>
    body {
      font-family: 'M PLUS Rounded 1c', 'Kosugi Maru', 'Noto Sans TC', 'Microsoft JhengHei', '微軟正黑體', sans-serif;
      touch-action: manipulation;
      user-select: none;
      -webkit-user-select: none;
      -webkit-tap-highlight-color: transparent;
    }

    /* 關鍵字微凸顯標籤樣式 */
    .kw-tag {
      display: inline-block;
      padding: 2px 10px;
      margin: 0 4px;
      border-radius: 9999px;
      font-weight: 800;
      box-shadow: 0 2px 4px rgba(0,0,0,0.06);
    }
    .kw-month {
      background-color: #FEF08A; /* 黃色高亮 */
      color: #854D0E;
      border: 2px solid #FDE047;
    }
    .kw-day {
      background-color: #BAE6FD; /* 天藍高亮 */
      color: #075985;
      border: 2px solid #7DD3FC;
    }

    /* 日期按鈕微動效與無障礙尺寸保證 (至少 60-66px) */
    .cal-cell-btn {
      min-width: 58px;
      min-height: 58px;
      transition: all 0.15s cubic-bezier(0.34, 1.56, 0.64, 1);
    }
    @media (min-width: 1024px) {
      .cal-cell-btn {
        min-width: 66px;
        min-height: 66px;
      }
    }

    /* 答對柔和光環 */
    .correct-pulse {
      animation: correctPulse 1.2s infinite alternate ease-in-out;
      background-color: #86EFAC !important;
      border-color: #16A34A !important;
      color: #14532D !important;
      transform: scale(1.08);
      z-index: 10;
      box-shadow: 0 0 20px rgba(34, 197, 94, 0.6) !important;
    }
    @keyframes correctPulse {
      0% { transform: scale(1.06); box-shadow: 0 0 12px rgba(34, 197, 94, 0.4); }
      100% { transform: scale(1.12); box-shadow: 0 0 22px rgba(34, 197, 94, 0.7); }
    }

    /* 柔和輕晃（防挫折，無劇烈震動與刺眼閃爍） */
    .soft-shake {
      animation: gentleShake 0.45s ease-in-out;
    }
    @keyframes gentleShake {
      0%, 100% { transform: translateX(0); }
      20% { transform: translateX(-6px); }
      40% { transform: translateX(6px); }
      60% { transform: translateX(-4px); }
      80% { transform: translateX(4px); }
    }

    /* 卡通彈跳小動畫 */
    .mascot-bounce {
      animation: mascotBounce 0.8s ease;
    }
    @keyframes mascotBounce {
      0% { transform: scale(0.3) translateY(30px); opacity: 0; }
      50% { transform: scale(1.1) translateY(-10px); opacity: 1; }
      75% { transform: scale(0.95) translateY(4px); }
      100% { transform: scale(1) translateY(0); }
    }
  </style>
</head>
<body class="bg-gradient-to-b from-sky-100 via-amber-50 to-emerald-50 min-h-screen text-slate-800 flex flex-col justify-between overflow-x-hidden p-3 md:p-5 select-none">

  <!-- =========================== 遊戲畫面主容器 (預設先隱藏，選好月份後顯示) =========================== -->
  <div id="gameScreen" class="w-full flex-grow flex flex-col justify-between hidden">
    <!-- 頂部區：題目條、操作按鈕與進度 -->
    <header class="w-full max-w-7xl mx-auto mb-3">
      <!-- 頂端控制列 -->
      <div class="flex items-center justify-between mb-2 px-2 flex-wrap gap-2">
        <!-- 應用標題與更換月份按鈕 -->
        <div class="flex items-center gap-2 md:gap-3">
          <div class="bg-white/90 shadow-sm border-2 border-amber-300 rounded-2xl px-3 py-1.5 flex items-center gap-2">
            <span class="text-xl md:text-2xl">🗓️</span>
            <span class="font-black text-amber-900 text-base md:text-xl tracking-wide">2026年 雙月曆歷險記</span>
          </div>
          <!-- 更換月份按鈕 -->
          <button id="changeMonthsBtn" class="flex items-center gap-1 bg-amber-400 hover:bg-amber-500 active:scale-95 text-amber-950 font-black text-xs md:text-sm px-3.5 py-1.5 rounded-2xl border-2 border-amber-500 shadow-sm transition">
            <span>⚙️</span>
            <span>更換月份</span>
          </button>
        </div>

        <!-- 右側：語音開關與星星成就進度 -->
        <div class="flex items-center gap-2 md:gap-3">
          <!-- 自動讀題開關 -->
          <button id="toggleAutoSpeechBtn" class="flex items-center gap-1.5 bg-white/90 hover:bg-slate-100 border-2 border-slate-300 text-slate-700 px-3 py-1.5 rounded-2xl font-bold text-xs md:text-sm shadow-sm transition active:scale-95" title="切換進入新題目時是否自動朗讀">
            <span id="autoSpeechIcon">🔊</span>
            <span id="autoSpeechLabel">自動讀題: 開</span>
          </button>

          <!-- 星星成就進度 -->
          <div class="bg-white/90 border-2 border-amber-200 rounded-2xl px-3 py-1.5 flex items-center gap-1 shadow-sm">
            <span class="text-amber-500 font-black text-sm md:text-base">⭐</span>
            <span id="starCounter" class="font-extrabold text-amber-800 text-sm md:text-base">0 / 8</span>
          </div>
        </div>
      </div>

      <!-- 核心題目大看板 -->
      <div class="bg-white border-4 border-amber-300 rounded-3xl p-3 md:p-4 shadow-md flex items-center gap-3 md:gap-5 transition-all">
        <!-- 超大發聲按鈕 -->
        <button id="speakQuestionBtn" class="flex-shrink-0 w-16 h-16 md:w-20 md:h-20 bg-gradient-to-tr from-amber-400 to-yellow-300 hover:from-amber-500 hover:to-yellow-400 active:scale-90 rounded-2xl shadow-md border-3 border-amber-500 flex flex-col items-center justify-center gap-0.5 text-white transition-transform focus:outline-none" aria-label="點擊播放廣東話朗讀題目">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-8 w-8 md:h-10 md:w-10 fill-amber-900" viewBox="0 0 24 24">
            <path d="M14 3.23v2.06c2.89.86 5 3.54 5 6.71s-2.11 5.85-5 6.71v2.06c4.01-.91 7-4.49 7-8.77s-2.99-7.86-7-8.77zm-2.5-1.23l-5 5H2v10h4.5l5 5V2z"/>
          </svg>
          <span class="text-[11px] md:text-xs font-black text-amber-950">再聽一次</span>
        </button>

        <!-- 題目內容文字框 -->
        <div class="flex-grow">
          <div class="flex items-center gap-2 mb-1">
            <span class="bg-amber-100 text-amber-800 text-xs font-extrabold px-2.5 py-0.5 rounded-md">
              第 <span id="currentQNum">1</span> 題
            </span>
            <span class="text-xs text-slate-500 font-bold hidden md:inline">點擊喇叭可以重聽語音喔！</span>
          </div>
          <div id="questionTextContainer" class="text-xl md:text-2xl lg:text-3xl font-extrabold text-slate-800 leading-snug tracking-wide">
        <!-- 題目文字由 JS 動態生成 -->
      </div>
    </div>

    <!-- 切換題目按鈕 (上一題 / 下一題) -->
        <div class="flex flex-col gap-1.5 flex-shrink-0">
          <button id="nextQuestionBtn" class="bg-emerald-500 hover:bg-emerald-600 active:scale-95 text-white font-extrabold px-4 py-2.5 rounded-2xl shadow border-2 border-emerald-600 text-sm md:text-base flex items-center justify-center gap-1">
            <span>下一題</span>
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="3" d="M9 5l7 7-7 7" />
            </svg>
          </button>
          <button id="prevQuestionBtn" class="bg-slate-100 hover:bg-slate-200 active:scale-95 text-slate-600 font-bold px-3 py-1.5 rounded-xl border border-slate-300 text-xs flex items-center justify-center gap-1">
            上一題
          </button>
        </div>
      </div>
    </header>

    <!-- 中央核心區：雙月曆並排 -->
    <main class="w-full max-w-7xl mx-auto flex-grow flex items-center justify-center">
      <div class="w-full grid grid-cols-1 md:grid-cols-2 gap-4 md:gap-6 lg:gap-8 items-start">
        
        <!-- ===== 左邊月曆 ===== -->
        <section id="calendarLeftCard" class="bg-white/95 rounded-3xl p-3 md:p-4 shadow-lg border-4 border-amber-200 transition-all">
          <div class="bg-gradient-to-r from-amber-400 to-yellow-400 rounded-2xl p-2.5 mb-3 flex items-center justify-between text-white shadow-inner">
            <div class="flex items-baseline gap-2 pl-2">
              <span class="text-base md:text-lg font-black text-amber-900/80 bg-white/70 px-2 py-0.5 rounded-lg border border-amber-300">2026年</span>
              <h2 id="leftMonthTitle" class="text-2xl md:text-3xl font-black tracking-wider text-amber-950">5月</h2>
            </div>
            <span id="leftMonthTag" class="bg-amber-100/90 text-amber-900 border-2 border-amber-300 font-extrabold text-xs md:text-sm px-3 py-1 rounded-full">
              上個月 (較先到)
            </span>
          </div>

          <!-- 星期表頭 -->
          <div class="grid grid-cols-7 gap-1.5 mb-2 text-center text-sm md:text-base font-black">
            <div class="text-rose-500 py-1 bg-rose-50 rounded-lg">日</div>
            <div class="text-slate-600 py-1 bg-slate-50 rounded-lg">一</div>
            <div class="text-slate-600 py-1 bg-slate-50 rounded-lg">二</div>
            <div class="text-slate-600 py-1 bg-slate-50 rounded-lg">三</div>
            <div class="text-slate-600 py-1 bg-slate-50 rounded-lg">四</div>
            <div class="text-slate-600 py-1 bg-slate-50 rounded-lg">五</div>
            <div class="text-sky-600 py-1 bg-sky-50 rounded-lg">六</div>
          </div>

          <!-- 左月 日期網格 -->
          <div id="gridMonthLeft" class="grid grid-cols-7 gap-1.5 md:gap-2"></div>
        </section>

        <!-- ===== 右邊月曆 ===== -->
        <section id="calendarRightCard" class="bg-white/95 rounded-3xl p-3 md:p-4 shadow-lg border-4 border-sky-200 transition-all">
          <div class="bg-gradient-to-r from-sky-400 to-cyan-400 rounded-2xl p-2.5 mb-3 flex items-center justify-between text-white shadow-inner">
            <div class="flex items-baseline gap-2 pl-2">
              <span class="text-base md:text-lg font-black text-sky-900/80 bg-white/70 px-2 py-0.5 rounded-lg border border-sky-300">2026年</span>
              <h2 id="rightMonthTitle" class="text-2xl md:text-3xl font-black tracking-wider text-sky-950">6月</h2>
            </div>
            <span id="rightMonthTag" class="bg-sky-100/90 text-sky-900 border-2 border-sky-300 font-extrabold text-xs md:text-sm px-3 py-1 rounded-full">
              下個月 (較後到)
            </span>
          </div>

          <!-- 星期表頭 -->
          <div class="grid grid-cols-7 gap-1.5 mb-2 text-center text-sm md:text-base font-black">
            <div class="text-rose-500 py-1 bg-rose-50 rounded-lg">日</div>
            <div class="text-slate-600 py-1 bg-slate-50 rounded-lg">一</div>
            <div class="text-slate-600 py-1 bg-slate-50 rounded-lg">二</div>
            <div class="text-slate-600 py-1 bg-slate-50 rounded-lg">三</div>
            <div class="text-slate-600 py-1 bg-slate-50 rounded-lg">四</div>
            <div class="text-slate-600 py-1 bg-slate-50 rounded-lg">五</div>
            <div class="text-sky-600 py-1 bg-sky-50 rounded-lg">六</div>
          </div>

          <!-- 右月 日期網格 -->
          <div id="gridMonthRight" class="grid grid-cols-7 gap-1.5 md:gap-2"></div>
        </section>
      </div>
    </main>

    <!-- 底部互動狀態條 -->
    <footer class="w-full max-w-7xl mx-auto mt-2">
      <!-- 步驟 2 專用：大型「星期幾」選擇區塊 (預設在步驟1隱藏) -->
      <div id="weekdaySelectorBar" class="hidden mb-2 bg-white/95 border-3 border-amber-400 rounded-3xl p-3 shadow-lg">
        <div class="flex items-center justify-between mb-2 px-1">
          <div class="flex items-center gap-2">
            <span class="bg-amber-400 text-amber-950 font-black text-xs md:text-sm px-3 py-1 rounded-full animate-bounce">
              步驟 2
            </span>
            <span class="text-sm md:text-base font-black text-amber-900">
              👉 請向上對比星期列，選出正確嘅「星期幾」：
            </span>
          </div>
          <span class="text-xs text-slate-500 font-bold hidden sm:inline">可直接點選下方按鈕</span>
        </div>

        <!-- 7 個星期按鈕 (尺寸充足至少 60px 高，觸控友善) -->
        <div class="grid grid-cols-7 gap-2 md:gap-3">
          <button type="button" class="weekday-choice-btn min-h-[58px] md:min-h-[64px] bg-rose-50 hover:bg-rose-100 border-2 border-rose-300 text-rose-700 active:scale-95 rounded-2xl flex flex-col items-center justify-center font-black transition shadow-sm" data-weekday-idx="0">
            <span class="text-xs md:text-sm">星期</span>
            <span class="text-xl md:text-2xl font-black">日</span>
          </button>
          <button type="button" class="weekday-choice-btn min-h-[58px] md:min-h-[64px] bg-sky-50 hover:bg-sky-100 border-2 border-slate-300 text-slate-700 active:scale-95 rounded-2xl flex flex-col items-center justify-center font-black transition shadow-sm" data-weekday-idx="1">
            <span class="text-xs md:text-sm">星期</span>
            <span class="text-xl md:text-2xl font-black">一</span>
          </button>
          <button type="button" class="weekday-choice-btn min-h-[58px] md:min-h-[64px] bg-sky-50 hover:bg-sky-100 border-2 border-slate-300 text-slate-700 active:scale-95 rounded-2xl flex flex-col items-center justify-center font-black transition shadow-sm" data-weekday-idx="2">
            <span class="text-xs md:text-sm">星期</span>
            <span class="text-xl md:text-2xl font-black">二</span>
          </button>
          <button type="button" class="weekday-choice-btn min-h-[58px] md:min-h-[64px] bg-sky-50 hover:bg-sky-100 border-2 border-slate-300 text-slate-700 active:scale-95 rounded-2xl flex flex-col items-center justify-center font-black transition shadow-sm" data-weekday-idx="3">
            <span class="text-xs md:text-sm">星期</span>
            <span class="text-xl md:text-2xl font-black">三</span>
          </button>
          <button type="button" class="weekday-choice-btn min-h-[58px] md:min-h-[64px] bg-sky-50 hover:bg-sky-100 border-2 border-slate-300 text-slate-700 active:scale-95 rounded-2xl flex flex-col items-center justify-center font-black transition shadow-sm" data-weekday-idx="4">
            <span class="text-xs md:text-sm">星期</span>
            <span class="text-xl md:text-2xl font-black">四</span>
          </button>
          <button type="button" class="weekday-choice-btn min-h-[58px] md:min-h-[64px] bg-sky-50 hover:bg-sky-100 border-2 border-slate-300 text-slate-700 active:scale-95 rounded-2xl flex flex-col items-center justify-center font-black transition shadow-sm" data-weekday-idx="5">
            <span class="text-xs md:text-sm">星期</span>
            <span class="text-xl md:text-2xl font-black">五</span>
          </button>
          <button type="button" class="weekday-choice-btn min-h-[58px] md:min-h-[64px] bg-cyan-50 hover:bg-cyan-100 border-2 border-cyan-300 text-cyan-700 active:scale-95 rounded-2xl flex flex-col items-center justify-center font-black transition shadow-sm" data-weekday-idx="6">
            <span class="text-xs md:text-sm">星期</span>
            <span class="text-xl md:text-2xl font-black">六</span>
          </button>
        </div>
      </div>

      <div id="feedbackStatusBar" class="bg-white/80 backdrop-blur-sm border-2 border-slate-200 rounded-2xl p-2.5 px-4 flex items-center justify-between shadow-sm min-h-[52px]">
        <div class="flex items-center gap-3">
          <div id="mascotAvatar" class="w-10 h-10 bg-amber-200 border-2 border-amber-400 rounded-full flex items-center justify-center text-2xl shadow-sm">
            🐻
          </div>
          <span id="feedbackMessage" class="font-extrabold text-slate-700 text-sm md:text-base">
            請仔細看題目，在月曆上輕輕點選正確的日期格子喔！
          </span>
        </div>

        <button id="retryBtn" class="text-slate-500 hover:text-slate-800 font-bold text-xs bg-slate-100 hover:bg-slate-200 px-3 py-1.5 rounded-xl border border-slate-300">
          重新嘗試
        </button>
      </div>
    </footer>
  </div>

  <!-- =========================== 前置設定選擇頁面 (選取兩個月份) =========================== -->
  <div id="setupScreen" class="fixed inset-0 bg-gradient-to-br from-amber-100 via-sky-50 to-emerald-100 z-50 flex items-center justify-center p-4 overflow-y-auto">
    <div class="bg-white/95 backdrop-blur border-4 border-amber-300 rounded-3xl p-5 md:p-8 max-w-2xl w-full shadow-2xl">
      <!-- 標題與簡介 -->
      <div class="text-center mb-6">
        <div class="inline-flex items-center justify-center gap-2 bg-amber-100 border-2 border-amber-300 px-4 py-1.5 rounded-full text-amber-900 font-black text-sm mb-3">
          <span>🗓️ 2026年 雙月曆學習</span>
        </div>
        <h1 class="text-2xl md:text-3xl font-black text-slate-800 mb-2">請選取想練習的兩個月份</h1>
        <p class="text-slate-600 font-bold text-sm md:text-base">
          適合教師或學童自行設定比對月份，系統會即時產生 2026 年真實日曆！
        </p>
      </div>

      <!-- 快速推薦組合快捷鍵 -->
      <div class="mb-6">
        <div class="text-xs md:text-sm font-extrabold text-slate-500 mb-2">⚡ 快速選取常用雙月組合：</div>
        <div class="grid grid-cols-3 sm:grid-cols-6 gap-2">
          <button type="button" class="preset-pair-btn bg-slate-50 hover:bg-amber-100 active:scale-95 border-2 border-slate-200 text-slate-700 font-black py-2 rounded-xl text-xs md:text-sm transition" data-m1="1" data-m2="2">1月 + 2月</button>
          <button type="button" class="preset-pair-btn bg-slate-50 hover:bg-amber-100 active:scale-95 border-2 border-slate-200 text-slate-700 font-black py-2 rounded-xl text-xs md:text-sm transition" data-m1="3" data-m2="4">3月 + 4月</button>
          <button type="button" class="preset-pair-btn bg-amber-200 border-2 border-amber-400 text-amber-950 font-black py-2 rounded-xl text-xs md:text-sm transition shadow-sm" data-m1="5" data-m2="6">5月 + 6月</button>
          <button type="button" class="preset-pair-btn bg-slate-50 hover:bg-amber-100 active:scale-95 border-2 border-slate-200 text-slate-700 font-black py-2 rounded-xl text-xs md:text-sm transition" data-m1="7" data-m2="8">7月 + 8月</button>
          <button type="button" class="preset-pair-btn bg-slate-50 hover:bg-amber-100 active:scale-95 border-2 border-slate-200 text-slate-700 font-black py-2 rounded-xl text-xs md:text-sm transition" data-m1="9" data-m2="10">9月 + 10月</button>
          <button type="button" class="preset-pair-btn bg-slate-50 hover:bg-amber-100 active:scale-95 border-2 border-slate-200 text-slate-700 font-black py-2 rounded-xl text-xs md:text-sm transition" data-m1="11" data-m2="12">11月 + 12月</button>
        </div>
      </div>

      <!-- 自選月份控制面板 (左右兩個月份) -->
      <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-6">
        <!-- 左邊月份 (較先/前一個月) -->
        <div class="bg-amber-50 border-3 border-amber-300 rounded-2xl p-4 flex flex-col items-center">
          <span class="bg-amber-200 text-amber-900 font-black text-xs px-3 py-1 rounded-full mb-2">左邊月曆 (前一個月份)</span>
          <div class="flex items-center gap-2 w-full justify-center">
            <span class="font-extrabold text-amber-900 text-lg">2026年</span>
            <select id="selectMonth1" class="bg-white border-2 border-amber-400 rounded-xl px-4 py-2 font-black text-xl text-amber-950 outline-none shadow-sm focus:ring-2 focus:ring-amber-300">
              <option value="1">1月</option>
              <option value="2">2月</option>
              <option value="3">3月</option>
              <option value="4">4月</option>
              <option value="5" selected>5月</option>
              <option value="6">6月</option>
              <option value="7">7月</option>
              <option value="8">8月</option>
              <option value="9">9月</option>
              <option value="10">10月</option>
              <option value="11">11月</option>
              <option value="12">12月</option>
            </select>
          </div>
        </div>

        <!-- 右邊月份 (較後/後一個月) -->
        <div class="bg-sky-50 border-3 border-sky-300 rounded-2xl p-4 flex flex-col items-center">
          <span class="bg-sky-200 text-sky-900 font-black text-xs px-3 py-1 rounded-full mb-2">右邊月曆 (後一個月份)</span>
          <div class="flex items-center gap-2 w-full justify-center">
            <span class="font-extrabold text-sky-900 text-lg">2026年</span>
            <select id="selectMonth2" class="bg-white border-2 border-sky-400 rounded-xl px-4 py-2 font-black text-xl text-sky-950 outline-none shadow-sm focus:ring-2 focus:ring-sky-300">
              <option value="1">1月</option>
              <option value="2">2月</option>
              <option value="3">3月</option>
              <option value="4">4月</option>
              <option value="5">5月</option>
              <option value="6" selected>6月</option>
              <option value="7">7月</option>
              <option value="8">8月</option>
              <option value="9">9月</option>
              <option value="10">10月</option>
              <option value="11">11月</option>
              <option value="12">12月</option>
            </select>
          </div>
        </div>
      </div>

      <!-- 錯誤提醒訊息 (例如選到相同月份) -->
      <div id="setupErrorMsg" class="text-rose-600 font-bold text-sm text-center mb-4 hidden">
        ⚠️ 請選取兩個不同的月份進行比對喔！
      </div>

      <!-- 開始遊戲大按鈕 -->
      <button id="startGameBtn" class="w-full bg-gradient-to-r from-emerald-500 to-teal-500 hover:from-emerald-600 hover:to-teal-600 active:scale-95 text-white font-black text-xl py-4 rounded-2xl shadow-lg border-2 border-emerald-600 transition flex items-center justify-center gap-2">
        <span>開始遊戲</span> 🚀
      </button>
    </div>
  </div>

  <!-- =========================== 彈出式鼓勵大彈窗 =========================== -->
  <div id="successModal" class="fixed inset-0 bg-slate-900/40 backdrop-blur-sm hidden items-center justify-center z-50 p-4">
    <div class="bg-white border-4 border-emerald-400 rounded-3xl p-6 md:p-8 max-w-md w-full text-center shadow-2xl transform mascot-bounce flex flex-col items-center">
      <div class="text-6xl md:text-7xl mb-2 animate-bounce">
        👏🐻🎉
      </div>
      <h3 class="text-2xl md:text-3xl font-black text-emerald-700 mb-1">
        答啱喇！真係叻！
      </h3>
      <p id="successDetailText" class="text-base md:text-lg font-bold text-slate-600 mb-5">
        你成功搵出目標日期喇！
      </p>
      
      <div class="flex gap-3 w-full justify-center">
        <button id="modalNextBtn" class="flex-1 bg-emerald-500 hover:bg-emerald-600 text-white font-black text-lg py-3 rounded-2xl shadow-lg border-2 border-emerald-600 active:scale-95 transition">
          繼續下一題 ➔
        </button>
        <button id="modalReplayBtn" class="bg-amber-100 hover:bg-amber-200 text-amber-900 font-black text-sm px-4 py-3 rounded-2xl border border-amber-300">
          留喺度睇睇
        </button>
      </div>
    </div>
  </div>

  <script>
    /* ==========================================================================
       1. 音效引擎 (Web Audio API)
       ========================================================================== */
    class SoundEngine {
      constructor() {
        this.ctx = null;
      }

      init() {
        if (!this.ctx) {
          const AudioContext = window.AudioContext || window.webkitAudioContext;
          this.ctx = new AudioContext();
        }
        if (this.ctx.state === 'suspended') {
          this.ctx.resume();
        }
      }

      // 答對音效：清脆雙和弦 (E5 -> G#5)
      playSuccess() {
        this.init();
        const now = this.ctx.currentTime;
        
        const osc1 = this.ctx.createOscillator();
        const gain1 = this.ctx.createGain();
        osc1.type = 'sine';
        osc1.frequency.setValueAtTime(659.25, now);
        gain1.gain.setValueAtTime(0, now);
        gain1.gain.linearRampToValueAtTime(0.25, now + 0.04);
        gain1.gain.exponentialRampToValueAtTime(0.001, now + 0.35);
        osc1.connect(gain1);
        gain1.connect(this.ctx.destination);
        osc1.start(now);
        osc1.stop(now + 0.36);

        const osc2 = this.ctx.createOscillator();
        const gain2 = this.ctx.createGain();
        osc2.type = 'sine';
        osc2.frequency.setValueAtTime(830.61, now + 0.12);
        gain2.gain.setValueAtTime(0, now + 0.12);
        gain2.gain.linearRampToValueAtTime(0.28, now + 0.16);
        gain2.gain.exponentialRampToValueAtTime(0.0001, now + 0.6);
        osc2.connect(gain2);
        gain2.connect(this.ctx.destination);
        osc2.start(now + 0.12);
        osc2.stop(now + 0.65);
      }

      // 答錯音效：柔和木質咚聲 (不刺耳、無挫折感)
      playRetry() {
        this.init();
        const now = this.ctx.currentTime;
        const osc = this.ctx.createOscillator();
        const gain = this.ctx.createGain();
        
        osc.type = 'triangle';
        osc.frequency.setValueAtTime(260, now);
        osc.frequency.exponentialRampToValueAtTime(180, now + 0.2);

        gain.gain.setValueAtTime(0, now);
        gain.gain.linearRampToValueAtTime(0.2, now + 0.03);
        gain.gain.exponentialRampToValueAtTime(0.001, now + 0.25);

        osc.connect(gain);
        gain.connect(this.ctx.destination);
        osc.start(now);
        osc.stop(now + 0.26);
      }

      // 輕柔點擊聲
      playTap() {
        this.init();
        const now = this.ctx.currentTime;
        const osc = this.ctx.createOscillator();
        const gain = this.ctx.createGain();
        osc.type = 'sine';
        osc.frequency.setValueAtTime(520, now);
        gain.gain.setValueAtTime(0.08, now);
        gain.gain.exponentialRampToValueAtTime(0.001, now + 0.08);
        osc.connect(gain);
        gain.connect(this.ctx.destination);
        osc.start(now);
        osc.stop(now + 0.09);
      }
    }

    const audioFX = new SoundEngine();

    /* ==========================================================================
       2. 廣東話語音朗讀合成器 (SpeechSynthesis)
       ========================================================================== */
    class CantoneseSpeechService {
      constructor() {
        this.synth = window.speechSynthesis;
        this.voice = null;
        this.autoSpeech = true;
        this.initVoices();
      }

      initVoices() {
        if (!this.synth) return;
        const setBestVoice = () => {
          const voices = this.synth.getVoices();
          this.voice = voices.find(v => v.lang.includes('zh-HK') || v.lang.includes('yue')) ||
                       voices.find(v => v.lang.includes('zh-TW')) ||
                       voices.find(v => v.lang.includes('zh'));
        };
        setBestVoice();
        if (speechSynthesis.onvoiceschanged !== undefined) {
          speechSynthesis.onvoiceschanged = setBestVoice;
        }
      }

      speak(text, onEndCallback) {
        if (!this.synth) return;
        this.cancel();

        const utterance = new SpeechSynthesisUtterance(text);
        if (this.voice) {
          utterance.voice = this.voice;
        }
        utterance.lang = this.voice ? this.voice.lang : 'zh-HK';
        utterance.rate = 0.85; // 專為小學與 SEN 調校之溫和 0.85 語速
        utterance.pitch = 1.08;

        if (onEndCallback) {
          utterance.onend = onEndCallback;
        }

        this.synth.speak(utterance);
      }

      cancel() {
        if (this.synth && this.synth.speaking) {
          this.synth.cancel();
        }
      }
    }

    const ttsService = new CantoneseSpeechService();

    /* ==========================================================================
       3. 2026年動態月曆計算核心
       ========================================================================== */
    const WEEKDAY_NAMES = ['星期日', '星期一', '星期二', '星期三', '星期四', '星期五', '星期六'];

    // 計算 2026 年指定月份的總天數與 1 號星期幾
    function getMonthCalendarInfo(year, month) {
      // month: 1 ~ 12
      const firstDay = new Date(year, month - 1, 1);
      const daysInMonth = new Date(year, month, 0).getDate();
      return {
        year: year,
        month: month,
        firstDayWeekday: firstDay.getDay(), // 0:日, 1:一, ... 6:六
        daysInMonth: daysInMonth
      };
    }

    // 計算該年該月某日是星期幾
    function getWeekdayNameFor(year, month, day) {
      const d = new Date(year, month - 1, day);
      return WEEKDAY_NAMES[d.getDay()];
    }

    /* ==========================================================================
       4. 應用狀態管理與動態出題
       ========================================================================== */
    const state = {
      selectedMonth1: 5,
      selectedMonth2: 6,
      questions: [],
      currentQuestionIndex: 0,
      currentStep: 1, // 1: 搵日子, 2: 搵星期幾
      solvedQuestions: new Set(),
      isAnsweringLocked: false
    };

    // 動態產生全體嚴格符合「請找出 2026年 [X]月 [Y]日。」格式的題目庫
    function generateQuestionBank(m1, m2) {
      const questions = [];
      const info1 = getMonthCalendarInfo(2026, m1);
      const info2 = getMonthCalendarInfo(2026, m2);

      const sampleDays1 = [1, Math.min(10, info1.daysInMonth), Math.min(15, info1.daysInMonth), info1.daysInMonth];
      const sampleDays2 = [1, Math.min(8, info2.daysInMonth), Math.min(20, info2.daysInMonth), info2.daysInMonth];

      let id = 1;

      // 加入第一個月份的題目
      sampleDays1.forEach(d => {
        const wk = getWeekdayNameFor(2026, m1, d);
        const wkIndex = new Date(2026, m1 - 1, d).getDay();
        questions.push({
          id: id++,
          year: 2026,
          month: m1,
          day: d,
          weekday: wk,
          weekdayIndex: wkIndex,
          displayHtml: `請找出 <span class="kw-tag kw-month">2026年 ${m1}月 ${d}日</span>。`,
          step2DisplayHtml: `搵到 <span class="kw-tag kw-month">${m1}月 ${d}日</span> 喇！<span class="kw-tag kw-day">咁呢一日係星期幾？</span>`,
          speechStep1: `請找出 2026年 ${m1}月 ${d}日。`,
          speechStep2: `搵到喇！咁 ${m1}月 ${d}日 係星期幾呢？請選出星期幾。`,
          praiseSpeech: `答啱喇！真係好叻！2026年 ${m1}月${d}日 係 ${wk}！`
        });
      });

      // 加入第二個月份的題目
      sampleDays2.forEach(d => {
        const wk = getWeekdayNameFor(2026, m2, d);
        const wkIndex = new Date(2026, m2 - 1, d).getDay();
        questions.push({
          id: id++,
          year: 2026,
          month: m2,
          day: d,
          weekday: wk,
          weekdayIndex: wkIndex,
          displayHtml: `請找出 <span class="kw-tag kw-month">2026年 ${m2}月 ${d}日</span>。`,
          step2DisplayHtml: `搵到 <span class="kw-tag kw-month">${m2}月 ${d}日</span> 喇！<span class="kw-tag kw-day">咁呢一日係星期幾？</span>`,
          speechStep1: `請找出 2026年 ${m2}月 ${d}日。`,
          speechStep2: `搵到喇！咁 ${m2}月 ${d}日 係星期幾呢？請選出星期幾。`,
          praiseSpeech: `答啱喇！真係好叻！2026年 ${m2}月${d}日 係 ${wk}！`
        });
      });

      const firstQ = questions.shift();
      questions.sort(() => Math.random() - 0.5);
      questions.unshift(firstQ);

      return questions;
    }

    // DOM 元素引用
    const elSetupScreen = document.getElementById('setupScreen');
    const elGameScreen = document.getElementById('gameScreen');
    const elSelectMonth1 = document.getElementById('selectMonth1');
    const elSelectMonth2 = document.getElementById('selectMonth2');
    const elSetupErrorMsg = document.getElementById('setupErrorMsg');
    const elStartGameBtn = document.getElementById('startGameBtn');
    const elChangeMonthsBtn = document.getElementById('changeMonthsBtn');

    // 遊戲畫面 DOM
    const elLeftMonthTitle = document.getElementById('leftMonthTitle');
    const elRightMonthTitle = document.getElementById('rightMonthTitle');
    const elLeftMonthTag = document.getElementById('leftMonthTag');
    const elRightMonthTag = document.getElementById('rightMonthTag');
    const elGridLeft = document.getElementById('gridMonthLeft');
    const elGridRight = document.getElementById('gridMonthRight');
    const elQuestionText = document.getElementById('questionTextContainer');
    const elQNum = document.getElementById('currentQNum');
    const elFeedbackMsg = document.getElementById('feedbackMessage');
    const elMascot = document.getElementById('mascotAvatar');
    const elStarCount = document.getElementById('starCounter');
    const elAutoSpeechBtn = document.getElementById('toggleAutoSpeechBtn');
    const elAutoSpeechIcon = document.getElementById('autoSpeechIcon');
    const elAutoSpeechLabel = document.getElementById('autoSpeechLabel');
    const elSpeakBtn = document.getElementById('speakQuestionBtn');
    const elNextBtn = document.getElementById('nextQuestionBtn');
    const elPrevBtn = document.getElementById('prevQuestionBtn');
    const elRetryBtn = document.getElementById('retryBtn');
    const elWeekdaySelectorBar = document.getElementById('weekdaySelectorBar');

    // 彈窗
    const elSuccessModal = document.getElementById('successModal');
    const elModalNextBtn = document.getElementById('modalNextBtn');
    const elModalReplayBtn = document.getElementById('modalReplayBtn');

    /* ==========================================================================
       5. 渲染月曆網格
       ========================================================================== */
    function renderCalendarGrid(containerEl, monthNum) {
      containerEl.innerHTML = '';
      const info = getMonthCalendarInfo(2026, monthNum);

      // 補齊前置空格
      for (let i = 0; i < info.firstDayWeekday; i++) {
        const blank = document.createElement('div');
        blank.className = 'w-full h-full min-h-[58px] md:min-h-[64px] rounded-2xl bg-slate-50/50 border border-transparent';
        containerEl.appendChild(blank);
      }

      // 建立日期按鈕
      for (let day = 1; day <= info.daysInMonth; day++) {
        const weekdayIndex = (info.firstDayWeekday + (day - 1)) % 7;
        const weekdayName = WEEKDAY_NAMES[weekdayIndex];
        const isWeekend = (weekdayIndex === 0 || weekdayIndex === 6);

        const btn = document.createElement('button');
        btn.type = 'button';
        btn.dataset.month = monthNum;
        btn.dataset.day = day;
        btn.id = `btn-${monthNum}-${day}`;

        btn.className = `cal-cell-btn flex flex-col items-center justify-center rounded-2xl md:rounded-3xl border-2 font-black shadow-sm transition-transform active:scale-90 ${
          isWeekend 
            ? (weekdayIndex === 0 ? 'bg-rose-50/90 border-rose-200 text-rose-700 hover:bg-rose-100' : 'bg-sky-50/90 border-sky-200 text-sky-700 hover:bg-sky-100')
            : 'bg-white border-slate-200 text-slate-800 hover:bg-amber-50 hover:border-amber-300'
        }`;

        btn.innerHTML = `<span class="text-xl md:text-2xl font-black leading-none">${day}</span>`;
        btn.addEventListener('click', () => handleDayClick(monthNum, day, weekdayName, btn));

        containerEl.appendChild(btn);
      }
    }

    // 載入當前題目
    function loadCurrentQuestion() {
      if (state.questions.length === 0) return;

      state.isAnsweringLocked = false;
      state.currentStep = 1; // 回歸步驟 1: 搵日子
      const q = state.questions[state.currentQuestionIndex];

      // 重設所有格子的答對與震動狀態
      document.querySelectorAll('.cal-cell-btn').forEach(b => {
        b.classList.remove('correct-pulse', 'soft-shake');
      });

      // 隱藏星期選擇列
      elWeekdaySelectorBar.classList.add('hidden');

      // 更新題目卡片
      elQNum.textContent = state.currentQuestionIndex + 1;
      elQuestionText.innerHTML = q.displayHtml;

      // 更新小助手指示
      elMascot.textContent = '🐻';
      elFeedbackMsg.textContent = '步驟 1：睇清楚題目，喺月曆上輕輕點選正確嘅日期啦！';
      elFeedbackMsg.className = 'font-extrabold text-slate-700 text-sm md:text-base';

      // 自動讀題 (步驟1語音)
      if (ttsService.autoSpeech) {
        setTimeout(() => {
          readQuestionAloud();
        }, 200);
      }
    }

    // 朗讀當前題目 (根據步驟自動判斷語音)
    function readQuestionAloud() {
      const q = state.questions[state.currentQuestionIndex];
      if (!q) return;

      elSpeakBtn.classList.add('scale-105');
      setTimeout(() => elSpeakBtn.classList.remove('scale-105'), 350);

      const speechTxt = (state.currentStep === 1) ? q.speechStep1 : q.speechStep2;

      elFeedbackMsg.textContent = '正為你朗讀題目……請留心聽喔！👂';
      ttsService.speak(speechTxt, () => {
        if (state.currentStep === 1) {
          elFeedbackMsg.textContent = '聽完喇！試下喺月曆上面點選日子啦！';
        } else {
          elFeedbackMsg.textContent = '聽完喇！睇下上面嘅星期列，再點選下方對應嘅星期幾啦！';
        }
      });
    }

    // 步驟 1：點擊日期格子判定
    function handleDayClick(month, day, weekday, buttonEl) {
      if (state.isAnsweringLocked) return;

      const currentQ = state.questions[state.currentQuestionIndex];
      if (!currentQ) return;

      // 若已經處於步驟2，點擊日曆格子僅作提示
      if (state.currentStep === 2) {
        audioFX.playTap();
        ttsService.speak(`你已經選咗 ${currentQ.month}月 ${currentQ.day}日 喇！依家請喺下方選出星期幾啦！`);
        return;
      }

      const isCorrect = (currentQ.month === month && currentQ.day === day);

      if (isCorrect) {
        // 步驟 1 答對！保持格子高亮，切換至步驟 2 (搵星期幾)
        audioFX.playSuccess();
        buttonEl.classList.add('correct-pulse');

        state.currentStep = 2; // 進入步驟 2

        // 展開星期選擇面板
        elWeekdaySelectorBar.classList.remove('hidden');

        // 更新題目卡片文字
        elQuestionText.innerHTML = currentQ.step2DisplayHtml;

        elMascot.textContent = '👀';
        elFeedbackMsg.textContent = `搵到 ${month}月${day}日 喇！睇下佢頂部對應咩星期？請點選下方按鈕！`;
        elFeedbackMsg.className = 'font-black text-amber-700 text-sm md:text-base';

        // 廣東話朗讀步驟 2
        ttsService.speak(currentQ.speechStep2);
      } else {
        // 步驟 1 答錯（無壓力重試）
        audioFX.playRetry();
        buttonEl.classList.add('soft-shake');
        setTimeout(() => buttonEl.classList.remove('soft-shake'), 450);

        elMascot.textContent = '💪';
        let hint = '唔緊要，再試多次啦！';
        if (month !== currentQ.month) {
          hint = `留意下月份喔！今題係要搵【2026年 ${currentQ.month}月】啊。再試一次啦！`;
        } else {
          hint = `差少少喇！搵下 ${currentQ.month}月 嘅 ${currentQ.day}號 喺邊度？再試一次啦！`;
        }
        elFeedbackMsg.textContent = hint;
        elFeedbackMsg.className = 'font-bold text-amber-700 text-sm md:text-base';

        ttsService.speak('唔緊要，再試多次啦！');
      }
    }

    // 步驟 2：點選「星期幾」按鈕判定
    function handleWeekdaySelect(selectedWeekdayIdx, btnEl) {
      if (state.currentStep !== 2 || state.isAnsweringLocked) return;

      const currentQ = state.questions[state.currentQuestionIndex];
      if (!currentQ) return;

      const isCorrect = (selectedWeekdayIdx === currentQ.weekdayIndex);

      if (isCorrect) {
        // 答對星期幾！兩步全部完成！
        state.isAnsweringLocked = true;
        audioFX.playSuccess();

        btnEl.classList.add('correct-pulse');

        state.solvedQuestions.add(currentQ.id);
        updateProgressStars();

        elMascot.textContent = '🎉';
        elFeedbackMsg.textContent = `答啱喇！真係好叻！2026年 ${currentQ.month}月${currentQ.day}日 係 ${currentQ.weekday}！`;
        elFeedbackMsg.className = 'font-black text-emerald-600 text-base md:text-lg';

        ttsService.speak(currentQ.praiseSpeech, () => {
          setTimeout(() => {
            showSuccessModal(currentQ);
          }, 350);
        });
      } else {
        // 步驟 2 答錯星期幾（無壓力重試）
        audioFX.playRetry();
        btnEl.classList.add('soft-shake');
        setTimeout(() => btnEl.classList.remove('soft-shake'), 450);

        const chosenName = WEEKDAY_NAMES[selectedWeekdayIdx];
        elMascot.textContent = '💡';
        elFeedbackMsg.textContent = `唔係${chosenName}喔！試下順住隻手指向上望，睇下嗰格上面係咩字？`;
        elFeedbackMsg.className = 'font-bold text-rose-700 text-sm md:text-base';

        ttsService.speak(`唔係${chosenName}喔，向上望下星期列，再試一次啦！`);
      }
    }

    // 綁定 7 個星期按鈕事件
    document.querySelectorAll('.weekday-choice-btn').forEach(btn => {
      btn.addEventListener('click', () => {
        const idx = parseInt(btn.dataset.weekdayIdx, 10);
        handleWeekdaySelect(idx, btn);
      });
    });

    function updateProgressStars() {
      const total = state.questions.length;
      const count = state.solvedQuestions.size;
      elStarCount.textContent = `${count} / ${total}`;
    }

    function showSuccessModal(question) {
      document.getElementById('successDetailText').textContent = 
        `你成功搵出咗「2026年 ${question.month}月 ${question.day}日」，嗰日係${question.weekday}嚟㗎！`;
      elSuccessModal.classList.remove('hidden');
      elSuccessModal.classList.add('flex');
    }

    function closeSuccessModal() {
      elSuccessModal.classList.add('hidden');
      elSuccessModal.classList.remove('flex');
    }

    function goToNextQuestion() {
      closeSuccessModal();
      if (state.currentQuestionIndex < state.questions.length - 1) {
        state.currentQuestionIndex++;
      } else {
        state.currentQuestionIndex = 0;
      }
      loadCurrentQuestion();
    }

    function goToPrevQuestion() {
      closeSuccessModal();
      if (state.currentQuestionIndex > 0) {
        state.currentQuestionIndex--;
      } else {
        state.currentQuestionIndex = state.questions.length - 1;
      }
      loadCurrentQuestion();
    }

    /* ==========================================================================
       6. 設定頁面與「開始遊戲」動態初始化
       ========================================================================== */
    function startNewGameWithMonths(m1, m2) {
      state.selectedMonth1 = m1;
      state.selectedMonth2 = m2;
      state.solvedQuestions.clear();
      state.currentQuestionIndex = 0;

      // 產生對應題目庫
      state.questions = generateQuestionBank(m1, m2);

      // 更新卡片標籤
      elLeftMonthTitle.textContent = `${m1}月`;
      elRightMonthTitle.textContent = `${m2}月`;
      
      // 更新先後標籤
      if (m1 < m2) {
        elLeftMonthTag.textContent = '上個月 (較先到)';
        elRightMonthTag.textContent = '下個月 (較後到)';
      } else {
        elLeftMonthTag.textContent = '前一個展示月份';
        elRightMonthTag.textContent = '後一個展示月份';
      }

      // 渲染動態月曆
      renderCalendarGrid(elGridLeft, m1);
      renderCalendarGrid(elGridRight, m2);

      // 切換視圖
      elSetupScreen.classList.add('hidden');
      elGameScreen.classList.remove('hidden');

      updateProgressStars();
      loadCurrentQuestion();
    }

    // 點擊「開始遊戲」
    elStartGameBtn.addEventListener('click', () => {
      audioFX.init();
      audioFX.playSuccess();
      ttsService.initVoices();

      const m1 = parseInt(elSelectMonth1.value, 10);
      const m2 = parseInt(elSelectMonth2.value, 10);

      if (m1 === m2) {
        elSetupErrorMsg.classList.remove('hidden');
        return;
      }
      elSetupErrorMsg.classList.add('hidden');
      startNewGameWithMonths(m1, m2);
    });

    // 點擊快速推薦組合按鈕
    document.querySelectorAll('.preset-pair-btn').forEach(btn => {
      btn.addEventListener('click', () => {
        audioFX.playTap();
        document.querySelectorAll('.preset-pair-btn').forEach(b => {
          b.className = 'preset-pair-btn bg-slate-50 hover:bg-amber-100 active:scale-95 border-2 border-slate-200 text-slate-700 font-black py-2 rounded-xl text-xs md:text-sm transition';
        });
        btn.className = 'preset-pair-btn bg-amber-200 border-2 border-amber-400 text-amber-950 font-black py-2 rounded-xl text-xs md:text-sm transition shadow-sm';

        const m1 = btn.dataset.m1;
        const m2 = btn.dataset.m2;
        elSelectMonth1.value = m1;
        elSelectMonth2.value = m2;
        elSetupErrorMsg.classList.add('hidden');
      });
    });

    // 點擊「更換月份」返回設定畫面
    elChangeMonthsBtn.addEventListener('click', () => {
      audioFX.playTap();
      ttsService.cancel();
      closeSuccessModal();
      elGameScreen.classList.add('hidden');
      elSetupScreen.classList.remove('hidden');
    });

    /* ==========================================================================
       7. 操作按鈕綁定
       ========================================================================== */
    elSpeakBtn.addEventListener('click', () => {
      audioFX.playTap();
      readQuestionAloud();
    });

    elAutoSpeechBtn.addEventListener('click', () => {
      audioFX.playTap();
      ttsService.autoSpeech = !ttsService.autoSpeech;
      if (ttsService.autoSpeech) {
        elAutoSpeechIcon.textContent = '🔊';
        elAutoSpeechLabel.textContent = '自動讀題: 開';
        elAutoSpeechBtn.classList.remove('opacity-70');
      } else {
        elAutoSpeechIcon.textContent = '🔇';
        elAutoSpeechLabel.textContent = '自動讀題: 關';
        elAutoSpeechBtn.classList.add('opacity-70');
      }
    });

    elNextBtn.addEventListener('click', () => {
      audioFX.playTap();
      goToNextQuestion();
    });
    elPrevBtn.addEventListener('click', () => {
      audioFX.playTap();
      goToPrevQuestion();
    });
    elRetryBtn.addEventListener('click', () => {
      audioFX.playTap();
      loadCurrentQuestion();
    });

    elModalNextBtn.addEventListener('click', () => {
      audioFX.playTap();
      goToNextQuestion();
    });
    elModalReplayBtn.addEventListener('click', () => {
      audioFX.playTap();
      closeSuccessModal();
    });
  </script>
</body>
</html>
