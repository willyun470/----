<html lang="ko" class="h-full bg-slate-100">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=5.0">
    <title>국립국어원 표준 한글 점자 및 좌우 반전 시뮬레이터</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Font Awesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Pretendard:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }
        body {
            font-family: 'Pretendard', system-ui, -apple-system, BlinkMacSystemFont, sans-serif;
            background-color: #f1f5f9;
            -webkit-tap-highlight-color: transparent;
        }
        /* Tactical Dot Visual Effects */
        .braille-dot {
            transition: all 0.15s ease-in-out;
        }
        .braille-dot.active {
            background-color: #4f46e5; /* Indigo 600 */
            box-shadow: inset 0 2px 4px rgba(0,0,0,0.3), 0 0 8px rgba(79, 70, 229, 0.5);
        }
        .braille-dot.inactive {
            background-color: #334155; /* Slate 700 */
        }
        .braille-dot-flipped.active {
            background-color: #10b981; /* Emerald 500 */
            box-shadow: inset 0 2px 4px rgba(0,0,0,0.3), 0 0 8px rgba(16, 185, 129, 0.5);
        }
        .braille-unicode-text {
            font-family: 'Segoe UI Symbol', 'Apple Symbols', 'Noto Sans Braille', monospace;
            line-height: 1.2;
        }
    </style>
