# Weight-diary-app
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>14日間 体重管理・分析トラッカー</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <!-- Google Fonts: Maru Gothic / Noto Sans JP -->
    <link href="https://fonts.googleapis.com/css2?family=Kiwi+Maru:wght@400;500&family=M+PLUS+Rounded+1c:wght@400;500;700;800&family=Noto+Sans+JP:wght@400;500;600&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'M PLUS Rounded 1c', 'Kiwi Maru', 'Noto Sans JP', sans-serif;
            background: linear-gradient(135deg, #fffaf5 0%, #fef2f2 50%, #f0fdf4 100%);
            background-attachment: fixed;
        }
        /* Custom soft scrollbar */
        ::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #fdf2f8;
        }
        ::-webkit-scrollbar-thumb {
            background: #fbcfe8;
            border-radius: 9999px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #f472b6;
        }
    </style>
</head>
<body class="text-slate-700 min-h-screen flex flex-col relative pb-12">

    <!-- Header -->
    <header class="bg-white/80 backdrop-blur-md border-b border-rose-100/60 sticky top-0 z-30 shadow-sm">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-3.5 flex flex-col sm:flex-row items-center justify-between gap-3">
            <div class="flex items-center space-x-3">
                <div class="p-2.5 bg-gradient-to-br from-rose-400 to-amber-400 text-white rounded-2xl shadow-md shadow-rose-200/60 flex items-center justify-center">
                    <i data-lucide="heart-handshake" class="w-6 h-6"></i>
                </div>
                <div>
                    <h1 class="text-lg sm:text-xl font-bold text-slate-800 tracking-tight flex items-center gap-2">
                        14日間 体重トラッカー
                        <span class="text-xs font-normal text-rose-500 bg-rose-50 px-2.5 py-0.5 rounded-full border border-rose-200/60">🌸 ぽかぽかログ</span>
                    </h1>
                    <p class="text-xs text-slate-500 mt-0.5">起床・朝食後・夕食後・就寝前の4回測定バイタルノート</p>
                </div>
            </div>
            
            <div class="flex items-center gap-2">
                <button id="btnSampleData" class="px-3.5 py-2 text-xs font-medium text-amber-800 bg-amber-50 hover:bg-amber-100/80 border border-amber-200/60 rounded-2xl transition-all shadow-sm flex items-center gap-1.5 active:scale-95">
                    <i data-lucide="sparkles" class="w-4 h-4 text-amber-500"></i>
                    サンプルデータ
                </button>
                <button id="btnClearData" class="px-3.5 py-2 text-xs font-medium text-rose-700 bg-rose-50 hover:bg-rose-100/80 border border-rose-200/60 rounded-2xl transition-all shadow-sm flex items-center gap-1.5 active:scale-95">
                    <i data-lucide="trash-2" class="w-4 h-4"></i>
                    リセット
                </button>
                <button id="btnExportCsv" class="px-3.5 py-2 text-xs font-medium text-emerald-800 bg-emerald-50 hover:bg-emerald-100/80 border border-emerald-200/60 rounded-2xl transition-all shadow-sm flex items-center gap-1.5 active:scale-95">
                    <i data-lucide="download" class="w-4 h-4"></i>
                    CSV保存
                </button>
            </div>
        </div>
    </header>

    <!-- Main Content -->
    <main class="flex-1 max-w-7xl w-full mx-auto px-4 sm:px-6 lg:px-8 py-6 space-y-6">

        <!-- Date Range Navigation Bar -->
        <div class="bg-white/90 backdrop-blur-sm p-4 rounded-3xl border border-rose-100/80 shadow-sm flex flex-col md:flex-row items-center justify-between gap-4">
            <div class="flex items-center space-x-2">
                <button id="btnPrevPeriod" class="p-2 hover:bg-rose-50 rounded-2xl text-slate-600 transition-colors">
                    <i data-lucide="chevron-left" class="w-5 h-5"></i>
                </button>
                <div class="text-center px-4">
                    <span id="periodLabel" class="text-sm font-bold text-slate-800">----年--月--日 〜 ----年--月--日</span>
                    <span class="text-xs text-rose-400 font-medium block mt-0.5">🗓️ 月曜始まり 14日間の記録</span>
                </div>
                <button id="btnNextPeriod" class="p-2 hover:bg-rose-50 rounded-2xl text-slate-600 transition-colors">
                    <i data-lucide="chevron-right" class="w-5 h-5"></i>
                </button>
            </div>

            <button id="btnTodayPeriod" class="px-4 py-2 text-xs font-semibold text-rose-600 bg-rose-50 hover:bg-rose-100/80 rounded-2xl border border-rose-200/60 transition-all shadow-xs">
                今週(月曜始まり)を表示する 🌿
            </button>
        </div>

        <!-- Quick Entry Form Card -->
        <div class="bg-white/90 backdrop-blur-sm rounded-3xl border border-rose-100/80 shadow-sm p-5 space-y-4">
            <div class="border-b border-rose-100/60 pb-3 flex items-center justify-between">
                <h2 class="font-bold text-slate-800 flex items-center gap-2 text-base">
                    <span class="p-1.5 bg-rose-100 text-rose-500 rounded-xl">✏️</span>
                    今日の体重をきろくする
                </h2>
                <span class="text-xs text-slate-400">数字を入力して「保存」を押してね</span>
            </div>

            <form id="weightForm" class="space-y-3">
                <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-5 lg:grid-cols-6 gap-3.5 items-end">
                    <div>
                        <label class="block text-xs font-bold text-slate-600 mb-1">日付</label>
                        <input type="date" id="inputDate" required class="w-full px-3 py-2 border border-rose-200/80 bg-rose-50/20 rounded-2xl text-sm focus:outline-none focus:ring-2 focus:ring-rose-400">
                    </div>

                    <div>
                        <label class="block text-xs font-semibold text-slate-600 mb-1 flex items-center gap-1">
                            <span class="w-2.5 h-2.5 rounded-full bg-sky-400"></span> 🌅 起床直後 (kg)
                        </label>
                        <input type="number" step="0.01" min="20" max="250" id="inputWake" placeholder="例: 65.0" class="w-full px-3 py-2 border border-slate-200 rounded-2xl text-sm focus:outline-none focus:ring-2 focus:ring-sky-300">
                    </div>

                    <div>
                        <label class="block text-xs font-semibold text-slate-600 mb-1 flex items-center gap-1">
                            <span class="w-2.5 h-2.5 rounded-full bg-emerald-400"></span> 🥗 朝食直後 (kg)
                        </label>
                        <input type="number" step="0.01" min="20" max="250" id="inputBreakfast" placeholder="例: 65.5" class="w-full px-3 py-2 border border-slate-200 rounded-2xl text-sm focus:outline-none focus:ring-2 focus:ring-emerald-300">
                    </div>

                    <div>
                        <label class="block text-xs font-semibold text-slate-600 mb-1 flex items-center gap-1">
                            <span class="w-2.5 h-2.5 rounded-full bg-amber-400"></span> 🍲 夕食直後 (kg)
                        </label>
                        <input type="number" step="0.01" min="20" max="250" id="inputDinner" placeholder="例: 66.2" class="w-full px-3 py-2 border border-slate-200 rounded-2xl text-sm focus:outline-none focus:ring-2 focus:ring-amber-300">
                    </div>

                    <div>
                        <label class="block text-xs font-semibold text-slate-600 mb-1 flex items-center gap-1">
                            <span class="w-2.5 h-2.5 rounded-full bg-indigo-400"></span> 🌙 就寝直前 (kg)
                        </label>
                        <input type="number" step="0.01" min="20" max="250" id="inputBed" placeholder="例: 66.0" class="w-full px-3 py-2 border border-slate-200 rounded-2xl text-sm focus:outline-none focus:ring-2 focus:ring-indigo-300">
                    </div>

                    <div class="sm:col-span-2 md:col-span-1 lg:col-span-1">
                        <button type="submit" class="w-full py-2.5 bg-gradient-to-r from-rose-500 to-amber-500 hover:from-rose-600 hover:to-amber-600 text-white font-bold text-sm rounded-2xl transition-all shadow-md shadow-rose-200/80 flex items-center justify-center gap-2 active:scale-95">
                            <i data-lucide="check-circle" class="w-4 h-4"></i>
                            保存する
                        </button>
                    </div>
                </div>

                <div id="formAlert" class="hidden p-3 rounded-2xl text-xs flex items-center gap-2"></div>
            </form>
        </div>

        <!-- Metric Summary Cards -->
        <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
            <div class="bg-white/90 backdrop-blur-sm p-4 rounded-3xl border border-sky-100/80 shadow-sm flex items-center justify-between">
                <div>
                    <p class="text-xs font-semibold text-slate-500">期間内の平均体重</p>
                    <h3 id="statAvgWeight" class="text-2xl font-black text-slate-800 mt-1">-- <span class="text-sm font-normal text-slate-400">kg</span></h3>
                </div>
                <div class="p-3 bg-sky-50 text-sky-500 rounded-2xl">
                    <i data-lucide="scale" class="w-5 h-5"></i>
                </div>
            </div>

            <div class="bg-white/90 backdrop-blur-sm p-4 rounded-3xl border border-rose-100/80 shadow-sm flex items-center justify-between">
                <div>
                    <p class="text-xs font-semibold text-slate-500">期間内の体重変化</p>
                    <h3 id="statWeightChange" class="text-2xl font-black text-slate-800 mt-1">-- <span class="text-sm font-normal text-slate-400">kg</span></h3>
                </div>
                <div id="statWeightChangeIcon" class="p-3 bg-rose-50 text-rose-500 rounded-2xl">
                    <i data-lucide="trending-up" class="w-5 h-5"></i>
                </div>
            </div>

            <div class="bg-white/90 backdrop-blur-sm p-4 rounded-3xl border border-rose-100/80 shadow-sm flex items-center justify-between">
                <div>
                    <p class="text-xs font-semibold text-slate-500">夕食＜就寝（夜間増加日）</p>
                    <h3 id="statNightIncreaseCount" class="text-2xl font-black text-rose-500 mt-1">0 <span class="text-sm font-normal text-slate-400">/ 14日</span></h3>
                </div>
                <div class="p-3 bg-rose-50 text-rose-500 rounded-2xl">
                    <i data-lucide="sparkles" class="w-5 h-5"></i>
                </div>
            </div>

            <div class="bg-white/90 backdrop-blur-sm p-4 rounded-3xl border border-amber-100/80 shadow-sm flex items-center justify-between">
                <div>
                    <p class="text-xs font-semibold text-slate-500">平均 夜間差分(就寝-夕食)</p>
                    <h3 id="statAvgNightDiff" class="text-2xl font-black text-slate-800 mt-1">-- <span class="text-sm font-normal text-slate-400">kg</span></h3>
                </div>
                <div class="p-3 bg-amber-50 text-amber-500 rounded-2xl">
                    <i data-lucide="moon" class="w-5 h-5"></i>
                </div>
            </div>
        </div>

        <!-- Chart Section -->
        <div class="bg-white/90 backdrop-blur-sm rounded-3xl border border-rose-100/80 shadow-sm p-5 space-y-4">
            <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-4 border-b border-rose-100/60 pb-4">
                <div>
                    <h2 class="text-lg font-bold text-slate-800 flex items-center gap-2">
                        <span class="p-1.5 bg-indigo-100 text-indigo-500 rounded-xl">📈</span>
                        14日間の体重推移グラフ
                    </h2>
                    <p class="text-xs text-slate-500 mt-0.5">※ Y軸は平均体重から ±1.5kg（0.5kg単位）で自動調整されます</p>
                </div>

                <!-- View Switcher Tabs -->
                <div class="inline-flex p-1 bg-slate-100/80 rounded-2xl text-xs font-bold self-start sm:self-auto" id="chartViewTabs">
                    <button data-view="timeline" class="chart-tab-btn px-3.5 py-1.5 rounded-xl bg-white text-rose-500 shadow-sm transition-all">
                        時系列連続 (56点)
                    </button>
                    <button data-view="daily" class="chart-tab-btn px-3.5 py-1.5 rounded-xl text-slate-500 hover:text-slate-800 transition-all">
                        時間帯別 (4本線)
                    </button>
                    <button data-view="diff" class="chart-tab-btn px-3.5 py-1.5 rounded-xl text-slate-500 hover:text-slate-800 transition-all">
                        夕食 vs 就寝 差分
                    </button>
                </div>
            </div>

            <!-- Legend and Indicator Help -->
            <div class="flex flex-wrap items-center gap-4 text-xs bg-rose-50/40 p-3.5 rounded-2xl border border-rose-100/50">
                <div class="flex items-center gap-1.5">
                    <span class="w-3 h-3 rounded-full bg-sky-400 inline-block"></span>
                    <span class="text-slate-600 font-medium">起床直後</span>
                </div>
                <div class="flex items-center gap-1.5">
                    <span class="w-3 h-3 rounded-full bg-emerald-400 inline-block"></span>
                    <span class="text-slate-600 font-medium">朝食直後</span>
                </div>
                <div class="flex items-center gap-1.5">
                    <span class="w-3 h-3 rounded-full bg-amber-400 inline-block"></span>
                    <span class="text-slate-600 font-medium">夕食直後</span>
                </div>
                <div class="flex items-center gap-1.5">
                    <span class="w-3 h-3 rounded-full bg-indigo-400 inline-block"></span>
                    <span class="text-slate-600 font-medium">就寝直前（通常）</span>
                </div>
                <div class="flex items-center gap-1.5 font-bold text-rose-500 bg-rose-100/60 px-2.5 py-1 rounded-xl border border-rose-200/60">
                    <span class="w-3 h-3 rounded-full bg-rose-500 inline-block animate-pulse"></span>
                    <span>就寝直前（夕食より増えた日：ピンク強調）</span>
                </div>
            </div>

            <!-- Chart Canvas Container -->
            <div class="relative w-full h-[380px] sm:h-[420px]">
                <canvas id="weightChart"></canvas>
            </div>
        </div>

        <!-- 14-Day Table View -->
        <div class="bg-white/90 backdrop-blur-sm rounded-3xl border border-rose-100/80 shadow-sm p-5 space-y-3">
            <div class="flex items-center justify-between border-b border-rose-100/60 pb-3">
                <h3 class="font-bold text-slate-800 flex items-center gap-2 text-base">
                    <span class="p-1.5 bg-emerald-100 text-emerald-600 rounded-xl">📋</span>
                    14日間の記録ノート
                </h3>
                <span class="text-xs text-slate-400">※ 表の行を押すと上のフォームに入力データが呼び出されます</span>
            </div>

            <div class="overflow-x-auto">
                <table class="w-full text-left border-collapse min-w-[700px]">
                    <thead>
                        <tr class="bg-rose-50/50 border-b border-rose-100/60 text-xs font-bold text-slate-600">
                            <th class="py-3 px-3.5 w-[15%] rounded-l-2xl">日付</th>
                            <th class="py-3 px-3.5 w-[15%] text-right">起床直後</th>
                            <th class="py-3 px-3.5 w-[15%] text-right">朝食直後</th>
                            <th class="py-3 px-3.5 w-[15%] text-right">夕食直後</th>
                            <th class="py-3 px-3.5 w-[15%] text-right">就寝直前</th>
                            <th class="py-3 px-3.5 w-[25%] text-right rounded-r-2xl">夕食→就寝 差分</th>
                        </tr>
                    </thead>
                    <tbody id="dataTableBody" class="divide-y divide-rose-50/80">
                        <!-- Rows populated by JS -->
                    </tbody>
                </table>
            </div>
        </div>

        <!-- Gemini AI Health Coach Section (Placed at the bottom) -->
        <div class="bg-gradient-to-br from-slate-900 via-rose-950 to-indigo-950 rounded-3xl p-5 sm:p-7 text-white shadow-xl space-y-5 border border-rose-500/20">
            <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-4 border-b border-white/10 pb-4">
                <div class="flex items-center space-x-3.5">
                    <div class="p-3 bg-gradient-to-tr from-rose-400 to-amber-300 rounded-2xl text-slate-950 shadow-md">
                        <i data-lucide="sparkles" class="w-6 h-6"></i>
                    </div>
                    <div>
                        <div class="flex items-center gap-2">
                            <h2 class="text-lg sm:text-xl font-bold tracking-tight">Gemini AI やさしいヘルスパートナー</h2>
                            <span class="px-2.5 py-0.5 text-[10px] font-bold bg-rose-500/30 text-rose-200 rounded-full border border-rose-400/30">AI Powered</span>
                        </div>
                        <p class="text-xs text-rose-200/80 mt-0.5">14日間のバイタルをあたたかく分析。否定せず前向きなアドバイスをお届けします 🌸</p>
                    </div>
                </div>

                <div class="flex flex-wrap items-center gap-2">
                    <button id="btnAiAnalyze" class="px-4 py-2.5 bg-gradient-to-r from-rose-400 via-amber-300 to-amber-400 hover:from-rose-300 hover:to-amber-200 text-slate-950 font-bold text-xs rounded-2xl shadow-lg shadow-rose-900/40 transition-all flex items-center gap-1.5 active:scale-95">
                        <i data-lucide="heart" class="w-4 h-4 text-rose-700"></i>
                        ✨ AIアドバイスを受け取る
                    </button>
                    <button id="btnAiGenCard" class="px-3.5 py-2.5 bg-white/10 hover:bg-white/20 text-white font-medium text-xs rounded-2xl transition-all flex items-center gap-1.5 border border-white/15">
                        <i data-lucide="image" class="w-4 h-4 text-rose-300"></i>
                        🎨 応援バッジ画像生成
                    </button>
                </div>
            </div>

            <!-- AI Status / Spinner -->
            <div id="aiLoadingState" class="hidden py-8 text-center space-y-3">
                <div class="inline-block animate-spin rounded-full h-8 w-8 border-4 border-rose-400 border-t-transparent"></div>
                <p id="aiLoadingText" class="text-xs text-rose-200 font-medium">Gemini AI がやさしく言葉を準備中...</p>
            </div>

            <!-- AI Output Card (Hidden initially) -->
            <div id="aiResultCard" class="hidden bg-white/10 backdrop-blur-md border border-white/15 rounded-3xl p-5 space-y-4 shadow-inner">
                <div class="flex flex-col sm:flex-row items-start sm:items-center justify-between gap-3 border-b border-white/10 pb-3">
                    <div class="flex items-center gap-3">
                        <div id="aiGradeBadge" class="w-12 h-12 rounded-2xl bg-gradient-to-tr from-amber-400 to-rose-400 text-slate-950 font-black text-2xl flex items-center justify-center shadow-md">
                            -
                        </div>
                        <div>
                            <span class="text-[10px] text-rose-300 uppercase tracking-widest block font-bold">14日間の頑張り評価</span>
                            <h4 id="aiSummaryTitle" class="text-sm font-bold text-white">---</h4>
                        </div>
                    </div>

                    <!-- TTS Voice Playback Button -->
                    <button id="btnPlayTts" class="px-3.5 py-2 bg-rose-500/30 hover:bg-rose-500/50 text-white rounded-2xl text-xs font-semibold flex items-center gap-1.5 border border-rose-400/40 transition-all active:scale-95">
                        <i data-lucide="volume-2" class="w-4 h-4 text-amber-300"></i>
                        <span id="ttsBtnText">🔊 声で聴いてみる</span>
                    </button>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-3 gap-4 text-xs">
                    <!-- Good Points -->
                    <div class="bg-slate-950/40 p-4 rounded-2xl border border-white/5 space-y-2">
                        <h5 class="font-bold text-emerald-300 flex items-center gap-1.5 text-sm">
                            <i data-lucide="smile" class="w-4 h-4"></i>
                            すてきな点・成果 🌸
                        </h5>
                        <ul id="aiGoodPoints" class="space-y-1.5 text-slate-200 list-disc list-inside leading-relaxed">
                        </ul>
                    </div>

                    <!-- Night Gain Analysis -->
                    <div class="bg-slate-950/40 p-4 rounded-2xl border border-white/5 space-y-2">
                        <h5 class="font-bold text-rose-300 flex items-center gap-1.5 text-sm">
                            <i data-lucide="moon" class="w-4 h-4"></i>
                            夜の体重変化と気づき 🌙
                        </h5>
                        <p id="aiNightAnalysis" class="text-slate-200 leading-relaxed">
                        </p>
                    </div>

                    <!-- Actionable Tips -->
                    <div class="bg-slate-950/40 p-4 rounded-2xl border border-white/5 space-y-2">
                        <h5 class="font-bold text-amber-300 flex items-center gap-1.5 text-sm">
                            <i data-lucide="coffee" class="w-4 h-4"></i>
                            明日からのやさしい一手 🌱
                        </h5>
                        <ul id="aiActionTips" class="space-y-1.5 text-slate-200 list-disc list-inside leading-relaxed">
                        </ul>
                    </div>
                </div>

                <!-- Hidden audio container for TTS -->
                <audio id="ttsAudioPlayer" class="hidden"></audio>
            </div>

            <!-- Generated Illustration Badge Display -->
            <div id="aiImageContainer" class="hidden bg-white/5 border border-white/10 rounded-3xl p-4 flex flex-col sm:flex-row items-center gap-4">
                <img id="aiGeneratedBadgeImg" class="w-32 h-32 rounded-2xl object-cover shadow-lg border border-white/20" alt="AI Motivation Badge" src="data:image/png;base64,iVBORw0KGgo=" />
                <div class="space-y-1 text-center sm:text-left">
                    <span class="text-[10px] text-amber-300 font-bold uppercase tracking-wider">Gemini Image 生成</span>
                    <h5 class="text-sm font-bold text-white">14日間達成記念イラストバッジ 🎁</h5>
                    <p class="text-xs text-slate-300">14日間の記録、本当にお疲れ様でした！このバッジを励みに次の2週間もマイペースに歩んでいきましょう。</p>
                </div>
            </div>
        </div>
    </main>

    <!-- Custom Soft Notification Modal -->
    <div id="customModal" class="fixed inset-0 z-50 flex items-center justify-center bg-slate-900/40 backdrop-blur-xs hidden opacity-0 transition-opacity duration-200">
        <div class="bg-white rounded-3xl shadow-2xl w-full max-w-sm mx-4 overflow-hidden transform scale-95 transition-transform duration-200 border border-rose-100" id="customModalContent">
            <div class="p-6">
                <div class="flex items-center gap-3 mb-3">
                    <div id="modalIconContainer" class="p-2.5 rounded-2xl">
                        <i id="modalIcon" data-lucide="info" class="w-6 h-6"></i>
                    </div>
                    <h3 id="modalTitle" class="text-base font-bold text-slate-800">お知らせ</h3>
                </div>
                <p id="modalMessage" class="text-slate-600 text-sm leading-relaxed"></p>
            </div>
            <div class="bg-rose-50/50 px-6 py-3.5 flex justify-end gap-2 border-t border-rose-100/60" id="modalActions">
                <button id="btnModalClose" class="px-4 py-2 bg-rose-500 hover:bg-rose-600 text-white text-xs font-bold rounded-2xl transition-all shadow-xs">
                    OK
                </button>
            </div>
        </div>
    </div>

    <script>
        // State Management
        let weightRecords = {}; // Format: { 'YYYY-MM-DD': { wake: null, breakfast: null, dinner: null, bed: null } }
        let currentPeriodStart = getMonday(new Date()); 
        let currentChartView = 'timeline'; // 'timeline', 'daily', 'diff'
        let chartInstance = null;
        let aiSessionResult = null; // Cache AI result per run

        // DOM Elements
        const el = {
            form: document.getElementById('weightForm'),
            inputDate: document.getElementById('inputDate'),
            inputWake: document.getElementById('inputWake'),
            inputBreakfast: document.getElementById('inputBreakfast'),
            inputDinner: document.getElementById('inputDinner'),
            inputBed: document.getElementById('inputBed'),
            periodLabel: document.getElementById('periodLabel'),
            dataTableBody: document.getElementById('dataTableBody'),
            formAlert: document.getElementById('formAlert'),
            
            // Stats
            statAvgWeight: document.getElementById('statAvgWeight'),
            statWeightChange: document.getElementById('statWeightChange'),
            statWeightChangeIcon: document.getElementById('statWeightChangeIcon'),
            statNightIncreaseCount: document.getElementById('statNightIncreaseCount'),
            statAvgNightDiff: document.getElementById('statAvgNightDiff'),

            // Custom Modal
            modal: document.getElementById('customModal'),
            modalContent: document.getElementById('customModalContent'),
            modalTitle: document.getElementById('modalTitle'),
            modalMessage: document.getElementById('modalMessage'),
            modalIcon: document.getElementById('modalIcon'),
            modalIconContainer: document.getElementById('modalIconContainer'),
            btnModalClose: document.getElementById('btnModalClose'),
            modalActions: document.getElementById('modalActions'),
            
            // AI 
            aiLoadingState: document.getElementById('aiLoadingState'),
            aiLoadingText: document.getElementById('aiLoadingText'),
            aiResultCard: document.getElementById('aiResultCard'),
            aiImageContainer: document.getElementById('aiImageContainer'),
            aiGeneratedBadgeImg: document.getElementById('aiGeneratedBadgeImg'),
            ttsAudioPlayer: document.getElementById('ttsAudioPlayer'),
            btnPlayTts: document.getElementById('btnPlayTts'),
            ttsBtnText: document.getElementById('ttsBtnText')
        };

        // Initialize Lucide icons
        lucide.createIcons();

        function formatDate(date) {
            const y = date.getFullYear();
            const m = String(date.getMonth() + 1).padStart(2, '0');
            const d = String(date.getDate()).padStart(2, '0');
            return `${y}-${m}-${d}`;
        }

        function formatDisplayDate(dateStr) {
            const date = new Date(dateStr);
            const days = ['日', '月', '火', '水', '木', '金', '土'];
            return `${date.getMonth() + 1}/${date.getDate()} (${days[date.getDay()]})`;
        }

        function getMonday(d) {
            d = new Date(d);
            const day = d.getDay(),
                diff = d.getDate() - day + (day == 0 ? -6 : 1);
            return new Date(d.setDate(diff));
        }

        function addDays(date, days) {
            const result = new Date(date);
            result.setDate(result.getDate() + days);
            return result;
        }

        function showModal(title, message, type = 'info', onConfirm = null) {
            el.modalTitle.textContent = title;
            el.modalMessage.textContent = message;
            
            el.modalIconContainer.className = 'p-2.5 rounded-2xl';
            el.modalActions.innerHTML = '';

            if (type === 'error') {
                el.modalIconContainer.classList.add('bg-rose-100', 'text-rose-600');
                el.modalIcon.setAttribute('data-lucide', 'alert-circle');
            } else if (type === 'success') {
                el.modalIconContainer.classList.add('bg-emerald-100', 'text-emerald-600');
                el.modalIcon.setAttribute('data-lucide', 'check-circle-2');
            } else if (type === 'confirm') {
                el.modalIconContainer.classList.add('bg-amber-100', 'text-amber-600');
                el.modalIcon.setAttribute('data-lucide', 'help-circle');
                
                const cancelBtn = document.createElement('button');
                cancelBtn.className = 'px-4 py-2 bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-semibold rounded-2xl transition-colors';
                cancelBtn.textContent = 'キャンセル';
                cancelBtn.onclick = closeModal;
                el.modalActions.appendChild(cancelBtn);
            } else {
                el.modalIconContainer.classList.add('bg-sky-100', 'text-sky-600');
                el.modalIcon.setAttribute('data-lucide', 'info');
            }

            const okBtn = document.createElement('button');
            okBtn.className = `px-4 py-2 text-white text-xs font-bold rounded-2xl transition-all shadow-xs ${type === 'error' ? 'bg-rose-500 hover:bg-rose-600' : 'bg-rose-500 hover:bg-rose-600'}`;
            okBtn.textContent = type === 'confirm' ? '実行する' : 'OK';
            okBtn.onclick = () => {
                closeModal();
                if (onConfirm) onConfirm();
            };
            el.modalActions.appendChild(okBtn);

            lucide.createIcons();

            el.modal.classList.remove('hidden');
            setTimeout(() => {
                el.modal.classList.remove('opacity-0');
                el.modalContent.classList.remove('scale-95');
            }, 10);
        }

        function closeModal() {
            el.modal.classList.add('opacity-0');
            el.modalContent.classList.add('scale-95');
            setTimeout(() => {
                el.modal.classList.add('hidden');
            }, 200);
        }

        function getCurrentPeriodDates() {
            const dates = [];
            for (let i = 0; i < 14; i++) {
                dates.push(formatDate(addDays(currentPeriodStart, i)));
            }
            return dates;
        }

        function updateUI() {
            const dates = getCurrentPeriodDates();
            el.periodLabel.textContent = `${formatDisplayDate(dates[0])} 〜 ${formatDisplayDate(dates[13])}`;
            
            if (!el.inputDate.value || dates.indexOf(el.inputDate.value) === -1) {
                el.inputDate.value = dates[0];
            }

            renderTable(dates);
            renderChart(dates);
            calculateStats(dates);
        }

        function renderTable(dates) {
            el.dataTableBody.innerHTML = '';
            
            dates.forEach(dateStr => {
                const data = weightRecords[dateStr] || { wake: null, breakfast: null, dinner: null, bed: null };
                const tr = document.createElement('tr');
                tr.className = 'hover:bg-rose-50/40 cursor-pointer transition-colors border-b border-rose-50/80 last:border-0';
                tr.onclick = () => loadDataIntoForm(dateStr);

                let diffText = '-';
                let diffClass = 'text-slate-400';
                if (data.dinner !== null && data.bed !== null) {
                    const diff = (data.bed - data.dinner).toFixed(2);
                    if (diff > 0) {
                        diffText = `+${diff} kg`;
                        diffClass = 'text-rose-600 font-bold bg-rose-100/70 px-2.5 py-0.5 rounded-full inline-block border border-rose-200/60';
                    } else if (diff < 0) {
                        diffText = `${diff} kg`;
                        diffClass = 'text-emerald-600 font-bold';
                    } else {
                        diffText = `±0 kg`;
                        diffClass = 'text-slate-500';
                    }
                }

                tr.innerHTML = `
                    <td class="py-3 px-3.5 text-xs font-semibold text-slate-700">${formatDisplayDate(dateStr)}</td>
                    <td class="py-3 px-3.5 text-sm text-right ${data.wake ? 'text-slate-800 font-medium' : 'text-slate-300'}">${data.wake ? data.wake.toFixed(2) : '-'}</td>
                    <td class="py-3 px-3.5 text-sm text-right ${data.breakfast ? 'text-slate-800 font-medium' : 'text-slate-300'}">${data.breakfast ? data.breakfast.toFixed(2) : '-'}</td>
                    <td class="py-3 px-3.5 text-sm text-right ${data.dinner ? 'text-slate-800 font-medium' : 'text-slate-300'}">${data.dinner ? data.dinner.toFixed(2) : '-'}</td>
                    <td class="py-3 px-3.5 text-sm text-right ${data.bed ? 'text-slate-800 font-medium' : 'text-slate-300'}">${data.bed ? data.bed.toFixed(2) : '-'}</td>
                    <td class="py-3 px-3.5 text-xs text-right"><span class="${diffClass}">${diffText}</span></td>
                `;
                el.dataTableBody.appendChild(tr);
            });
        }

        function loadDataIntoForm(dateStr) {
            el.inputDate.value = dateStr;
            const data = weightRecords[dateStr] || { wake: '', breakfast: '', dinner: '', bed: '' };
            el.inputWake.value = data.wake || '';
            el.inputBreakfast.value = data.breakfast || '';
            el.inputDinner.value = data.dinner || '';
            el.inputBed.value = data.bed || '';
            
            el.form.scrollIntoView({ behavior: 'smooth', block: 'center' });
        }

        function renderChart(dates) {
            const ctx = document.getElementById('weightChart').getContext('2d');
            
            if (chartInstance) {
                chartInstance.destroy();
            }

            // Calculate average weight to set Y-axis boundaries (Avg - 1.5kg to Avg + 1.5kg)
            let allWeights = [];
            dates.forEach(d => {
                const rec = weightRecords[d];
                if (rec) {
                    [rec.wake, rec.breakfast, rec.dinner, rec.bed].forEach(w => {
                        if (w !== null && !isNaN(w) && w > 0) allWeights.push(w);
                    });
                }
            });

            let yMin = undefined;
            let yMax = undefined;

            if (allWeights.length > 0) {
                const avg = allWeights.reduce((a, b) => a + b, 0) / allWeights.length;
                const roundedAvg = Math.round(avg * 2) / 2; // round to nearest 0.5
                yMin = roundedAvg - 1.5;
                yMax = roundedAvg + 1.5;
            }

            const chartConfig = {
                type: 'line',
                data: {},
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    spanGaps: false,
                    interaction: { mode: 'index', intersect: false },
                    plugins: {
                        legend: { display: currentChartView === 'daily' },
                        tooltip: {
                            callbacks: {
                                label: function(context) {
                                    let label = context.dataset.label || '';
                                    if (label) label += ': ';
                                    if (context.parsed.y !== null) {
                                        label += context.parsed.y + ' kg';
                                    }
                                    return label;
                                }
                            }
                        }
                    },
                    scales: {
                        y: {
                            title: { display: true, text: '体重 (kg)', color: '#64748b' },
                            min: yMin,
                            max: yMax,
                            ticks: {
                                stepSize: 0.5,
                                color: '#64748b'
                            },
                            grid: {
                                color: '#f1f5f9'
                            }
                        }
                    }
                }
            };

            if (currentChartView === 'timeline') {
                const labels = [];
                const dataPoints = [];
                const pointColors = [];
                const pointRadii = [];

                dates.forEach(dateStr => {
                    const data = weightRecords[dateStr] || {};
                    const shortDate = dateStr.substring(5).replace('-', '/');
                    
                    labels.push(`${shortDate} 起床`, `${shortDate} 朝食`, `${shortDate} 夕食`, `${shortDate} 就寝`);
                    dataPoints.push(data.wake || null, data.breakfast || null, data.dinner || null, data.bed || null);

                    const colors = ['#38bdf8', '#34d399', '#fbbf24'];
                    const radii = [4, 4, 4];
                    
                    if (data.bed !== null && data.dinner !== null && data.bed > data.dinner) {
                        colors.push('#f43f5e'); // Warm rose alert
                        radii.push(6);
                    } else {
                        colors.push('#818cf8'); // Periwinkle normal
                        radii.push(4);
                    }
                    pointColors.push(...colors);
                    pointRadii.push(...radii);
                });

                chartConfig.data = {
                    labels: labels,
                    datasets: [{
                        label: '体重推移',
                        data: dataPoints,
                        borderColor: '#cbd5e1',
                        borderWidth: 2,
                        pointBackgroundColor: pointColors,
                        pointBorderColor: '#fff',
                        pointRadius: pointRadii,
                        pointHoverRadius: 7,
                        tension: 0.35
                    }]
                };
                chartConfig.options.plugins.legend.display = false;
                chartConfig.options.scales.x = {
                    ticks: {
                        callback: function(val, index) {
                            return index % 4 === 0 ? this.getLabelForValue(val).split(' ')[0] : '';
                        },
                        color: '#64748b'
                    },
                    grid: {
                        color: (context) => context.index % 4 === 0 ? '#f1f5f9' : 'transparent'
                    }
                };

            } else if (currentChartView === 'daily') {
                const labels = dates.map(d => d.substring(5).replace('-', '/'));
                const wakes = dates.map(d => weightRecords[d]?.wake || null);
                const breakfasts = dates.map(d => weightRecords[d]?.breakfast || null);
                const dinners = dates.map(d => weightRecords[d]?.dinner || null);
                const beds = dates.map(d => weightRecords[d]?.bed || null);

                const bedPointColors = dates.map(d => {
                    const rec = weightRecords[d];
                    return (rec && rec.bed !== null && rec.dinner !== null && rec.bed > rec.dinner) ? '#f43f5e' : '#818cf8';
                });

                chartConfig.data = {
                    labels: labels,
                    datasets: [
                        { label: '起床直後', data: wakes, borderColor: '#38bdf8', backgroundColor: '#38bdf8', tension: 0.3 },
                        { label: '朝食直後', data: breakfasts, borderColor: '#34d399', backgroundColor: '#34d399', tension: 0.3 },
                        { label: '夕食直後', data: dinners, borderColor: '#fbbf24', backgroundColor: '#fbbf24', tension: 0.3 },
                        { 
                            label: '就寝直前', 
                            data: beds, 
                            borderColor: '#818cf8', 
                            backgroundColor: '#818cf8', 
                            pointBackgroundColor: bedPointColors,
                            tension: 0.3 
                        }
                    ]
                };

            } else if (currentChartView === 'diff') {
                chartConfig.type = 'bar';
                const labels = dates.map(d => d.substring(5).replace('-', '/'));
                const diffs = dates.map(d => {
                    const rec = weightRecords[d];
                    if (rec && rec.bed !== null && rec.dinner !== null) {
                        return parseFloat((rec.bed - rec.dinner).toFixed(2));
                    }
                    return null;
                });

                const bgColors = diffs.map(val => val > 0 ? 'rgba(244, 63, 94, 0.7)' : 'rgba(52, 211, 153, 0.7)');
                const borderColors = diffs.map(val => val > 0 ? 'rgb(244, 63, 94)' : 'rgb(52, 211, 153)');

                chartConfig.data = {
                    labels: labels,
                    datasets: [{
                        label: '就寝時の体重増減量 (kg)',
                        data: diffs,
                        backgroundColor: bgColors,
                        borderColor: borderColors,
                        borderWidth: 1,
                        borderRadius: 8
                    }]
                };
                chartConfig.options.scales.y = {
                    title: { display: true, text: '差分 (kg)', color: '#64748b' },
                    ticks: { stepSize: 0.5, color: '#64748b' },
                    grid: { color: (ctx) => ctx.tick.value === 0 ? '#cbd5e1' : '#f1f5f9' }
                };
            }

            chartInstance = new Chart(ctx, chartConfig);
        }

        function calculateStats(dates) {
            let totalWeight = 0;
            let weightCount = 0;
            let firstWeight = null;
            let lastWeight = null;
            let nightIncreaseCount = 0;
            let validNightDays = 0;
            let totalNightDiff = 0;

            dates.forEach(dateStr => {
                const rec = weightRecords[dateStr];
                if (!rec) return;

                const validPoints = [rec.wake, rec.breakfast, rec.dinner, rec.bed].filter(v => v !== null);
                if (validPoints.length > 0) {
                    totalWeight += validPoints.reduce((a, b) => a + b, 0);
                    weightCount += validPoints.length;

                    if (firstWeight === null) firstWeight = validPoints[0];
                    lastWeight = validPoints[validPoints.length - 1];
                }

                if (rec.dinner !== null && rec.bed !== null) {
                    const diff = rec.bed - rec.dinner;
                    validNightDays++;
                    totalNightDiff += diff;
                    if (diff > 0) {
                        nightIncreaseCount++;
                    }
                }
            });

            if (weightCount > 0) {
                const avg = (totalWeight / weightCount).toFixed(2);
                el.statAvgWeight.innerHTML = `${avg} <span class="text-sm font-normal text-slate-400">kg</span>`;
            } else {
                el.statAvgWeight.innerHTML = `-- <span class="text-sm font-normal text-slate-400">kg</span>`;
            }

            if (firstWeight !== null && lastWeight !== null) {
                const change = (lastWeight - firstWeight).toFixed(2);
                const prefix = change > 0 ? '+' : '';
                el.statWeightChange.innerHTML = `${prefix}${change} <span class="text-sm font-normal text-slate-400">kg</span>`;
                
                el.statWeightChangeIcon.className = `p-3 rounded-2xl ${change > 0 ? 'bg-amber-50 text-amber-500' : 'bg-emerald-50 text-emerald-500'}`;
                el.statWeightChangeIcon.innerHTML = `<i data-lucide="trending-${change > 0 ? 'up' : 'down'}" class="w-5 h-5"></i>`;
                lucide.createIcons();
            } else {
                el.statWeightChange.innerHTML = `-- <span class="text-sm font-normal text-slate-400">kg</span>`;
            }

            el.statNightIncreaseCount.innerHTML = `${nightIncreaseCount} <span class="text-sm font-normal text-slate-400">/ 14日</span>`;
            
            if (validNightDays > 0) {
                const avgDiff = (totalNightDiff / validNightDays).toFixed(2);
                const prefix = avgDiff > 0 ? '+' : '';
                el.statAvgNightDiff.innerHTML = `${prefix}${avgDiff} <span class="text-sm font-normal text-slate-400">kg</span>`;
            } else {
                el.statAvgNightDiff.innerHTML = `-- <span class="text-sm font-normal text-slate-400">kg</span>`;
            }
        }

        el.form.addEventListener('submit', (e) => {
            e.preventDefault();
            const date = el.inputDate.value;
            const wake = el.inputWake.value ? parseFloat(el.inputWake.value) : null;
            const breakfast = el.inputBreakfast.value ? parseFloat(el.inputBreakfast.value) : null;
            const dinner = el.inputDinner.value ? parseFloat(el.inputDinner.value) : null;
            const bed = el.inputBed.value ? parseFloat(el.inputBed.value) : null;

            weightRecords[date] = { wake, breakfast, dinner, bed };

            el.formAlert.className = "p-3 rounded-2xl text-xs flex items-center gap-2 bg-emerald-50 text-emerald-700 border border-emerald-200/60 mt-2";
            el.formAlert.innerHTML = `<i data-lucide="check" class="w-4 h-4"></i> ${date} の記録を保存しました♪`;
            lucide.createIcons();
            setTimeout(() => { el.formAlert.classList.add('hidden'); }, 3000);

            updateUI();
        });

        document.getElementById('btnPrevPeriod').addEventListener('click', () => {
            currentPeriodStart = addDays(currentPeriodStart, -14);
            updateUI();
        });
        document.getElementById('btnNextPeriod').addEventListener('click', () => {
            currentPeriodStart = addDays(currentPeriodStart, 14);
            updateUI();
        });
        document.getElementById('btnTodayPeriod').addEventListener('click', () => {
            currentPeriodStart = getMonday(new Date());
            updateUI();
        });

        document.querySelectorAll('.chart-tab-btn').forEach(btn => {
            btn.addEventListener('click', (e) => {
                document.querySelectorAll('.chart-tab-btn').forEach(b => {
                    b.className = 'chart-tab-btn px-3.5 py-1.5 rounded-xl text-slate-500 hover:text-slate-800 transition-all';
                });
                e.target.className = 'chart-tab-btn px-3.5 py-1.5 rounded-xl bg-white text-rose-500 shadow-sm transition-all';
                
                currentChartView = e.target.getAttribute('data-view');
                updateUI();
            });
        });

        document.getElementById('btnSampleData').addEventListener('click', () => {
            showModal('サンプルデータ入れ込み', '現在の14日間に仮の体重データを入力しますか？', 'confirm', () => {
                const dates = getCurrentPeriodDates();
                let baseWeight = 65.0;
                
                dates.forEach((dateStr, index) => {
                    baseWeight = baseWeight - (Math.random() * 0.1) + (Math.random() * 0.05);
                    
                    const wake = baseWeight;
                    const breakfast = baseWeight + 0.3 + (Math.random() * 0.2);
                    const dinner = baseWeight + 0.5 + (Math.random() * 0.3);
                    
                    let bed;
                    if (index === 3 || index === 8 || index === 11) {
                        bed = dinner + 0.4 + (Math.random() * 0.2);
                    } else {
                        bed = dinner - 0.2 - (Math.random() * 0.2);
                    }

                    weightRecords[dateStr] = {
                        wake: parseFloat(wake.toFixed(2)),
                        breakfast: parseFloat(breakfast.toFixed(2)),
                        dinner: parseFloat(dinner.toFixed(2)),
                        bed: parseFloat(bed.toFixed(2))
                    };
                });
                updateUI();
                showModal('完了', 'サンプルデータを準備しました。', 'success');
            });
        });

        document.getElementById('btnClearData').addEventListener('click', () => {
            showModal('データ消去', 'すべての記録をリセットしますか？', 'confirm', () => {
                weightRecords = {};
                updateUI();
                el.aiResultCard.classList.add('hidden');
                el.aiImageContainer.classList.add('hidden');
                showModal('完了', 'データをクリアしました。', 'success');
            });
        });

        document.getElementById('btnExportCsv').addEventListener('click', () => {
            const dates = Object.keys(weightRecords).sort();
            if (dates.length === 0) {
                showModal('お知らせ', '保存するデータがまだありません。', 'error');
                return;
            }

            let csvContent = "data:text/csv;charset=utf-8,";
            csvContent += "日付,起床直後,朝食直後,夕食直後,就寝直前\n";

            dates.forEach(date => {
                const rec = weightRecords[date];
                const row = [
                    date,
                    rec.wake || '',
                    rec.breakfast || '',
                    rec.dinner || '',
                    rec.bed || ''
                ].join(",");
                csvContent += row + "\n";
            });

            const encodedUri = encodeURI(csvContent);
            const link = document.createElement("a");
            link.setAttribute("href", encodedUri);
            link.setAttribute("download", "weight_tracker_data.csv");
            document.body.appendChild(link); 
            link.click();
            document.body.removeChild(link);
        });

        document.getElementById('btnAiAnalyze').addEventListener('click', async () => {
            const dates = getCurrentPeriodDates();
            const dataToAnalyze = dates.map(d => ({ date: d, data: weightRecords[d] || null })).filter(item => item.data !== null && Object.values(item.data).some(v => v !== null));
            
            if (dataToAnalyze.length < 3) {
                showModal('もう少しデータが必要です', 'AI分析には少なくとも3日分ほどのデータ入力をおすすめします🌸', 'error');
                return;
            }

            el.aiResultCard.classList.add('hidden');
            el.aiLoadingState.classList.remove('hidden');
            el.aiLoadingText.textContent = "Gemini AI が14日間の記録をやさしく読み解いています...";

            try {
                const prompt = `あなたはとても優しく心温かい専属のヘルスコーチです。以下の14日間の1日4回（起床・朝食・夕食・就寝）の体重測定データを分析し、ユーザーを温かく包み込むフィードバックを提供してください。責めたり厳しく指導したりせず、できたことをほめ、前向きな気持ちになれる提案をしてください。

データ:
${JSON.stringify(dataToAnalyze, null, 2)}

以下のJSON形式で出力してください:
{
  "grade": "花丸, A, B, C のいずれか",
  "summaryTitle": "1行の温かく親しみやすい総評タイトル",
  "goodPoints": ["すてきな点や継続できていること1", "すてきな点2"],
  "nightAnalysis": "夕食から就寝までの変化に関するやさしいアドバイスと気づき（2-3文）",
  "actionTips": ["無理なく試せる明日からの心地よい工夫1", "心地よい工夫2"]
}`;

                const apiKey = "";
                const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-3-flash-preview:generateContent?key=${apiKey}`;
                
                const payload = {
                    contents: [{ parts: [{ text: prompt }] }],
                    generationConfig: {
                        responseMimeType: "application/json",
                        responseSchema: {
                            type: "OBJECT",
                            properties: {
                                grade: { type: "STRING" },
                                summaryTitle: { type: "STRING" },
                                goodPoints: { type: "ARRAY", items: { type: "STRING" } },
                                nightAnalysis: { type: "STRING" },
                                actionTips: { type: "ARRAY", items: { type: "STRING" } }
                            },
                            required: ["grade", "summaryTitle", "goodPoints", "nightAnalysis", "actionTips"]
                        }
                    }
                };

                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });

                if (!response.ok) throw new Error(`API Error: ${response.status}`);
                
                const result = await response.json();
                const jsonText = result.candidates[0].content.parts[0].text;
                const analysis = JSON.parse(jsonText);
                aiSessionResult = analysis;

                document.getElementById('aiGradeBadge').textContent = analysis.grade;
                document.getElementById('aiSummaryTitle').textContent = analysis.summaryTitle;
                
                document.getElementById('aiGoodPoints').innerHTML = analysis.goodPoints.map(p => `<li>${p}</li>`).join('');
                document.getElementById('aiNightAnalysis').textContent = analysis.nightAnalysis;
                document.getElementById('aiActionTips').innerHTML = analysis.actionTips.map(p => `<li>${p}</li>`).join('');

                el.aiLoadingState.classList.add('hidden');
                el.aiResultCard.classList.remove('hidden');

            } catch (error) {
                console.error("AI Analysis Error:", error);
                el.aiLoadingState.classList.add('hidden');
                showModal('AI分析エラー', '分析の取得中にエラーが発生しました。時間をおいてもう一度お試しください。', 'error');
            }
        });

        function base64ToArrayBuffer(base64) {
            const binaryString = window.atob(base64);
            const len = binaryString.length;
            const bytes = new Uint8Array(len);
            for (let i = 0; i < len; i++) {
                bytes[i] = binaryString.charCodeAt(i);
            }
            return bytes.buffer;
        }

        function pcmToWav(pcmData, sampleRate) {
            const numChannels = 1;
            const bitsPerSample = 16;
            const blockAlign = numChannels * (bitsPerSample / 8);
            const byteRate = sampleRate * blockAlign;
            const dataSize = pcmData.length * 2;
            const buffer = new ArrayBuffer(44 + dataSize);
            const view = new DataView(buffer);

            const writeString = (offset, string) => {
                for (let i = 0; i < string.length; i++) {
                    view.setUint8(offset + i, string.charCodeAt(i));
                }
            };

            writeString(0, 'RIFF');
            view.setUint32(4, 36 + dataSize, true);
            writeString(8, 'WAVE');
            writeString(12, 'fmt ');
            view.setUint32(16, 16, true);
            view.setUint16(20, 1, true);
            view.setUint16(22, numChannels, true);
            view.setUint32(24, sampleRate, true);
            view.setUint32(28, byteRate, true);
            view.setUint16(32, blockAlign, true);
            view.setUint16(34, bitsPerSample, true);
            writeString(36, 'data');
            view.setUint32(40, dataSize, true);

            let offset = 44;
            for (let i = 0; i < pcmData.length; i++, offset += 2) {
                view.setInt16(offset, pcmData[i], true);
            }

            return new Blob([view], { type: 'audio/wav' });
        }

        document.getElementById('btnPlayTts').addEventListener('click', async () => {
            if (!aiSessionResult) return;

            const isPlaying = !el.ttsAudioPlayer.paused && el.ttsAudioPlayer.currentTime > 0;
            if (isPlaying) {
                el.ttsAudioPlayer.pause();
                el.ttsAudioPlayer.currentTime = 0;
                el.ttsBtnText.textContent = "🔊 声で聴いてみる";
                return;
            }

            el.ttsBtnText.textContent = "⏳ 声を準備中...";
            el.btnPlayTts.disabled = true;

            try {
                const scriptText = `
                こんにちは、あなたのAIヘルスパートナーです。14日間の記録、本当にお疲れ様でした！
                メッセージをお届けしますね。「${aiSessionResult.summaryTitle}」。
                すてきな点として、${aiSessionResult.goodPoints.join("。また、")}。
                夜のお話ですが、${aiSessionResult.nightAnalysis}
                明日からは、${aiSessionResult.actionTips.join("。そして、")}、を試してみてくださいね。応援しています！
                `;

                const payload = {
                    contents: [{ parts: [{ text: scriptText }] }],
                    generationConfig: {
                        responseModalities: ["AUDIO"],
                        speechConfig: {
                            voiceConfig: {
                                prebuiltVoiceConfig: { voiceName: "Aoede" }
                            }
                        }
                    },
                    model: "gemini-2.5-flash-preview-tts"
                };

                const apiKey = "";
                const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-tts:generateContent?key=${apiKey}`;

                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });

                if (!response.ok) throw new Error('TTS API Failed');
                
                const result = await response.json();
                const part = result?.candidates?.[0]?.content?.parts?.[0];
                const audioData = part?.inlineData?.data;
                const mimeType = part?.inlineData?.mimeType;

                if (audioData && mimeType && mimeType.startsWith("audio/")) {
                    const rateMatch = mimeType.match(/rate=(\d+)/);
                    const sampleRate = rateMatch ? parseInt(rateMatch[1], 10) : 24000;
                    
                    const pcmData = base64ToArrayBuffer(audioData);
                    const pcm16 = new Int16Array(pcmData);
                    const wavBlob = pcmToWav(pcm16, sampleRate);
                    const audioUrl = URL.createObjectURL(wavBlob);
                    
                    el.ttsAudioPlayer.src = audioUrl;
                    el.ttsAudioPlayer.play();
                    
                    el.ttsBtnText.textContent = "⏹️ 音声を止める";
                    
                    el.ttsAudioPlayer.onended = () => {
                        el.ttsBtnText.textContent = "🔊 声で聴いてみる";
                    };
                } else {
                    throw new Error("Invalid audio data received.");
                }
            } catch (error) {
                console.error("TTS Error:", error);
                showModal('音声エラー', '音声の読み込みに失敗しました。', 'error');
                el.ttsBtnText.textContent = "🔊 声で聴いてみる";
            } finally {
                el.btnPlayTts.disabled = false;
            }
        });

        document.getElementById('btnAiGenCard').addEventListener('click', async () => {
            el.aiLoadingState.classList.remove('hidden');
            el.aiLoadingText.textContent = "Gemini AI がかわいい応援バッジを描いています...";
            el.aiImageContainer.classList.add('hidden');

            try {
                const prompt = `A cute, warm, vector style digital achievement badge for weight tracking and health. Pastel soft colors, glowing flowers, friendly star or trophy character, soft lighting, cozy mobile app achievement icon. No text inside the image.`;

                const payload = {
                    contents: [{ parts: [{ text: prompt }] }],
                    generationConfig: {
                        responseModalities: ['IMAGE'],
                        imageConfig: { aspectRatio: "1:1" }
                    }
                };

                const apiKey = "";
                const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-3.1-flash-lite-image:generateContent?key=${apiKey}`;

                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });

                if (!response.ok) throw new Error('Image Gen API Failed');

                const result = await response.json();
                const part = result?.candidates?.[0]?.content?.parts?.find(p => p.inlineData);
                
                if (part && part.inlineData) {
                    const imageUrl = `data:${part.inlineData.mimeType};base64,${part.inlineData.data}`;
                    el.aiGeneratedBadgeImg.src = imageUrl;
                    
                    el.aiLoadingState.classList.add('hidden');
                    el.aiImageContainer.classList.remove('hidden');
                } else {
                    throw new Error("No image data returned.");
                }

            } catch (error) {
                console.error("Image Gen Error:", error);
                el.aiLoadingState.classList.add('hidden');
                showModal('画像生成エラー', 'バッジの作成に失敗しました。', 'error');
            }
        });

        // App Initialization
        updateUI();

    </script>
</body>
</html>
