<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>실시간 점자 변환기 및 좌우 반전 시스템</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Font Awesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Pretendard:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Pretendard', system-ui, -apple-system, sans-serif;
            background-color: #f1f5f9;
        }
        /* Custom Tactile Dot Effect */
        .braille-dot {
            transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .braille-dot.active {
            background-color: #4f46e5; /* Indigo 600 */
            box-shadow: inset 0 2px 4px rgba(0,0,0,0.3), 0 0 8px rgba(79, 70, 229, 0.5);
        }
        .braille-dot.inactive {
            background-color: #e2e8f0; /* Slate 200 */
        }
        .braille-dot-flipped.active {
            background-color: #059669; /* Emerald 600 */
            box-shadow: inset 0 2px 4px rgba(0,0,0,0.3), 0 0 8px rgba(5, 150, 105, 0.5);
        }
    </style>
</head>
<body class="min-h-screen text-slate-800 flex flex-col justify-between antialiased">

    <!-- Header Container -->
    <header class="bg-indigo-700 text-white shadow-lg sticky top-0 z-30">
        <div class="max-w-6xl mx-auto px-4 py-4 flex flex-wrap justify-between items-center gap-3">
            <div class="flex items-center space-x-3">
                <div class="w-10 h-10 bg-white/10 rounded-xl flex items-center justify-center backdrop-blur-sm border border-white/20">
                    <i class="fa-solid fa-braille text-2xl text-indigo-200"></i>
                </div>
                <div>
                    <h1 class="font-bold text-lg sm:text-xl leading-tight flex items-center gap-2">
                        점자 변환 & 좌우 반전 시뮬레이터
                        <span class="bg-indigo-500 text-[10px] sm:text-xs px-2 py-0.5 rounded-full font-normal border border-indigo-300/30">한글/영문/숫자</span>
                    </h1>
                    <p class="text-xs text-indigo-200">점자 읽기용 정방향 & 점자판 쓰기용 좌우 반전 실시간 변환</p>
                </div>
            </div>

            <div class="flex items-center gap-2">
                <button onclick="openGuideModal()" class="px-3 py-1.5 bg-indigo-600 hover:bg-indigo-500 active:scale-95 text-xs font-semibold rounded-xl border border-indigo-400/30 transition-all flex items-center gap-1.5">
                    <i class="fa-solid fa-circle-question"></i>
                    <span>반전점자란?</span>
                </button>
            </div>
        </div>
    </header>

    <!-- Main Content -->
    <main class="max-w-6xl mx-auto px-4 py-6 flex-grow w-full space-y-6">

        <!-- Input Box Section -->
        <section class="bg-white rounded-2xl p-5 shadow-sm border border-slate-200/80">
            <div class="flex justify-between items-center mb-3">
                <label for="text-input" class="text-sm font-bold text-slate-700 flex items-center gap-2">
                    <i class="fa-solid fa-pen-to-square text-indigo-600"></i>
                    변환할 문장 입력
                </label>
                <div class="flex gap-2">
                    <button onclick="setSample('안녕하세요')" class="text-xs bg-slate-100 hover:bg-indigo-50 hover:text-indigo-600 px-2.5 py-1 rounded-lg transition-colors font-medium text-slate-600">"안녕하세요"</button>
                    <button onclick="setSample('2026 과학대제전')" class="text-xs bg-slate-100 hover:bg-indigo-50 hover:text-indigo-600 px-2.5 py-1 rounded-lg transition-colors font-medium text-slate-600">"2026 과학대제전"</button>
                    <button onclick="setSample('Hello World')" class="text-xs bg-slate-100 hover:bg-indigo-50 hover:text-indigo-600 px-2.5 py-1 rounded-lg transition-colors font-medium text-slate-600">"Hello World"</button>
                    <button onclick="clearInput()" class="text-xs bg-rose-50 hover:bg-rose-100 text-rose-600 px-2 py-1 rounded-lg transition-colors font-medium">Clear</button>
                </div>
            </div>

            <div class="relative">
                <textarea id="text-input" rows="3" oninput="convertText()" placeholder="여기에 변환할 글자(한글, 영어, 숫자 등)를 입력하세요..." 
                    class="w-full p-3.5 text-slate-800 bg-slate-50 border border-slate-200 rounded-xl text-sm sm:text-base focus:outline-none focus:ring-2 focus:ring-indigo-500/30 focus:border-indigo-500 transition-all resize-none"></textarea>
                <div class="absolute bottom-3 right-3 text-xs text-slate-400 font-mono">
                    <span id="char-count">0</span> 자
                </div>
            </div>
        </section>

        <!-- Results Comparison Section Grid -->
        <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">

            <!-- 1. Normal Braille Output (Reading Mode) -->
            <section class="bg-white rounded-2xl p-5 shadow-sm border border-slate-200/80 flex flex-col justify-between">
                <div>
                    <!-- Section Title -->
                    <div class="flex justify-between items-center pb-3 border-b border-slate-100 mb-4">
                        <div class="flex items-center gap-2">
                            <span class="w-8 h-8 rounded-xl bg-indigo-100 text-indigo-600 flex items-center justify-center font-bold text-xs">
                                <i class="fa-solid fa-eye"></i>
                            </span>
                            <div>
                                <h2 class="font-bold text-slate-800 text-base">1. 정방향 점자 (읽기용)</h2>
                                <p class="text-[11px] text-slate-400">눈으로 보거나 손가락 tactile 로 읽는 표준 점자</p>
                            </div>
                        </div>
                        <button onclick="copyToClipboard('normal-braille-text')" class="px-2.5 py-1.5 text-xs font-semibold bg-indigo-50 text-indigo-600 hover:bg-indigo-100 rounded-xl transition-colors flex items-center gap-1">
                            <i class="fa-regular fa-copy"></i>
                            <span>복사</span>
                        </button>
                    </div>

                    <!-- Unicode String Preview -->
                    <div class="mb-4">
                        <label class="block text-xs font-medium text-slate-500 mb-1">유니코드 점자 텍스트</label>
                        <div id="normal-braille-text" class="p-3 bg-slate-50 border border-slate-200 rounded-xl font-mono text-2xl tracking-widest text-indigo-900 min-h-[56px] break-words flex items-center">
                            <span class="text-slate-300 text-sm font-sans">입력 시 실시간 점자 표시</span>
                        </div>
                    </div>

                    <!-- Visual Cell Grid -->
                    <div>
                        <label class="block text-xs font-medium text-slate-500 mb-2">6점 점자 cell 상세 미리보기 (좌: 1·2·3 / 우: 4·5·6)</label>
                        <div id="normal-braille-grid" class="flex flex-wrap gap-2.5 min-h-[100px] p-2 bg-slate-50/50 rounded-xl border border-slate-100 overflow-x-auto">
                            <div class="text-xs text-slate-400 m-auto">입력된 글자가 점자 셀로 분해되어 표시됩니다.</div>
                        </div>
                    </div>
                </div>

                <div class="mt-4 pt-3 border-t border-slate-100 flex items-center gap-2 text-[11px] text-slate-500">
                    <i class="fa-solid fa-circle-info text-indigo-500"></i>
                    <span>왼쪽 열: 1, 2, 3번 점 | 오른쪽 열: 4, 5, 6번 점</span>
                </div>
            </section>

            <!-- 2. Horizontally Mirrored Braille Output (Stylus Writing Mode) -->
            <section class="bg-white rounded-2xl p-5 shadow-sm border border-emerald-200/80 bg-emerald-50/10 flex flex-col justify-between">
                <div>
                    <!-- Section Title -->
                    <div class="flex justify-between items-center pb-3 border-b border-emerald-100 mb-4">
                        <div class="flex items-center gap-2">
                            <span class="w-8 h-8 rounded-xl bg-emerald-100 text-emerald-600 flex items-center justify-center font-bold text-xs">
                                <i class="fa-solid fa-arrows-left-right-to-line"></i>
                            </span>
                            <div>
                                <h2 class="font-bold text-slate-800 text-base flex items-center gap-1.5">
                                    2. 좌우 반전 점자 (점자판 천공용)
                                    <span class="bg-emerald-100 text-emerald-700 text-[10px] px-2 py-0.5 rounded-full font-bold">쓰기용</span>
                                </h2>
                                <p class="text-[11px] text-slate-400">점자판과 점필로 종이 뒷면에 찍을 때 사용하는 모양</p>
                            </div>
                        </div>
                        <button onclick="copyToClipboard('flipped-braille-text')" class="px-2.5 py-1.5 text-xs font-semibold bg-emerald-50 text-emerald-700 hover:bg-emerald-100 rounded-xl transition-colors flex items-center gap-1">
                            <i class="fa-regular fa-copy"></i>
                            <span>복사</span>
                        </button>
                    </div>

                    <!-- Unicode String Preview -->
                    <div class="mb-4">
                        <label class="block text-xs font-medium text-slate-500 mb-1">유니코드 반전 점자 텍스트</label>
                        <div id="flipped-braille-text" class="p-3 bg-emerald-50/50 border border-emerald-200/60 rounded-xl font-mono text-2xl tracking-widest text-emerald-900 min-h-[56px] break-words flex items-center">
                            <span class="text-slate-300 text-sm font-sans">입력 시 좌우 반전 점자 표시</span>
                        </div>
                    </div>

                    <!-- Visual Cell Grid -->
                    <div>
                        <label class="block text-xs font-medium text-slate-500 mb-2">반전 6점 점자 cell 상세 (좌: 4·5·6 / 우: 1·2·3)</label>
                        <div id="flipped-braille-grid" class="flex flex-wrap gap-2.5 min-h-[100px] p-2 bg-emerald-50/20 rounded-xl border border-emerald-100/50 overflow-x-auto">
                            <div class="text-xs text-slate-400 m-auto">반전된 점자 셀이 표시됩니다.</div>
                        </div>
                    </div>
                </div>

                <div class="mt-4 pt-3 border-t border-emerald-100 flex items-center gap-2 text-[11px] text-emerald-700">
                    <i class="fa-solid fa-lightbulb text-emerald-600"></i>
                    <span>종이 뒤에서 찍은 후 뒤집으면 1번(정방향) 점자와 정확히 일치하게 됩니다!</span>
                </div>
            </section>

        </div>

        <!-- Braille Dot Structure Reference Guide -->
        <section class="bg-slate-800 text-white rounded-2xl p-5 shadow-sm">
            <h3 class="font-bold text-sm sm:text-base mb-3 flex items-center gap-2">
                <i class="fa-solid fa-table-cells text-indigo-400"></i>
                6점식 점자 기본 번호 배치 및 좌우 반전 구조
            </h3>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-4 text-xs">
                <!-- Standard Dots -->
                <div class="bg-slate-700/60 p-4 rounded-xl border border-slate-600/50 flex items-center justify-between">
                    <div>
                        <p class="font-bold text-indigo-300 text-sm mb-1">정방향 (읽기 / 볼록 나온 면)</p>
                        <p class="text-slate-300 leading-relaxed">
                            • 왼쪽 열 : 1번, 2번, 3번 점<br>
                            • 오른쪽 열 : 4번, 5번, 6번 점
                        </p>
                    </div>
                    <div class="bg-slate-900 p-3 rounded-xl border border-slate-600 grid grid-cols-2 gap-2 text-center font-mono font-bold text-indigo-400">
                        <div class="w-6 h-6 rounded-full border border-indigo-400/40 flex items-center justify-center">1</div>
                        <div class="w-6 h-6 rounded-full border border-indigo-400/40 flex items-center justify-center">4</div>
                        <div class="w-6 h-6 rounded-full border border-indigo-400/40 flex items-center justify-center">2</div>
                        <div class="w-6 h-6 rounded-full border border-indigo-400/40 flex items-center justify-center">5</div>
                        <div class="w-6 h-6 rounded-full border border-indigo-400/40 flex items-center justify-center">3</div>
                        <div class="w-6 h-6 rounded-full border border-indigo-400/40 flex items-center justify-center">6</div>
                    </div>
                </div>

                <!-- Flipped Dots -->
                <div class="bg-slate-700/60 p-4 rounded-xl border border-slate-600/50 flex items-center justify-between">
                    <div>
                        <p class="font-bold text-emerald-300 text-sm mb-1">좌우 반전 (점자판 천공 / 오목한 면)</p>
                        <p class="text-slate-300 leading-relaxed">
                            • 왼쪽 열 : <strong class="text-emerald-400">4번, 5번, 6번 점</strong><br>
                            • 오른쪽 열 : <strong class="text-emerald-400">1번, 2번, 3번 점</strong>
                        </p>
                    </div>
                    <div class="bg-slate-900 p-3 rounded-xl border border-slate-600 grid grid-cols-2 gap-2 text-center font-mono font-bold text-emerald-400">
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
        <div class="bg-white rounded-3xl max-w-lg w-full p-6 shadow-2xl border border-slate-100 animate-fade-in relative max-h-[90vh] overflow-y-auto">
            <div class="flex items-center justify-between pb-3 border-b border-slate-100 mb-4">
                <div class="flex items-center gap-2">
                    <div class="w-9 h-9 bg-indigo-100 text-indigo-600 rounded-xl flex items-center justify-center font-bold">
                        <i class="fa-solid fa-braille"></i>
                    </div>
                    <h3 class="font-bold text-slate-800 text-base">점자판 작성을 위한 좌우 반전 원리</h3>
                </div>
                <button onclick="closeGuideModal()" class="text-slate-400 hover:text-slate-600 w-8 h-8 rounded-full flex items-center justify-center hover:bg-slate-100">
                    <i class="fa-solid fa-xmark text-lg"></i>
                </button>
            </div>

            <div class="space-y-3 text-xs sm:text-sm text-slate-600 leading-relaxed">
                <p>
                    <strong>왜 점자를 쓸 때 좌우를 반전시켜야 할까요?</strong>
                </p>
                <p class="bg-indigo-50 p-3 rounded-xl border border-indigo-100 text-indigo-900">
                    점필(Stylus)로 종이를 눌러 점자를 새길 때는 **종이의 뒷면**에 구멍을 오목하게 뚫는 방식입니다.
                </p>
                <ul class="list-disc list-inside space-y-1.5 pl-1">
                    <li>종이 뒷면에서 오른쪽에서 왼쪽 방향으로 점자를 찍어 나갑니다.</li>
                    <li>이때, **1번 점은 오른쪽, 4번 점은 왼쪽**에 위치하도록 거울처럼 좌우를 뒤집어서 찍어야 합니다.</li>
                    <li>작성이 끝난 뒤 종이를 돌려 앞면(볼록 튀어나온 면)으로 보면, 원래 읽기용 **정방향 점자**가 됩니다!</li>
                </ul>
                <div class="p-3 bg-amber-50 rounded-xl border border-amber-200 text-amber-800 text-xs">
                    💡 **본 프로그램 활용법:** 점자판으로 종이에 직접 구멍을 뚫을 때는 **'2. 좌우 반전 점자'**의 셀 모양을 보고 오른쪽 열부터 차례로 찍어주시면 됩니다.
                </div>
            </div>

            <div class="mt-5 pt-3 border-t border-slate-100">
                <button onclick="closeGuideModal()" class="w-full py-2.5 bg-indigo-600 hover:bg-indigo-700 text-white font-bold rounded-xl text-xs shadow-md transition-all">
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
    <footer class="py-4 text-center text-xs text-slate-400 border-t border-slate-200/80 bg-white">
        <p>실시간 한글·영문·숫자 점자 변환기 및 좌우 반전 시뮬레이터</p>
    </footer>

    <!-- JavaScript Application Logic -->
    <script>
        /*
          Korean Braille Mapping Engine (6-dot Standard)
          Dots Array Format: [dot1, dot2, dot3, dot4, dot5, dot6] (1: active, 0: inactive)
        */

        // Initial Consonants (초성)
        const CHO_SEONG = {
            'ㄱ': [0, 0, 0, 1, 0, 0], // 4
            'ㄴ': [1, 0, 0, 1, 0, 0], // 1,4
            'ㄷ': [0, 1, 0, 1, 0, 0], // 2,4
            'ㄹ': [0, 0, 0, 0, 1, 0], // 5
            'ㅁ': [1, 0, 0, 0, 1, 0], // 1,5
            'ㅂ': [0, 0, 0, 1, 1, 0], // 4,5
            'ㅅ': [0, 0, 0, 0, 0, 1], // 6
            'ㅇ': null,               // 초성 ㅇ은 생략
            'ㅈ': [0, 0, 0, 1, 0, 1], // 4,6
            'ㅊ': [0, 0, 0, 0, 1, 1], // 5,6
            'ㅋ': [1, 1, 0, 1, 0, 0], // 1,2,4
            'ㅌ': [1, 1, 0, 0, 1, 0], // 1,2,5
            'ㅍ': [1, 0, 0, 1, 1, 0], // 1,4,5
            'ㅎ': [0, 1, 0, 1, 1, 0], // 2,4,5
            'ㄲ': 'DOUBLE_ㄱ',
            'ㄸ': 'DOUBLE_ㄷ',
            'ㅃ': 'DOUBLE_ㅂ',
            'ㅆ': 'DOUBLE_ㅅ',
            'ㅉ': 'DOUBLE_ㅈ'
        };

        // Vowels (중성)
        const JUNG_SEONG = {
            'ㅏ': [1, 1, 0, 0, 0, 1], // 1,2,6
            'ㅐ': [1, 1, 1, 0, 1, 0], // 1,2,3,5
            'ㅑ': [0, 0, 1, 1, 1, 0], // 3,4,5
            'ㅒ': [[0, 0, 1, 1, 1, 0], [1, 1, 1, 0, 1, 0]],
            'ㅓ': [0, 1, 1, 1, 0, 0], // 2,3,4
            'ㅔ': [1, 0, 1, 1, 1, 0], // 1,3,4,5
            '여': [1, 0, 0, 0, 1, 1], // 1,5,6
            '예': [0, 0, 1, 1, 0, 0], // 3,4
            'ㅗ': [1, 0, 1, 0, 0, 1], // 1,3,6
            '와': [1, 1, 1, 0, 0, 1], // 1,2,3,6
            '왜': [[1, 1, 1, 0, 0, 1], [1, 1, 1, 0, 1, 0]],
            '외': [1, 0, 1, 1, 0, 1], // 1,3,4,6
            '요': [0, 0, 1, 1, 0, 1], // 3,4,6
            'ㅜ': [1, 0, 1, 1, 0, 0], // 1,3,4
            '워': [1, 1, 1, 1, 0, 0], // 1,2,3,4
            '웨': [[1, 1, 1, 1, 0, 0], [1, 1, 1, 0, 1, 0]],
            '위': [[1, 0, 1, 1, 0, 0], [1, 1, 1, 0, 1, 0]],
            '유': [1, 0, 0, 1, 1, 1], // 1,4,5,6
            'ㅡ': [0, 1, 0, 1, 0, 1], // 2,4,6
            '의': [0, 1, 1, 1, 0, 1], // 2,3,4,6
            'ㅣ': [1, 0, 1, 0, 1, 0]  // 1,3,5
        };

        // Final Consonants (종성)
        const JONG_SEONG = {
            'ㄱ': [1, 0, 0, 0, 0, 0], // 1
            'ㄴ': [0, 1, 0, 0, 1, 0], // 2,5
            'ㄷ': [0, 0, 1, 0, 1, 0], // 3,5
            'ㄹ': [0, 1, 0, 0, 0, 0], // 2
            'ㅁ': [0, 1, 0, 0, 0, 1], // 2,6
            'ㅂ': [1, 1, 0, 0, 0, 0], // 1,2
            'ㅅ': [0, 0, 1, 0, 0, 0], // 3
            'ㅇ': [0, 1, 1, 0, 1, 1], // 2,3,5,6
            'ㅈ': [1, 0, 1, 0, 0, 0], // 1,3
            'ㅊ': [0, 1, 1, 0, 0, 0], // 2,3
            'ㅋ': [0, 1, 1, 0, 1, 0], // 2,3,5
            'ㅌ': [0, 1, 1, 0, 0, 1], // 2,3,6
            'ㅍ': [0, 1, 0, 0, 1, 1], // 2,5,6
            'ㅎ': [0, 1, 1, 1, 0, 0], // 2,3,4
            'ㄲ': [[1, 0, 0, 0, 0, 0], [1, 0, 0, 0, 0, 0]],
            'ㅆ': [0, 0, 1, 0, 1, 0]  // 3,4 (종성 ㅆ 특수점자)
        };

        // Numbers (0-9) with Number Indicator Prefix (⠼ : 3,4,5,6)
        const NUMBER_PREFIX = [0, 0, 1, 1, 1, 1]; // 3,4,5,6
        const NUMBERS = {
            '1': [1, 0, 0, 0, 0, 0], // 1
            '2': [1, 1, 0, 0, 0, 0], // 1,2
            '3': [1, 0, 0, 1, 0, 0], // 1,4
            '4': [1, 0, 0, 1, 1, 0], // 1,4,5
            '5': [1, 0, 0, 0, 1, 0], // 1,5
            '6': [1, 1, 0, 1, 0, 0], // 1,2,4
            '7': [1, 1, 0, 1, 1, 0], // 1,2,4,5
            '8': [1, 1, 0, 0, 1, 0], // 1,2,5
            '9': [0, 1, 0, 1, 0, 0], // 2,4
            '0': [0, 1, 0, 1, 1, 0]  // 2,4,5
        };

        // English Letters (a-z)
        const ENGLISH = {
            'a': [1, 0, 0, 0, 0, 0], 'b': [1, 1, 0, 0, 0, 0], 'c': [1, 0, 0, 1, 0, 0],
            'd': [1, 0, 0, 1, 1, 0], 'e': [1, 0, 0, 0, 1, 0], 'f': [1, 1, 0, 1, 0, 0],
            'g': [1, 1, 0, 1, 1, 0], 'h': [1, 1, 0, 0, 1, 0], 'i': [0, 1, 0, 1, 0, 0],
            'j': [0, 1, 0, 1, 1, 0], 'k': [1, 0, 1, 0, 0, 0], 'l': [1, 1, 1, 0, 0, 0],
            'm': [1, 0, 1, 1, 0, 0], 'n': [1, 0, 1, 1, 1, 0], 'o': [1, 0, 1, 0, 1, 0],
            'p': [1, 1, 1, 1, 0, 0], 'q': [1, 1, 1, 1, 1, 0], 'r': [1, 1, 1, 0, 1, 0],
            's': [0, 1, 1, 1, 0, 0], 't': [0, 1, 1, 1, 1, 0], 'u': [1, 0, 1, 0, 0, 1],
            'v': [1, 1, 1, 0, 0, 1], 'w': [0, 1, 0, 1, 1, 1], 'x': [1, 0, 1, 1, 0, 1],
            'y': [1, 0, 1, 1, 1, 1], 'z': [1, 0, 1, 0, 1, 1]
        };

        // Hangul Jamo lists for syllable decomposition
        const CHO_LIST = ['ㄱ','ㄲ','ㄴ','ㄷ','ㄸ','ㄹ','ㅁ','ㅂ','ㅃ','ㅅ','ㅆ','ㅇ','ㅈ','ㅉ','ㅊ','ㅋ','ㅌ','ㅍ','ㅎ'];
        const JUNG_LIST = ['ㅏ','ㅐ','ㅑ','ㅒ','ㅓ','ㅔ','여','예','ㅗ','와','왜','외','요','ㅜ','워','웨','위','유','ㅡ','의','ㅣ'];
        const JONG_LIST = ['','ㄱ','ㄲ','ㄳ','ㄴ','ㄵ','ㄶ','ㄷ','ㄹ','ㄺ','ㄻ','ㄼ','ㄽ','ㄾ','ㄿ','ㅀ','ㅁ','ㅂ','ㅄ','ㅅ','ㅆ','ㅇ','ㅈ','ㅊ','ㅋ','ㅌ','ㅍ','ㅎ'];

        // Convert [d1,d2,d3,d4,d5,d6] to Unicode Braille Pattern Character (U+2800 ~ U+283F)
        function dotsToUnicode(dots) {
            if (!dots) return ' ';
            let value = 0;
            if (dots[0]) value |= 0x01; // Dot 1
            if (dots[1]) value |= 0x02; // Dot 2
            if (dots[2]) value |= 0x04; // Dot 3
            if (dots[3]) value |= 0x08; // Dot 4
            if (dots[4]) value |= 0x10; // Dot 5
            if (dots[5]) value |= 0x20; // Dot 6
            return String.fromCharCode(0x2800 + value);
        }

        // Horizontal Flip Logic: Left col (1,2,3) <-> Right col (4,5,6)
        function flipDots(dots) {
            if (!dots) return [0,0,0,0,0,0];
            return [
                dots[3], // New Dot 1 = Old Dot 4
                dots[4], // New Dot 2 = Old Dot 5
                dots[5], // New Dot 3 = Old Dot 6
                dots[0], // New Dot 4 = Old Dot 1
                dots[1], // New Dot 5 = Old Dot 2
                dots[2]  // New Dot 6 = Old Dot 3
            ];
        }

        // Convert Input String to Array of Cell Objects
        function textToBrailleCells(text) {
            const cells = [];
            let inNumberMode = false;

            for (let i = 0; i < text.length; i++) {
                const char = text[i];
                const code = char.charCodeAt(0);

                // 1. Space
                if (char === ' ') {
                    inNumberMode = false;
                    cells.push({ label: '공백', dots: [0,0,0,0,0,0] });
                    continue;
                }

                // 2. Numbers (0-9)
                if (char >= '0' && char <= '9') {
                    if (!inNumberMode) {
                        cells.push({ label: '수표(⠼)', dots: NUMBER_PREFIX });
                        inNumberMode = true;
                    }
                    cells.push({ label: char, dots: NUMBERS[char] });
                    continue;
                } else {
                    inNumberMode = false; // reset number mode when non-digit occurs
                }

                // 3. Korean Hangul Syllable (AC00-D7A3)
                if (code >= 0xAC00 && code <= 0xD7A3) {
                    const sylIndex = code - 0xAC00;
                    const choIdx = Math.floor(sylIndex / 588);
                    const jungIdx = Math.floor((sylIndex % 588) / 28);
                    const jongIdx = sylIndex % 28;

                    const choChar = CHO_LIST[choIdx];
                    const jungChar = JUNG_LIST[jungIdx];
                    const jongChar = JONG_LIST[jongIdx];

                    // Cho-seong
                    if (choChar && choChar !== 'ㅇ') {
                        if (CHO_SEONG[choChar] === 'DOUBLE_ㄱ') {
                            cells.push({ label: '된소리(⠠)', dots: [0,0,0,0,0,1] });
                            cells.push({ label: 'ㄱ', dots: CHO_SEONG['ㄱ'] });
                        } else if (CHO_SEONG[choChar] === 'DOUBLE_ㄷ') {
                            cells.push({ label: '된소리(⠠)', dots: [0,0,0,0,0,1] });
                            cells.push({ label: 'ㄷ', dots: CHO_SEONG['ㄷ'] });
                        } else if (CHO_SEONG[choChar] === 'DOUBLE_ㅂ') {
                            cells.push({ label: '된소리(⠠)', dots: [0,0,0,0,0,1] });
                            cells.push({ label: 'ㅂ', dots: CHO_SEONG['ㅂ'] });
                        } else if (CHO_SEONG[choChar] === 'DOUBLE_ㅅ') {
                            cells.push({ label: '된소리(⠠)', dots: [0,0,0,0,0,1] });
                            cells.push({ label: 'ㅅ', dots: CHO_SEONG['ㅅ'] });
                        } else if (CHO_SEONG[choChar] === 'DOUBLE_ㅈ') {
                            cells.push({ label: '된소리(⠠)', dots: [0,0,0,0,0,1] });
                            cells.push({ label: 'ㅈ', dots: CHO_SEONG['ㅈ'] });
                        } else if (CHO_SEONG[choChar]) {
                            cells.push({ label: choChar, dots: CHO_SEONG[choChar] });
                        }
                    }

                    // Jung-seong
                    if (jungChar && JUNG_SEONG[jungChar]) {
                        const jData = JUNG_SEONG[jungChar];
                        if (Array.isArray(jData[0])) { // multi-cell vowel
                            jData.forEach(d => cells.push({ label: jungChar, dots: d }));
                        } else {
                            cells.push({ label: jungChar, dots: jData });
                        }
                    }

                    // Jong-seong
                    if (jongChar && JONG_SEONG[jongChar]) {
                        const jData = JONG_SEONG[jongChar];
                        if (Array.isArray(jData[0])) { // double final consonant
                            jData.forEach(d => cells.push({ label: jongChar, dots: d }));
                        } else {
                            cells.push({ label: jongChar, dots: jData });
                        }
                    }
                    continue;
                }

                // 4. English Alphabet
                const lowerChar = char.toLowerCase();
                if (ENGLISH[lowerChar]) {
                    cells.push({ label: char, dots: ENGLISH[lowerChar] });
                    continue;
                }

                // Fallback for unknown symbol
                cells.push({ label: char, dots: [0,0,0,0,0,0] });
            }

            return cells;
        }

        // Main Trigger Function
        function convertText() {
            const inputVal = document.getElementById('text-input').value;
            document.getElementById('char-count').innerText = inputVal.length;

            const cells = textToBrailleCells(inputVal);

            // 1. Build Normal Unicode String & Grid
            let normalUnicode = '';
            let flippedUnicode = '';

            const normalGrid = document.getElementById('normal-braille-grid');
            const flippedGrid = document.getElementById('flipped-braille-grid');

            normalGrid.innerHTML = '';
            flippedGrid.innerHTML = '';

            if (cells.length === 0) {
                document.getElementById('normal-braille-text').innerHTML = '<span class="text-slate-300 text-sm font-sans">입력 시 실시간 점자 표시</span>';
                document.getElementById('flipped-braille-text').innerHTML = '<span class="text-slate-300 text-sm font-sans">입력 시 좌우 반전 점자 표시</span>';
                normalGrid.innerHTML = '<div class="text-xs text-slate-400 m-auto">입력된 글자가 점자 셀로 분해되어 표시됩니다.</div>';
                flippedGrid.innerHTML = '<div class="text-xs text-slate-400 m-auto">반전된 점자 셀이 표시됩니다.</div>';
                return;
            }

            cells.forEach(cell => {
                const normDots = cell.dots;
                const flipDotsArr = flipDots(cell.dots);

                normalUnicode += dotsToUnicode(normDots);
                flippedUnicode += dotsToUnicode(flipDotsArr);

                // Render Normal Cell Card
                normalGrid.appendChild(createCellCard(cell.label, normDots, false));

                // Render Flipped Cell Card
                flippedGrid.appendChild(createCellCard(cell.label, flipDotsArr, true));
            });

            document.getElementById('normal-braille-text').innerText = normalUnicode;
            document.getElementById('flipped-braille-text').innerText = flippedUnicode;
        }

        // Generate Single Visual 6-Dot Cell Element
        function createCellCard(label, dots, isFlipped) {
            const card = document.createElement('div');
            card.className = `flex flex-col items-center p-2 rounded-xl border ${isFlipped ? 'bg-emerald-50/60 border-emerald-200' : 'bg-white border-slate-200'} shadow-2xs`;

            // Label
            const lbl = document.createElement('span');
            lbl.className = `text-[11px] font-bold mb-1.5 ${isFlipped ? 'text-emerald-800' : 'text-slate-700'}`;
            lbl.innerText = label;

            // 6-Dot Matrix Container (2 cols x 3 rows)
            const dotMatrix = document.createElement('div');
            dotMatrix.className = "grid grid-cols-2 gap-1.5 p-1.5 bg-slate-900 rounded-lg";

            // Dot order inside CSS Grid:
            // Row 1: Dot 1 (col 0), Dot 4 (col 1)
            // Row 2: Dot 2 (col 0), Dot 5 (col 1)
            // Row 3: Dot 3 (col 0), Dot 6 (col 1)
            const dotIndices = [0, 3, 1, 4, 2, 5];

            dotIndices.forEach(idx => {
                const dot = document.createElement('div');
                const isActive = dots[idx] === 1;

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

        // Sample Text Button Helper
        function setSample(text) {
            document.getElementById('text-input').value = text;
            convertText();
        }

        // Clear Helper
        function clearInput() {
            document.getElementById('text-input').value = '';
            convertText();
        }

        // Copy to Clipboard
        function copyToClipboard(elementId) {
            const el = document.getElementById(elementId);
            const text = el.innerText;

            if (!text || text.includes('입력 시')) return;

            navigator.clipboard.writeText(text).then(() => {
                showToast("점자 텍스트가 클립보드에 복사되었습니다!");
            }).catch(() => {
                // Fallback
                const textarea = document.createElement('textarea');
                textarea.value = text;
                document.body.appendChild(textarea);
                textarea.select();
                document.execCommand('copy');
                document.body.removeChild(textarea);
                showToast("점자 텍스트가 클립보드에 복사되었습니다!");
            });
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

        // Guide Modal Handlers
        function openGuideModal() {
            document.getElementById('guide-modal').classList.remove('hidden');
        }

        function closeGuideModal() {
            document.getElementById('guide-modal').classList.add('hidden');
        }

        // Initialize with default sample
        window.addEventListener('DOMContentLoaded', () => {
            setSample('안녕하세요');
        });
    </script>
</body>
</html>