</head>
<body class="min-h-screen text-slate-800 flex flex-col justify-between antialiased selection:bg-indigo-500 selection:text-white">

    <!-- Header Container -->
    <header class="bg-indigo-700 text-white shadow-md sticky top-0 z-30 w-full">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-3.5 flex flex-wrap justify-between items-center gap-3">
            <div class="flex items-center space-x-3">
                <div class="w-10 h-10 bg-white/10 rounded-xl flex items-center justify-center backdrop-blur-sm border border-white/20 shrink-0">
                    <i class="fa-solid fa-braille text-2xl text-indigo-200"></i>
                </div>
                <div>
                    <h1 class="font-bold text-base sm:text-lg md:text-xl leading-tight flex flex-wrap items-center gap-2">
                        <span>한글 점자 변환 & 좌우 반전 시뮬레이터</span>
                        <span class="bg-indigo-500/80 text-indigo-100 text-[10px] sm:text-xs px-2.5 py-0.5 rounded-full font-normal border border-indigo-300/30">국립국어원 표준 규정</span>
                    </h1>
                    <p class="text-xs text-indigo-200 hidden sm:block">한글·숫자·기호 실시간 점자 변환 및 점자판 천공용 좌우 반전 시뮬레이션</p>
                </div>
            </div>

            <div class="flex items-center gap-2 ml-auto sm:ml-0">
                <button onclick="openGuideModal()" class="px-3 py-1.5 bg-indigo-600 hover:bg-indigo-500 active:scale-95 text-xs font-semibold rounded-xl border border-indigo-400/40 transition-all flex items-center gap-1.5 shadow-sm">
                    <i class="fa-solid fa-circle-question text-indigo-200"></i>
                    <span>반전점자 원리 보기</span>
                </button>
            </div>
        </div>
    </header>

    <!-- Main Content Grid -->
    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-5 sm:py-8 flex-grow w-full space-y-6">

        <!-- Input Box Section -->
        <section class="bg-white rounded-2xl p-4 sm:p-6 shadow-sm border border-slate-200/80">
            <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-3 mb-3">
                <label for="text-input" class="text-sm sm:text-base font-bold text-slate-800 flex items-center gap-2">
                    <i class="fa-solid fa-pen-to-square text-indigo-600"></i>
                    <span>변환할 한글 문장 및 숫자 입력</span>
                </label>
                <!-- Quick Preset Sample Buttons -->
                <div class="flex flex-wrap items-center gap-1.5">
                    <span class="text-xs text-slate-400 font-medium mr-1 hidden md:inline">예시 문장:</span>
                    <button onclick="setSample('안녕하세요')" class="text-xs bg-slate-100 hover:bg-indigo-50 hover:text-indigo-600 px-2.5 py-1.5 rounded-lg transition-colors font-medium text-slate-600 active:scale-95">"안녕하세요"</button>
                    <button onclick="setSample('2026 과학대제전')" class="text-xs bg-slate-100 hover:bg-indigo-50 hover:text-indigo-600 px-2.5 py-1.5 rounded-lg transition-colors font-medium text-slate-600 active:scale-95">"2026 과학대제전"</button>
                    <button onclick="setSample('도서관 ㅠ 3층')" class="text-xs bg-slate-100 hover:bg-indigo-50 hover:text-indigo-600 px-2.5 py-1.5 rounded-lg transition-colors font-medium text-slate-600 active:scale-95">"도서관 ㅠ 3층"</button>
                    <button onclick="clearInput()" class="text-xs bg-rose-50 hover:bg-rose-100 text-rose-600 px-2.5 py-1.5 rounded-lg transition-colors font-semibold active:scale-95">초기화</button>
                </div>
            </div>

            <div class="relative">
                <textarea id="text-input" rows="3" oninput="convertText()" onkeyup="convertText()" placeholder="여기에 변환할 글자(한글, 숫자, 문장부호)를 입력하세요..." 
                    class="w-full p-3.5 text-slate-800 bg-slate-50 border border-slate-200 rounded-xl text-sm sm:text-base focus:outline-none focus:ring-2 focus:ring-indigo-500/30 focus:border-indigo-500 transition-all resize-none shadow-inner"></textarea>
                <div class="absolute bottom-3 right-3 text-xs text-slate-400 font-mono pointer-events-none bg-slate-50/80 px-1.5 py-0.5 rounded">
                    <span id="char-count" class="font-bold text-indigo-600">0</span> 자
                </div>
            </div>
        </section>

        <!-- Results Comparison Section Grid -->
        <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">

            <!-- 1. Normal Braille Output (Reading Mode) -->
            <section class="bg-white rounded-2xl p-4 sm:p-6 shadow-sm border border-indigo-100 flex flex-col justify-between">
                <div>
                    <!-- Section Title -->
                    <div class="flex justify-between items-center pb-3 border-b border-slate-100 mb-4">
                        <div class="flex items-center gap-2.5">
                            <span class="w-8 h-8 rounded-xl bg-indigo-100 text-indigo-600 flex items-center justify-center font-bold text-sm shrink-0">
                                <i class="fa-solid fa-eye"></i>
                            </span>
                            <div>
                                <h2 class="font-bold text-slate-800 text-sm sm:text-base">1. 정방향 점자 (읽기용 표준)</h2>
                                <p class="text-[11px] sm:text-xs text-slate-400">손끝 촉각이나 눈으로 읽는 표준 점자</p>
                            </div>
                        </div>
                        <button onclick="copyToClipboard('normal-braille-text')" class="px-2.5 py-1.5 text-xs font-semibold bg-indigo-50 text-indigo-600 hover:bg-indigo-100 rounded-xl transition-colors flex items-center gap-1 shrink-0 active:scale-95">
                            <i class="fa-regular fa-copy"></i>
                            <span>복사</span>
                        </button>
                    </div>

                    <!-- Unicode String Preview -->
                    <div class="mb-4">
                        <label class="block text-xs font-medium text-slate-500 mb-1">유니코드 점자 텍스트</label>
                        <div id="normal-braille-text" class="p-3 bg-slate-50 border border-slate-200 rounded-xl braille-unicode-text text-3xl tracking-widest text-indigo-900 min-h-[64px] break-words flex items-center overflow-x-auto">
                            <span class="text-slate-300 text-sm font-sans">입력 시 실시간 점자 표시</span>
                        </div>
                    </div>

                    <!-- Visual Cell Grid -->
                    <div>
                        <label class="block text-xs font-medium text-slate-500 mb-2">6점 점자 셀 상세 (좌: 1·2·3 / 우: 4·5·6)</label>
                        <div id="normal-braille-grid" class="flex flex-wrap gap-2.5 min-h-[110px] p-3 bg-slate-50/80 rounded-xl border border-slate-200/60 overflow-x-auto">
                            <div class="text-xs text-slate-400 m-auto text-center py-4">입력된 글자가 점자 셀로 분해되어 표시됩니다.</div>
                        </div>
                    </div>
                </div>

                <div class="mt-4 pt-3 border-t border-slate-100 flex items-center gap-2 text-[11px] sm:text-xs text-slate-500">
                    <i class="fa-solid fa-circle-info text-indigo-500 shrink-0"></i>
                    <span>왼쪽 열: 1, 2, 3번 점 | 오른쪽 열: 4, 5, 6번 점</span>
                </div>
            </section>

            <!-- 2. Horizontally Mirrored Braille Output (Stylus Writing Mode) -->
            <section class="bg-white rounded-2xl p-4 sm:p-6 shadow-sm border border-emerald-200/80 bg-emerald-50/10 flex flex-col justify-between">
                <div>
                    <!-- Section Title -->
                    <div class="flex justify-between items-center pb-3 border-b border-emerald-100 mb-4">
                        <div class="flex items-center gap-2.5">
                            <span class="w-8 h-8 rounded-xl bg-emerald-100 text-emerald-600 flex items-center justify-center font-bold text-sm shrink-0">
                                <i class="fa-solid fa-arrows-left-right-to-line"></i>
                            </span>
                            <div>
                                <h2 class="font-bold text-slate-800 text-sm sm:text-base flex items-center gap-1.5 flex-wrap">
                                    <span>2. 좌우 반전 점자 (점자판 천공용)</span>
                                    <span class="bg-emerald-100 text-emerald-700 text-[10px] px-2 py-0.5 rounded-full font-bold">쓰기용</span>
                                </h2>
                                <p class="text-[11px] sm:text-xs text-slate-400">점자판과 점필로 종이 뒷면에 찍을 때 사용하는 모양</p>
                            </div>
                        </div>
                        <button onclick="copyToClipboard('flipped-braille-text')" class="px-2.5 py-1.5 text-xs font-semibold bg-emerald-50 text-emerald-700 hover:bg-emerald-100 rounded-xl transition-colors flex items-center gap-1 shrink-0 active:scale-95">
                            <i class="fa-regular fa-copy"></i>
                            <span>복사</span>
                        </button>
                    </div>

                    <!-- Unicode String Preview -->
                    <div class="mb-4">
                        <label class="block text-xs font-medium text-slate-500 mb-1">유니코드 반전 점자 텍스트</label>
                        <div id="flipped-braille-text" class="p-3 bg-emerald-50/50 border border-emerald-200/60 rounded-xl braille-unicode-text text-3xl tracking-widest text-emerald-900 min-h-[64px] break-words flex items-center overflow-x-auto">
                            <span class="text-slate-300 text-sm font-sans">입력 시 좌우 반전 점자 표시</span>
                        </div>
                    </div>

                    <!-- Visual Cell Grid -->
                    <div>
                        <label class="block text-xs font-medium text-slate-500 mb-2">반전 6점 점자 셀 상세 (좌: 4·5·6 / 우: 1·2·3)</label>
                        <div id="flipped-braille-grid" class="flex flex-wrap gap-2.5 min-h-[110px] p-3 bg-emerald-50/30 rounded-xl border border-emerald-200/50 overflow-x-auto">
                            <div class="text-xs text-slate-400 m-auto text-center py-4">반전된 점자 셀이 표시됩니다.</div>
                        </div>
                    </div>
                </div>

                <div class="mt-4 pt-3 border-t border-emerald-100 flex items-center gap-2 text-[11px] sm:text-xs text-emerald-700">
                    <i class="fa-solid fa-lightbulb text-emerald-600 shrink-0"></i>
                    <span>종이 뒷면에서 찍은 후 종이를 뒤집으면 1번(정방향) 점자와 정확히 일치합니다.</span>
                </div>
            </section>

        </div>

        <!-- Braille Dot Structure Reference Guide -->
        <section class="bg-slate-800 text-white rounded-2xl p-4 sm:p-6 shadow-sm">
            <h3 class="font-bold text-sm sm:text-base mb-4 flex items-center gap-2">
                <i class="fa-solid fa-table-cells text-indigo-400"></i>
                <span>6점식 점자 기본 번호 배치 및 좌우 반전 구조 비교</span>
            </h3>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-4 text-xs">
                <!-- Standard Dots -->
                <div class="bg-slate-700/60 p-4 rounded-xl border border-slate-600/50 flex items-center justify-between gap-3">
                    <div>
                        <p class="font-bold text-indigo-300 text-sm mb-1.5">정방향 (읽기 / 볼록 나온 면)</p>
                        <p class="text-slate-300 leading-relaxed text-xs">
                            • 왼쪽 열 : <strong class="text-indigo-200">1번, 2번, 3번 점</strong><br>
                            • 오른쪽 열 : <strong class="text-indigo-200">4번, 5번, 6번 점</strong>
                        </p>
                    </div>
                    <div class="bg-slate-900 p-3 rounded-xl border border-slate-600 grid grid-cols-2 gap-2 text-center font-mono font-bold text-indigo-400 shrink-0">
                        <div class="w-6 h-6 rounded-full border border-indigo-400/40 flex items-center justify-center">1</div>
                        <div class="w-6 h-6 rounded-full border border-indigo-400/40 flex items-center justify-center">4</div>
                        <div class="w-6 h-6 rounded-full border border-indigo-400/40 flex items-center justify-center">2</div>
                        <div class="w-6 h-6 rounded-full border border-indigo-400/40 flex items-center justify-center">5</div>
                        <div class="w-6 h-6 rounded-full border border-indigo-400/40 flex items-center justify-center">3</div>
                        <div class="w-6 h-6 rounded-full border border-indigo-400/40 flex items-center justify-center">6</div>
                    </div>
                </div>

                <!-- Flipped Dots -->
                <div class="bg-slate-700/60 p-4 rounded-xl border border-slate-600/50 flex items-center justify-between gap-3">
                    <div>
                        <p class="font-bold text-emerald-300 text-sm mb-1.5">좌우 반전 (점자판 천공 / 오목한 면)</p>
                        <p class="text-slate-300 leading-relaxed text-xs">
                            • 왼쪽 열 : <strong class="text-emerald-400">4번, 5번, 6번 점</strong><br>
                            • 오른쪽 열 : <strong class="text-emerald-400">1번, 2번, 3번 점</strong>
                        </p>
                    </div>
                    <div class="bg-slate-900 p-3 rounded-xl border border-slate-600 grid grid-cols-2 gap-2 text-center font-mono font-bold text-emerald-400 shrink-0">
                        <div class="w-6 h-6 rounded-full border border-emerald-400/40 flex items-center justify-center">4</div>
                        <div class="w-6 h-6 rounded-full border border-emerald-400/40 flex items-center justify-center">1</div>
                        <div class="w-6 h-6 rounded-full border border-emerald-400/40 flex items-center justify-center">5</div>
                        <div class="w-6 h-6 rounded-full border border-emerald-400/40 flex items-center justify-center">2</div>
                        <div class="w-6 h-6 rounded-full border border-emerald-400/40 flex items-center justify-center">6</div>
                        <div class="w-6 h-6 rounded-full border border-emerald-400/40 flex items-center justify-center">3</div>
                    </div>
                </div>
            </div>
        </section>

    </main>

    <!-- Explanation Modal -->
    <div id="guide-modal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 hidden flex items-center justify-center p-4">
        <div class="bg-white rounded-3xl max-w-lg w-full p-5 sm:p-6 shadow-2xl border border-slate-100 relative max-h-[90vh] overflow-y-auto">
            <div class="flex items-center justify-between pb-3 border-b border-slate-100 mb-4">
                <div class="flex items-center gap-2">
                    <div class="w-9 h-9 bg-indigo-100 text-indigo-600 rounded-xl flex items-center justify-center font-bold shrink-0">
                        <i class="fa-solid fa-braille"></i>
                    </div>
                    <h3 class="font-bold text-slate-800 text-sm sm:text-base">점자 작성 시 좌우 반전 원리</h3>
                </div>
                <button onclick="closeGuideModal()" class="text-slate-400 hover:text-slate-600 w-8 h-8 rounded-full flex items-center justify-center hover:bg-slate-100 transition-colors">
                    <i class="fa-solid fa-xmark text-lg"></i>
                </button>
            </div>

            <div class="space-y-3 text-xs sm:text-sm text-slate-600 leading-relaxed">
                <p class="font-semibold text-slate-800">
                    왜 점자를 종이에 직접 새길 때는 좌우를 반전시켜야 할까요?
                </p>
                <p class="bg-indigo-50 p-3 rounded-xl border border-indigo-100 text-indigo-900">
                    점필(Stylus)로 종이를 눌러 점자를 새길 때는 **종이의 뒷면**에 구멍을 오목하게 누르는 방식이기 때문입니다.
                </p>
                <ul class="list-disc list-inside space-y-1.5 pl-1 text-slate-600">
                    <li>종이 뒷면에서 **오른쪽에서 왼쪽 방향**으로 점자를 찍어 나갑니다.</li>
                    <li>이때, **1번 점은 오른쪽 상단, 4번 점은 왼쪽 상단**으로 거울처럼 좌우를 뒤집어서 찍어야 합니다.</li>
                    <li>작성이 끝난 뒤 종이를 뒤집어 앞면(볼록 튀어나온 면)으로 보면 표준 **정방향 점자**가 됩니다.</li>
                </ul>
                <div class="p-3 bg-emerald-50 rounded-xl border border-emerald-200 text-emerald-800 text-xs">
                    💡 **활용 팁:** 점자판으로 종이에 직접 찍을 때에는 **'2. 좌우 반전 점자'**의 점형 배치를 보고 찍으시면 됩니다!
                </div>
            </div>

            <div class="mt-5 pt-3 border-t border-slate-100">
                <button onclick="closeGuideModal()" class="w-full py-2.5 bg-indigo-600 hover:bg-indigo-700 text-white font-bold rounded-xl text-xs shadow-md transition-all active:scale-95">
                    확인 및 닫기
                </button>
            </div>
        </div>
    </div>

    <!-- Toast Notification -->
    <div id="toast" class="fixed bottom-5 right-5 z-50 bg-slate-900 text-white px-4 py-2.5 rounded-2xl shadow-xl text-xs font-semibold hidden flex items-center gap-2 border border-slate-700">
        <i class="fa-solid fa-circle-check text-emerald-400"></i>
        <span id="toast-message">클립보드에 복사되었습니다.</span>
    </div>

    <!-- Footer -->
    <footer class="py-4 text-center text-xs text-slate-400 border-t border-slate-200 bg-white">
        <p class="px-4">국립국어원 표준 한글 점자 규정 기반 실시간 점자 변환 & 좌우 반전 시뮬레이터</p>
    </footer>

    <!-- JavaScript Application Logic -->
    <script>
        /*
         * Korean Standard Braille Engine (국립국어원 한국 점자 규정)
         * Dot Array Indexing: [Dot 1, Dot 2, Dot 3, Dot 4, Dot 5, Dot 6]
         */
        
        const CHO_LIST = ['ㄱ','ㄲ','ㄴ','ㄷ','ㄸ','ㄹ','ㅁ','ㅂ','ㅃ','ㅅ','ㅆ','ㅇ','ㅈ','ㅉ','ㅊ','ㅋ','ㅌ','ㅍ','ㅎ'];
        const JUNG_LIST = ['ㅏ','ㅐ','ㅑ','ㅒ','ㅓ','ㅔ','ㅕ','ㅖ','ㅗ','ㅘ','ㅙ','ㅚ','ㅛ','ㅜ','ㅝ','ㅞ','ㅟ','ㅠ','ㅡ','ㅢ','ㅣ'];
        const JONG_LIST = ['','ㄱ','ㄲ','ㄳ','ㄴ','ㄵ','ㄶ','ㄷ','ㄹ','ㄺ','ㄻ','ㄼ','ㄽ','ㄾ','ㄿ','ㅀ','ㅁ','ㅂ','ㅄ','ㅅ','ㅆ','ㅇ','ㅈ','ㅊ','ㅋ','ㅌ','ㅍ','ㅎ'];

        // Single Jamo definition table (used for reference)
        const SINGLE_JAMO_KEYS = ['ㄱ','ㄴ','ㄷ','ㄹ','ㅁ','ㅂ','ㅅ','ㅇ','ㅈ','ㅊ','ㅋ','ㅌ','ㅍ','ㅎ','ㄲ','ㄸ','ㅃ','ㅆ','ㅉ','ㅏ','ㅐ','ㅑ','ㅒ','ㅓ','ㅔ','ㅕ','ㅖ','ㅗ','ㅘ','ㅙ','ㅚ','ㅛ','ㅜ','ㅝ','ㅞ','ㅟ','ㅠ','ㅡ','ㅢ','ㅣ'];

        const BRAILLE_MAP = {
            CHO: {
                'ㄱ': [0,0,0,1,0,0], // 4
                'ㄴ': [1,0,0,1,0,0], // 1,4
                'ㄷ': [0,1,0,1,0,0], // 2,4
                'ㄹ': [0,0,0,0,1,0], // 5
                'ㅁ': [1,0,0,0,1,0], // 1,5
                'ㅂ': [0,0,0,1,1,0], // 4,5
                'ㅅ': [0,0,0,0,0,1], // 6
                'ㅇ': null,           // 초성 ㅇ 생략
                'ㅈ': [0,0,0,1,0,1], // 4,6
                'ㅊ': [0,0,0,0,1,1], // 5,6
                'ㅋ': [1,1,0,1,0,0], // 1,2,4
                'ㅌ': [1,1,0,0,1,0], // 1,2,5
                'ㅍ': [1,0,0,1,1,0], // 1,4,5
                'ㅎ': [0,1,0,1,1,0]  // 2,4,5
            },
            JUNG: {
                'ㅏ': [[1,1,0,0,0,1]], // 1,2,6
                'ㅐ': [[1,1,1,0,1,0]], // 1,2,3,5
                'ㅑ': [[0,0,1,1,1,0]], // 3,4,5
                'ㅒ': [[0,0,1,1,1,0], [1,1,1,0,1,0]],
                'ㅓ': [[0,1,1,1,0,0]], // 2,3,4
                'ㅔ': [[1,0,1,1,1,0]], // 1,3,4,5
                'ㅕ': [[1,0,0,0,1,1]], // 1,5,6
                'ㅖ': [[0,0,1,1,0,0]], // 3,4
                'ㅗ': [[1,0,1,0,0,1]], // 1,3,6
                'ㅘ': [[1,1,1,0,0,1]], // 1,2,3,6
                'ㅙ': [[1,1,1,0,0,1], [1,1,1,0,1,0]],
                'ㅚ': [[1,0,1,1,1,1]], // 1,3,4,5,6
                'ㅛ': [[0,0,1,1,0,1]], // 3,4,6
                'ㅜ': [[1,0,1,1,0,0]], // 1,3,4
                'ㅝ': [[1,1,1,1,0,0]], // 1,2,3,4
                'ㅞ': [[1,1,1,1,0,0], [1,1,1,0,1,0]],
                'ㅟ': [[1,0,1,1,0,0], [1,1,1,0,1,0]],
                'ㅠ': [[1,0,0,1,0,1]], // 1,4,6
                'ㅡ': [[0,1,0,1,0,1]], // 2,4,6
                'ㅢ': [[0,1,1,1,1,1]], // 2,3,4,5,6
                'ㅣ': [[1,0,1,0,1,0]]  // 1,3,5
            },
            JONG: {
                'ㄱ': [[1,0,0,0,0,0]], // 1
                'ㄴ': [[0,1,0,0,1,0]], // 2,5
                'ㄷ': [[0,0,1,0,1,0]], // 3,5
                'ㄹ': [[0,1,0,0,0,0]], // 2
                'ㅁ': [[0,1,0,0,0,1]], // 2,6
                'ㅂ': [[1,1,0,0,0,0]], // 1,2
                'ㅅ': [[0,0,1,0,0,0]], // 3
                'ㅇ': [[0,1,1,0,1,1]], // 2,3,5,6
                'ㅈ': [[1,0,1,0,0,0]], // 1,3
                'ㅊ': [[0,1,1,0,0,0]], // 2,3
                'ㅋ': [[0,1,1,0,1,0]], // 2,3,5
                'ㅌ': [[0,1,1,0,0,1]], // 2,3,6
                'ㅍ': [[0,1,0,0,1,1]], // 2,5,6
                'ㅎ': [[0,0,1,0,1,1]], // 3,5,6
                'ㄲ': [[1,0,0,0,0,0], [1,0,0,0,0,0]],
                'ㅆ': [[0,0,1,1,0,0]], // 3,4 (받침 ㅆ 약자)
                'ㄳ': [[1,0,0,0,0,0], [0,0,1,0,0,0]],
                'ㄵ': [[0,1,0,0,1,0], [1,0,1,0,0,0]],
                'ㄶ': [[0,1,0,0,1,0], [0,0,1,0,1,1]],
                'ㄺ': [[0,1,0,0,0,0], [1,0,0,0,0,0]],
                'ㄻ': [[0,1,0,0,0,0], [0,1,0,0,0,1]],
                'ㄼ': [[0,1,0,0,0,0], [1,1,0,0,0,0]],
                'ㄽ': [[0,1,0,0,0,0], [0,0,1,0,0,0]],
                'ㄾ': [[0,1,0,0,0,0], [0,1,1,0,0,1]],
                'ㄿ': [[0,1,0,0,0,0], [0,1,0,0,1,1]],
                'ㅀ': [[0,1,0,0,0,0], [0,0,1,0,1,1]],
                'ㅄ': [[1,1,0,0,0,0], [0,0,1,0,0,0]]
            },
            // Syllable contractions (JUNG + JONG)
            SYLLABLE_CONTRACT: {
                'ㅓㄱ': { label: '억(약자)', dots: [1,0,0,1,1,1] }, // 1,4,5,6
                'ㅓㄴ': { label: '언(약자)', dots: [0,1,1,1,1,1] }, // 2,3,4,5,6
                'ㅓㄹ': { label: '얼(약자)', dots: [0,1,1,1,1,0] }, // 2,3,4,5
                'ㅕㄴ': { label: '연(약자)', dots: [1,0,0,0,0,1] }, // 1,6
                'ㅕㄹ': { label: '열(약자)', dots: [1,1,0,0,1,1] }, // 1,2,5,6
                'ㅕㅇ': { label: '영(약자)', dots: [1,1,0,1,1,1] }, // 1,2,4,5,6
                'ㅗㄱ': { label: '옥(약자)', dots: [1,0,1,1,0,1] }, // 1,3,4,6
                'ㅗㄴ': { label: '온(약자)', dots: [1,1,1,0,1,1] }, // 1,2,3,5,6
                'ㅗㅇ': { label: '옹(약자)', dots: [1,1,1,1,1,1] }, // 1,2,3,4,5,6
                'ㅜㄴ': { label: '운(약자)', dots: [1,1,0,1,1,0] }, // 1,2,4,5
                'ㅜㄹ': { label: '울(약자)', dots: [1,1,1,1,0,1] }, // 1,2,3,4,6
                'ㅡㄴ': { label: '은(약자)', dots: [1,0,1,0,1,1] }, // 1,3,5,6
                'ㅡㄹ': { label: '을(약자)', dots: [0,1,1,1,0,1] }, // 2,3,4,6
                'ㅣㄴ': { label: '인(약자)', dots: [1,1,1,1,1,0] }  // 1,2,3,4,5
            },
            NUMBERS: {
                '1': [1,0,0,0,0,0], '2': [1,1,0,0,0,0], '3': [1,0,0,1,0,0],
                '4': [1,0,0,1,1,0], '5': [1,0,0,0,1,0], '6': [1,1,0,1,0,0],
                '7': [1,1,0,1,1,0], '8': [1,1,0,0,1,0], '9': [0,1,0,1,0,0],
                '0': [0,1,0,1,1,0]
            },
            PUNCTUATION: {
                '.': { label: '마침표', dots: [0,1,0,0,1,1] }, // 2,5,6
                ',': { label: '쉼표', dots: [0,0,0,0,1,0] },   // 5
                '?': { label: '물음표', dots: [0,1,1,0,0,1] }, // 2,3,6
                '!': { label: '느낌표', dots: [0,1,1,0,1,0] }, // 2,3,5
                '-': { label: '붙임표', dots: [0,0,1,0,0,1] }, // 3,6
                '~': { label: '물결표', dots: [0,0,1,0,0,1] },
                ':': { label: '쌍점', dots: [0,1,0,0,1,0] },
                ';': { label: '쌍점', dots: [0,1,1,0,1,0] }
            }
        };

        const NUMBER_PREFIX = [0,0,1,1,1,1]; // 수표 ⠼ (3,4,5,6)

        // Convert 6-dot array to Unicode Braille Pattern Character (U+2800 ~ U+283F)
        function dotsToUnicode(dots) {
            if (!dots || !Array.isArray(dots)) return ' ';
            let value = 0;
            if (dots[0]) value |= 0x01; // Dot 1
            if (dots[1]) value |= 0x02; // Dot 2
            if (dots[2]) value |= 0x04; // Dot 3
            if (dots[3]) value |= 0x08; // Dot 4
            if (dots[4]) value |= 0x10; // Dot 5
            if (dots[5]) value |= 0x20; // Dot 6
            return String.fromCharCode(0x2800 + value);
        }

        // Mirror Horizontally: Left Col (1,2,3) <-> Right Col (4,5,6)
        function flipDots(dots) {
            if (!dots || !Array.isArray(dots)) return [0,0,0,0,0,0];
            return [
                dots[3], // New Dot 1 = Old Dot 4
                dots[4], // New Dot 2 = Old Dot 5
                dots[5], // New Dot 3 = Old Dot 6
                dots[0], // New Dot 4 = Old Dot 1
                dots[1], // New Dot 5 = Old Dot 2
                dots[2]  // New Dot 6 = Old Dot 3
            ];
        }

        // Standard Korean Text to Braille Translator Engine
        function textToBrailleCells(text) {
            const cells = [];
            let inNumberMode = false;

            for (let i = 0; i < text.length; i++) {
                const char = text[i];
                const code = char.charCodeAt(0);

                // 1. Space & Newlines
                if (char === ' ' || char === '\n' || char === '\t') {
                    inNumberMode = false;
                    cells.push({ label: char === '\n' ? '줄바꿈' : '공백', dots: [0,0,0,0,0,0] });
                    continue;
                }

                // 2. Numbers (0-9)
                if (char >= '0' && char <= '9') {
                    if (!inNumberMode) {
                        cells.push({ label: '수표(⠼)', dots: NUMBER_PREFIX });
                        inNumberMode = true;
                    }
                    cells.push({ label: char, dots: BRAILLE_MAP.NUMBERS[char] });
                    continue;
                } else {
                    inNumberMode = false;
                }

                // 3. Korean Syllables (가 - 힣)
                if (code >= 0xAC00 && code <= 0xD7A3) {
                    const sylIndex = code - 0xAC00;
                    const choIdx = Math.floor(sylIndex / 588);
                    const jungIdx = Math.floor((sylIndex % 588) / 28);
                    const jongIdx = sylIndex % 28;

                    const choChar = CHO_LIST[choIdx];
                    const jungChar = JUNG_LIST[jungIdx];
                    const jongChar = JONG_LIST[jongIdx];

                    // Check Syllable Special Word Contractions
                    if (char === '가') {
                        cells.push({ label: '가(약자)', dots: [1,1,0,1,0,1] }); // 1,2,4,6
                        continue;
                    }
                    if (char === '사') {
                        cells.push({ label: '사(약자)', dots: [1,1,1,0,0,0] }); // 1,2,3
                        continue;
                    }
                    if (char === '것') {
                        cells.push({ label: '것(약자1)', dots: [0,0,0,1,1,1] }); // 4,5,6
                        cells.push({ label: '것(약자2)', dots: [0,1,1,1,0,0] }); // 2,3,4
                        continue;
                    }

                    // Process Initial Consonant (초성)
                    const isDouble = ['ㄲ','ㄸ','ㅃ','ㅆ','ㅉ'].includes(choChar);
                    let baseCho = choChar;
                    if (isDouble) {
                        cells.push({ label: '된소리(⠠)', dots: [0,0,0,0,0,1] });
                        if (choChar === 'ㄲ') baseCho = 'ㄱ';
                        if (choChar === 'ㄸ') baseCho = 'ㄷ';
                        if (choChar === 'ㅃ') baseCho = 'ㅂ';
                        if (choChar === 'ㅆ') baseCho = 'ㅅ';
                        if (choChar === 'ㅉ') baseCho = 'ㅈ';
                    }

                    // '나, 다, 마, 바, 자, 카, 타, 파, 하' - omit 'ㅏ' ONLY when NO final consonant (종성 없음)
                    const isOmitA = ['ㄴ','ㄷ','ㅁ','ㅂ','ㅈ','ㅋ','ㅌ','ㅍ','ㅎ'].includes(baseCho) && jungChar === 'ㅏ' && jongIdx === 0;

                    if (baseCho !== 'ㅇ') {
                        const choDots = BRAILLE_MAP.CHO[baseCho];
                        if (choDots) {
                            cells.push({ label: isOmitA ? `${baseCho}(약자)` : baseCho, dots: choDots });
                        }
                    }

                    // Process Vowel & Final Consonant (중성 & 종성 약자 검사)
                    const jjPair = jungChar + jongChar;
                    const contract = BRAILLE_MAP.SYLLABLE_CONTRACT[jjPair];

                    if (contract) {
                        cells.push(contract);
                    } else {
                        // Vowel
                        if (!isOmitA) {
                            const jungDotsArr = BRAILLE_MAP.JUNG[jungChar];
                            if (jungDotsArr) {
                                jungDotsArr.forEach(d => cells.push({ label: jungChar, dots: d }));
                            }
                        }
                        // Final Consonant
                        if (jongChar && BRAILLE_MAP.JONG[jongChar]) {
                            const jongDotsArr = BRAILLE_MAP.JONG[jongChar];
                            jongDotsArr.forEach(d => cells.push({ label: jongChar, dots: d }));
                        }
                    }
                    continue;
                }

                // 4. Single Korean Jamo (단독 자음/모음) - 국립국어원 표준 점자 정식 변환
                if (SINGLE_JAMO_KEYS.includes(char)) {
                    // 단독 모음 변환 (예: ㅠ -> 1, 4, 6번 점)
                    if (JUNG_LIST.includes(char)) {
                        const jungDotsArr = BRAILLE_MAP.JUNG[char];
                        if (jungDotsArr) {
                            jungDotsArr.forEach(d => cells.push({ label: char, dots: d }));
                        }
                    } 
                    // 단독 자음 변환 (초성 점형 및 된소리 규정 적용)
                    else {
                        const isDouble = ['ㄲ','ㄸ','ㅃ','ㅆ','ㅉ'].includes(char);
                        let baseCho = char;
                        if (isDouble) {
                            cells.push({ label: '된소리(⠠)', dots: [0,0,0,0,0,1] });
                            if (char === 'ㄲ') baseCho = 'ㄱ';
                            if (char === 'ㄸ') baseCho = 'ㄷ';
                            if (char === 'ㅃ') baseCho = 'ㅂ';
                            if (char === 'ㅆ') baseCho = 'ㅅ';
                            if (char === 'ㅉ') baseCho = 'ㅈ';
                        }

                        if (baseCho === 'ㅇ') {
                            cells.push({ label: 'ㅇ(생략)', dots: [0,0,0,0,0,0] });
                        } else if (BRAILLE_MAP.CHO[baseCho]) {
                            cells.push({ label: baseCho, dots: BRAILLE_MAP.CHO[baseCho] });
                        }
                    }
                    continue;
                }

                // 5. Punctuation
                if (BRAILLE_MAP.PUNCTUATION[char]) {
                    cells.push(BRAILLE_MAP.PUNCTUATION[char]);
                    continue;
                }

                // Fallback for space/other unmapped chars
                cells.push({ label: char, dots: [0,0,0,0,0,0] });
            }

            return cells;
        }

        function convertText() {
            try {
                const inputVal = document.getElementById('text-input').value;
                document.getElementById('char-count').innerText = inputVal.length;

                const cells = textToBrailleCells(inputVal);

                let normalUnicode = '';
                let flippedUnicode = '';

                const normalGrid = document.getElementById('normal-braille-grid');
                const flippedGrid = document.getElementById('flipped-braille-grid');

                normalGrid.innerHTML = '';
                flippedGrid.innerHTML = '';

                if (cells.length === 0) {
                    document.getElementById('normal-braille-text').innerHTML = '<span class="text-slate-300 text-sm font-sans">입력 시 실시간 점자 표시</span>';
                    document.getElementById('flipped-braille-text').innerHTML = '<span class="text-slate-300 text-sm font-sans">입력 시 좌우 반전 점자 표시</span>';
                    normalGrid.innerHTML = '<div class="text-xs text-slate-400 m-auto text-center py-4">입력된 글자가 점자 셀로 분해되어 표시됩니다.</div>';
                    flippedGrid.innerHTML = '<div class="text-xs text-slate-400 m-auto text-center py-4">반전된 점자 셀이 표시됩니다.</div>';
                    return;
                }

                cells.forEach(cell => {
                    const normDots = cell.dots || [0,0,0,0,0,0];
                    const flipDotsArr = flipDots(normDots);

                    normalUnicode += dotsToUnicode(normDots);
                    flippedUnicode += dotsToUnicode(flipDotsArr);

                    normalGrid.appendChild(createCellCard(cell.label, normDots, false));
                    flippedGrid.appendChild(createCellCard(cell.label, flipDotsArr, true));
                });

                document.getElementById('normal-braille-text').innerText = normalUnicode;
                document.getElementById('flipped-braille-text').innerText = flippedUnicode;
            } catch (err) {
                console.error("점자 변환 에러:", err);
            }
        }

        // Generate Single Visual 6-Dot Cell Card
        function createCellCard(label, dots, isFlipped) {
            const card = document.createElement('div');
            card.className = `flex flex-col items-center p-2 rounded-xl border ${isFlipped ? 'bg-emerald-50/80 border-emerald-200' : 'bg-white border-slate-200'} shadow-2xs shrink-0 transition-all hover:scale-105`;

            const lbl = document.createElement('span');
            lbl.className = `text-[11px] font-bold mb-1.5 ${isFlipped ? 'text-emerald-800' : 'text-slate-700'}`;
            lbl.innerText = label || '';

            const dotMatrix = document.createElement('div');
            dotMatrix.className = "grid grid-cols-2 gap-1.5 p-1.5 bg-slate-900 rounded-lg";

            const dotIndices = [0, 3, 1, 4, 2, 5];

            dotIndices.forEach(idx => {
                const dot = document.createElement('div');
                const isActive = dots && dots[idx] === 1;

                dot.className = `w-3 h-3 rounded-full braille-dot ${
                    isActive 
                        ? (isFlipped ? 'braille-dot-flipped active' : 'active') 
                        : 'inactive'
                }`;
                dotMatrix.appendChild(dot);
            });

            card.appendChild(lbl);
            card.appendChild(dotMatrix);
            return card;
        }

        // Preset Sample Button Handler
        function setSample(text) {
            document.getElementById('text-input').value = text;
            convertText();
        }

        // Clear Handler
        function clearInput() {
            document.getElementById('text-input').value = '';
            convertText();
        }

        // Copy to Clipboard
        function copyToClipboard(elementId) {
            const el = document.getElementById(elementId);
            const text = el.innerText;

            if (!text || text.includes('입력 시')) return;

            const textarea = document.createElement('textarea');
            textarea.value = text;
            textarea.style.position = 'fixed';
            textarea.style.opacity = '0';
            document.body.appendChild(textarea);
            textarea.select();
            
            try {
                document.execCommand('copy');
                showToast("점자 텍스트가 클립보드에 복사되었습니다!");
            } catch (err) {
                console.error("복사 실패:", err);
            } finally {
                document.body.removeChild(textarea);
            }
        }

        // Toast Helper
        function showToast(msg) {
            const toast = document.getElementById('toast');
            document.getElementById('toast-message').innerText = msg;
            toast.classList.remove('hidden');

            setTimeout(() => {
                toast.classList.add('hidden');
            }, 2500);
        }

        // Modal Handlers
        function openGuideModal() {
            document.getElementById('guide-modal').classList.remove('hidden');
        }

        function closeGuideModal() {
            document.getElementById('guide-modal').classList.add('hidden');
        }

        // Initial Load Event
        window.addEventListener('DOMContentLoaded', () => {
            setSample('안녕하세요');
        });
    </script>
</body>
</html>
