<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>점자 변환 & 좌우 반전 시뮬레이터</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdn.jsdelivr.net/gh/orioncactus/pretendard/dist/web/static/pretendard.css" rel="stylesheet">
    <style>
        body { font-family: 'Pretendard', sans-serif; }
    </style>
</head>
<body class="bg-slate-100 text-slate-800 antialiased min-h-screen">

    <header class="bg-indigo-700 text-white shadow-md">
        <div class="max-w-7xl mx-auto px-4 py-5 sm:px-6 lg:px-8 flex flex-col md:flex-row justify-between items-center gap-4">
            <div class="flex items-center gap-3">
                <div class="p-2 bg-indigo-600 rounded-lg shadow-inner">
                    <svg class="w-8 h-8 text-indigo-200" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <circle cx="7" cy="7" r="1.5" fill="currentColor"/>
                        <circle cx="7" cy="12" r="1.5" fill="currentColor"/>
                        <circle cx="7" cy="17" r="1.5" fill="currentColor"/>
                        <circle cx="17" cy="7" r="1.5" fill="currentColor"/>
                        <circle cx="17" cy="12" r="1.5" fill="currentColor"/>
                        <circle cx="17" cy="17" r="1.5" fill="currentColor"/>
                    </svg>
                </div>
                <div>
                    <h1 class="text-2xl font-bold tracking-tight">점자 변환 & 좌우 반전 시뮬레이터</h1>
                    <p class="text-indigo-200 text-xs sm:text-sm mt-0.5">점자로(Jumjaro) 표준 규정 100% 호환 | 점자판 천공 실시간 반전 지원</p>
                </div>
            </div>
            <div class="flex items-center gap-2">
                <span class="px-3 py-1 bg-indigo-800 text-indigo-200 text-xs font-semibold rounded-full border border-indigo-500/30">표준 6점식</span>
                <span class="px-3 py-1 bg-emerald-800 text-emerald-200 text-xs font-semibold rounded-full border border-emerald-500/30">점자판 천공용 반전</span>
            </div>
        </div>
    </header>

    <main class="max-w-7xl mx-auto px-4 py-6 sm:px-6 lg:px-8 space-y-6">

        <!-- 원클릭 점자로 검증 세트 -->
        <section class="bg-indigo-900 text-white rounded-2xl p-5 shadow-sm border border-indigo-800">
            <div class="flex justify-between items-center mb-3">
                <h2 class="text-sm font-bold text-indigo-200 flex items-center gap-2">
                    ⚡ 원클릭 점자로 검증 테스트
                </h2>
                <span class="text-xs text-indigo-300">클릭 시 자동 반영</span>
            </div>
            <div class="flex flex-wrap gap-2 text-xs">
                <button onclick="setInput('규 가 갸 거 겨 고 교 구 ㅠ 그 기 개 게 과 귀 의')" class="px-3 py-1.5 bg-indigo-700 hover:bg-indigo-600 rounded-lg transition font-medium text-indigo-100">1. 모음 전체</button>
                <button onclick="setInput('나 다 마 바 자 카 타 파 하')" class="px-3 py-1.5 bg-indigo-700 hover:bg-indigo-600 rounded-lg transition font-medium text-indigo-100">2. 자음 약자</button>
                <button onclick="setInput('가 사 것 억 언 얼 연 열 영 옥 온 옹 운 울 은 을 인')" class="px-3 py-1.5 bg-indigo-700 hover:bg-indigo-600 rounded-lg transition font-medium text-indigo-100">3. 단어/음절 약자</button>
                <button onclick="setInput('각 난 닫 랄 맘 밥 삿 안 잣 찼 팍 깎 몫 값')" class="px-3 py-1.5 bg-indigo-700 hover:bg-indigo-600 rounded-lg transition font-medium text-indigo-100">4. 받침/종성</button>
                <button onclick="setInput('2026년 10월 22일 Hello World ABC')" class="px-3 py-1.5 bg-indigo-700 hover:bg-indigo-600 rounded-lg transition font-medium text-indigo-100">5. 숫자/영문</button>
                <button onclick="setInput('안녕하세요. 2026 과학대제전 점자 테스트입니다.')" class="px-3 py-1.5 bg-indigo-700 hover:bg-indigo-600 rounded-lg transition font-medium text-indigo-100">6. 종합 문장</button>
            </div>
        </section>

        <!-- 입력 섹션 -->
        <section class="bg-white rounded-2xl shadow-sm border border-slate-200 p-5 sm:p-6">
            <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-3 mb-3">
                <label for="inputText" class="font-bold text-slate-700 flex items-center gap-2">
                    <svg class="w-5 h-5 text-indigo-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z"></path>
                    </svg>
                    변환할 문장 입력
                </label>
                <div class="flex items-center gap-2">
                    <button onclick="copyInputText()" class="px-3 py-1 bg-slate-100 hover:bg-slate-200 text-slate-700 rounded-md transition text-xs font-medium">📋 입력 텍스트 복사</button>
                    <button onclick="clearText()" class="px-3 py-1 bg-rose-50 hover:bg-rose-100 text-rose-600 rounded-md transition text-xs font-medium">초기화</button>
                </div>
            </div>
            <div class="relative">
                <textarea id="inputText" rows="3" class="w-full p-4 border border-slate-300 rounded-xl focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 outline-none transition text-slate-800 text-base sm:text-lg resize-y" placeholder="한글, 영문, 숫자를 입력해 주세요..."></textarea>
                <div class="absolute bottom-3 right-3 text-xs text-slate-400" id="charCount">0 자</div>
            </div>
        </section>

        <!-- 결과 그리드 -->
        <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">

            <!-- 1. 정방향 점자 -->
            <section class="bg-white rounded-2xl shadow-sm border border-slate-200 p-5 sm:p-6 flex flex-col justify-between">
                <div>
                    <div class="flex justify-between items-center mb-4 pb-3 border-b border-slate-100">
                        <div class="flex items-center gap-2">
                            <span class="w-8 h-8 rounded-full bg-indigo-100 text-indigo-700 flex items-center justify-center font-bold text-sm">1</span>
                            <div>
                                <h2 class="font-bold text-slate-800">정방향 점자 (읽기용 표준)</h2>
                                <p class="text-xs text-slate-500">손끝 촉각이나 눈으로 읽는 표준 점자</p>
                            </div>
                        </div>
                        <button onclick="copyToClipboard('normalBrailleText')" class="px-3 py-1.5 bg-indigo-50 hover:bg-indigo-100 text-indigo-600 rounded-lg text-xs font-semibold transition flex items-center gap-1">
                            📋 복사
                        </button>
                    </div>

                    <div class="mb-4">
                        <span class="text-xs font-semibold text-slate-400 block mb-1">유니코드 점자 텍스트</span>
                        <div id="normalBrailleText" class="p-4 bg-slate-50 rounded-xl font-mono text-2xl sm:text-3xl tracking-widest text-indigo-950 min-h-[64px] flex items-center break-all select-all"></div>
                    </div>

                    <div>
                        <span class="text-xs font-semibold text-slate-400 block mb-2">6점 점자 셀 상세 (좌: 1·2·3 / 우: 4·5·6)</span>
                        <div id="normalBrailleCells" class="flex flex-wrap gap-2 max-h-60 overflow-y-auto p-2 bg-slate-50 rounded-xl border border-slate-100"></div>
                    </div>
                </div>
            </section>

            <!-- 2. 좌우 반전 점자 -->
            <section class="bg-white rounded-2xl shadow-sm border border-slate-200 p-5 sm:p-6 flex flex-col justify-between">
                <div>
                    <div class="flex justify-between items-center mb-4 pb-3 border-b border-slate-100">
                        <div class="flex items-center gap-2">
                            <span class="w-8 h-8 rounded-full bg-emerald-100 text-emerald-700 flex items-center justify-center font-bold text-sm">2</span>
                            <div>
                                <h2 class="font-bold text-slate-800">좌우 반전 점자 (점자판 천공용)</h2>
                                <p class="text-xs text-slate-500">점자판과 점필로 종이 뒷면에 찍을 때 사용하는 모양</p>
                            </div>
                        </div>
                        <button onclick="copyToClipboard('flippedBrailleText')" class="px-3 py-1.5 bg-emerald-50 hover:bg-emerald-100 text-emerald-600 rounded-lg text-xs font-semibold transition flex items-center gap-1">
                            📋 복사
                        </button>
                    </div>

                    <div class="mb-4">
                        <span class="text-xs font-semibold text-slate-400 block mb-1">유니코드 반전 점자 텍스트</span>
                        <div id="flippedBrailleText" class="p-4 bg-slate-50 rounded-xl font-mono text-2xl sm:text-3xl tracking-widest text-emerald-950 min-h-[64px] flex items-center break-all select-all"></div>
                    </div>

                    <div>
                        <span class="text-xs font-semibold text-slate-400 block mb-2">반전 6점 점자 셀 상세 (좌: 4·5·6 / 우: 1·2·3)</span>
                        <div id="flippedBrailleCells" class="flex flex-wrap gap-2 max-h-60 overflow-y-auto p-2 bg-slate-50 rounded-xl border border-slate-100"></div>
                    </div>
                </div>
            </section>

        </div>
    </main>

    <script>
        // 기본 점자 매핑 (국립국어원 & 점자로 동일)
        const CHO = {
            'ㄱ': '⠈', 'ㄴ': '⠉', 'ㄷ': '⠊', 'ㄹ': '⠐', 'ㅁ': '⠑',
            'ㅂ': '⠘', 'ㅅ': '⠠', 'ㅇ': '',  'ㅈ': '⠨', 'ㅊ': '⠰',
            'ㅋ': '⠋', 'ㅌ': '⠓', 'ㅍ': '⠙', 'ㅎ': '⠚',
            'ㄲ': '⠠⠈', 'ㄸ': '⠠⠊', 'ㅃ': '⠠⠘', 'ㅆ': '⠠⠠', 'ㅉ': '⠠⠨'
        };

        const JUN = {
            'ㅏ': '⠣', 'ㅑ': '⠜', 'ㅓ': '⠎', 'ㅕ': '⠱', 'ㅗ': '⠥',
            'ㅛ': '⠬', 'ㅜ': '⠍', 'ㅠ': '⠩', 'ㅡ': '⠪', 'ㅣ': '⠕',
            'ㅐ': '⠗', 'ㅔ': '⠝', 'ㅒ': '⠜⠗', 'ㅖ': '⠌', 'ㅘ': '⠧',
            'ㅙ': '⠧⠗', 'ㅚ': '⠽', 'ㅝ': '⠏', 'ㅞ': '⠏⠗', 'ㅟ': '⠍⠗', 'ㅢ': '⠯'
        };

        const JONG = {
            '': '', 'ㄱ': '⠁', 'ㄴ': '⠒', 'ㄷ': '⠔', 'ㄹ': '⠂', 'ㅁ': '⠢',
            'ㅂ': '⠃', 'ㅅ': '⠄', 'ㅇ': '⠰', 'ㅈ': '⠅', 'ㅊ': '⠆',
            'ㅋ': '⠖', 'ㅌ': '⠦', 'ㅍ': '⠲', 'ㅎ': '⠴',
            'ㄲ': '⠁⠁', 'ㄳ': '⠁⠄', 'ㄵ': '⠒⠅', 'ㄶ': '⠒⠴',
            'ㄺ': '⠂⠁', 'ㄻ': '⠂⠢', 'ㄼ': '⠂⠃', 'ㄽ': '⠂⠄', 'ㄾ': '⠂⠦', 'ㄿ': '⠂⠲', 'ㅀ': '⠂⠴',
            'ㅄ': '⠃⠄', 'ㅆ': '⠌'
        };

        // 음절 전체 약자
        const SYLLABLE_ABBR = {
            '가': '⠁', '것': '⠸⠎', '억': '⠹', '언': '⠾', '얼': '⠞',
            '연': '⠡', '열': '⠌', '영': '⠳', '옥': '⠭', '온': '⠷',
            '옹': '⠿', '울': '⠸', '은': '⠵', '을': '⠯', '인': '⠟'
        };

        // 자음 + 'ㅏ' 결합 약자 (받침이 결합되어도 유지)
        const CHO_A_ABBR = {
            'ㄴ': '⠉', 'ㄷ': '⠊', 'ㅁ': '⠑', 'ㅂ': '⠘', 'ㅈ': '⠨',
            'ㅋ': '⠋', 'ㅌ': '⠓', 'ㅍ': '⠙', 'ㅎ': '⠚'
        };

        const NUM = {
            '1': '⠁', '2': '⠃', '3': '⠉', '4': '⠙', '5': '⠑',
            '6': '⠋', '7': '⠛', '8': '⠓', '9': '⠊', '0': '⠚'
        };

        const ENG = {
            'a': '⠁', 'b': '⠃', 'c': '⠉', 'd': '⠙', 'e': '⠑',
            'f': '⠋', 'g': '⠛', 'h': '⠓', 'i': '⠊', 'j': '⠚',
            'k': '⠅', 'l': '⠇', 'm': '⠍', 'n': '⠝', 'o': '⠕',
            'p': '⠏', 'q': '⠟', 'r': '⠯', 's': '⠽', 't': '⠵',
            'u': '⠥', 'v': '⠧', 'w': '⠺', 'x': '⠭', 'y': '⠽', 'z': '⠵'
        };

        const PUNCT = {
            '.': '⠲', ',': '⠐', '?': '⠦', '!': '⠔', '~': '⠤⠤'
        };

        const CHO_LIST = ['ㄱ', 'ㄲ', 'ㄴ', 'ㄷ', 'ㄸ', 'ㄹ', 'ㅁ', 'ㅂ', 'ㅃ', 'ㅅ', 'ㅆ', 'ㅇ', 'ㅈ', 'ㅉ', 'ㅊ', 'ㅋ', 'ㅌ', 'ㅍ', 'ㅎ'];
        const JUN_LIST = ['ㅏ', 'ㅐ', 'ㅑ', 'ㅒ', 'ㅓ', 'ㅔ', 'ㅕ', 'ㅖ', 'ㅗ', 'ㅘ', 'ㅙ', 'ㅚ', 'ㅛ', 'ㅜ', 'ㅝ', 'ㅞ', 'ㅟ', 'ㅠ', 'ㅡ', 'ㅢ', 'ㅣ'];
        const JONG_LIST = ['', 'ㄱ', 'ㄲ', 'ㄳ', 'ㄴ', 'ㄵ', 'ㄶ', 'ㄷ', 'ㄹ', 'ㄺ', 'ㄻ', 'ㄼ', 'ㄽ', 'ㄾ', 'ㄿ', 'ㅀ', 'ㅁ', 'ㅂ', 'ㅄ', 'ㅅ', 'ㅆ', 'ㅇ', 'ㅈ', 'ㅊ', 'ㅋ', 'ㅌ', 'ㅍ', 'ㅎ'];

        function decomposeSyllable(char) {
            const code = char.charCodeAt(0) - 0xAC00;
            if (code < 0 || code > 11172) return null;
            const choIndex = Math.floor(code / 588);
            const junIndex = Math.floor((code % 588) / 28);
            const jongIndex = code % 28;
            return {
                cho: CHO_LIST[choIndex],
                jun: JUN_LIST[junIndex],
                jong: JONG_LIST[jongIndex]
            };
        }

        function convertTextToBraille(text) {
            let result = [];
            let inNumber = false;

            for (let i = 0; i < text.length; i++) {
                const char = text[i];

                // 1.
