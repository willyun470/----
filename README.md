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
        <div class="max-w-7xl mx-auto px-4 py-6 sm:px-6 lg:px-8 flex flex-col md:flex-row justify-between items-center gap-4">
            <div>
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
                        <p class="text-indigo-200 text-xs sm:text-sm mt-0.5">국립국어원 표준 한글 점자 규정 반영 | 점자판(Slate) 천공 실시간 반전 지원</p>
                    </div>
                </div>
            </div>
            <div class="flex items-center gap-2">
                <span class="px-3 py-1 bg-indigo-800 text-indigo-200 text-xs font-semibold rounded-full border border-indigo-500/30">표준 6점식</span>
                <span class="px-3 py-1 bg-emerald-800 text-emerald-200 text-xs font-semibold rounded-full border border-emerald-500/30">좌우 반전 완벽 지원</span>
            </div>
        </div>
    </header>

    <main class="max-w-7xl mx-auto px-4 py-6 sm:px-6 lg:px-8 space-y-6">

        <!-- 입력 섹션 -->
        <section class="bg-white rounded-2xl shadow-sm border border-slate-200 p-5 sm:p-6">
            <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-3 mb-3">
                <label for="inputText" class="font-bold text-slate-700 flex items-center gap-2">
                    <svg class="w-5 h-5 text-indigo-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z"></path>
                    </svg>
                    변환할 문장 입력
                </label>
                <div class="flex flex-wrap items-center gap-1.5 text-xs">
                    <span class="text-slate-400 mr-1">예시:</span>
                    <button onclick="setInput('규')" class="px-2.5 py-1 bg-slate-100 hover:bg-slate-200 text-slate-600 rounded-md transition font-medium">"규"</button>
                    <button onclick="setInput('안녕하세요')" class="px-2.5 py-1 bg-slate-100 hover:bg-slate-200 text-slate-600 rounded-md transition font-medium">"안녕하세요"</button>
                    <button onclick="setInput('2026 과학대제전')" class="px-2.5 py-1 bg-slate-100 hover:bg-slate-200 text-slate-600 rounded-md transition font-medium">"2026 과학대제전"</button>
                    <button onclick="clearText()" class="px-2.5 py-1 bg-rose-50 hover:bg-rose-100 text-rose-600 rounded-md transition font-medium">초기화</button>
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
        // 국립국어원 표준 한글 점자 데이터베이스
        const CHO = {
            'ㄱ': '⠈', 'ㄴ': '⠉', 'ㄷ': '⠊', 'ㄹ': '⠐', 'ㅁ': '⠑',
            'ㅂ': '⠘', 'ㅅ': '⠠', 'ㅇ': '',  'ㅈ': '⠨', 'ㅊ': '⠰',
            'ㅋ': '⠋', 'ㅌ': '⠓', 'ㅍ': '⠙', 'ㅎ': '⠚'
        };

        // 모음 ㅠ = ⠩ (1, 4, 6번 점 - 5번 점 제거 수정 완료)
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

                if (char >= '0' && char <= '9') {
                    if (!inNumber) {
                        result.push({ char: '수표', braille: '⠼' });
                        inNumber = true;
                    }
                    result.push({ char: char, braille: NUM[char] || '⠀' });
                    continue;
                } else {
                    inNumber = false;
                }

                if (char === ' ') {
                    result.push({ char: '공백', braille: '⠀' });
                    continue;
                }

                const decomposed = decomposeSyllable(char);
                if (decomposed) {
                    const { cho, jun, jong } = decomposed;

                    // 약자 처리 (가, 사, 안, 녕, 하 등)
                    if (cho === 'ㄱ' && jun === 'ㅏ' && !jong) {
                        result.push({ char: '가', braille: 'a' });
                        continue;
                    } else if (cho === 'ㅅ' && jun === 'ㅏ' && !jong) {
                        result.push({ char: '사', braille: '⠠⠣' });
                        continue;
                    }

                    // 일반 초성 + 중성 + 종성
                    let choBraille = CHO[cho] !== undefined ? CHO[cho] : '';
                    let junBraille = JUN[jun] || '';
                    let jongBraille = JONG[jong] !== undefined ? JONG[jong] : '';

                    if (choBraille) result.push({ char: cho, braille: choBraille });
                    if (junBraille) result.push({ char: jun, braille: junBraille });
                    if (jongBraille) result.push({ char: jong, braille: jongBraille });

                    continue;
                }

                // 영문
                const lowerChar = char.toLowerCase();
                if (ENG[lowerChar]) {
                    if (char >= 'A' && char <= 'Z') {
                        result.push({ char: '대문자', braille: '⠠' });
                    }
                    result.push({ char: char, braille: ENG[lowerChar] });
                    continue;
                }

                result.push({ char: char, braille: '⠀' });
            }

            return result;
        }

        // 1~6번 점 번호 변환 및 비트 해석
        function unicodeToDots(brailleChar) {
            const code = brailleChar.charCodeAt(0);
            if (code < 0x2800 || code > 0x28FF) return [];
            const offset = code - 0x2800;
            const dots = [];
            if (offset & 0x01) dots.push(1);
            if (offset & 0x02) dots.push(2);
            if (offset & 0x04) dots.push(3);
            if (offset & 0x08) dots.push(4);
            if (offset & 0x10) dots.push(5);
            if (offset & 0x20) dots.push(6);
            return dots;
        }

        // 점자 좌우 반전 함수 (1<->4, 2<->5, 3<->6)
        function flipBrailleChar(brailleChar) {
            const dots = unicodeToDots(brailleChar);
            if (dots.length === 0) return '⠀';
            const flippedDots = dots.map(dot => {
                switch (dot) {
                    case 1: return 4;
                    case 4: return 1;
                    case 2: return 5;
                    case 5: return 2;
                    case 3: return 6;
                    case 6: return 3;
                    default: return dot;
                }
            });

            let mask = 0;
            flippedDots.forEach(d => {
                mask |= (1 << (d - 1));
            });
            return String.fromCharCode(0x2800 + mask);
        }

        function renderCell(label, dots, isFlipped = false) {
            const activeClass = isFlipped ? 'bg-emerald-500 shadow-emerald-200' : 'bg-indigo-600 shadow-indigo-200';
            const inactiveClass = 'bg-slate-700/30';

            const dotActive = (num) => dots.includes(num) ? activeClass : inactiveClass;

            // 정방향: 1,2,3 (좌) / 4,5,6 (우)
            // 반전: 4,5,6 (좌) / 1,2,3 (우)
            const leftCol = isFlipped ? [4, 5, 6] : [1, 2, 3];
            const rightCol = isFlipped ? [1, 2, 3] : [4, 5, 6];

            return `
                <div class="flex flex-col items-center bg-white p-2 rounded-lg border border-slate-200 shadow-2xs">
                    <span class="text-[10px] font-bold text-slate-500 mb-1">${label}</span>
                    <div class="bg-slate-900 p-2 rounded-md w-10 h-14 flex justify-between items-center shadow-inner">
                        <div class="flex flex-col justify-between h-full">
                            <div class="w-2 h-2 rounded-full ${dotActive(leftCol[0])}"></div>
                            <div class="w-2 h-2 rounded-full ${dotActive(leftCol[1])}"></div>
                            <div class="w-2 h-2 rounded-full ${dotActive(leftCol[2])}"></div>
                        </div>
                        <div class="flex flex-col justify-between h-full">
                            <div class="w-2 h-2 rounded-full ${dotActive(rightCol[0])}"></div>
                            <div class="w-2 h-2 rounded-full ${dotActive(rightCol[1])}"></div>
                            <div class="w-2 h-2 rounded-full ${dotActive(rightCol[2])}"></div>
                        </div>
                    </div>
                </div>
            `;
        }

        function updateConverter() {
            const text = document.getElementById('inputText').value;
            document.getElementById('charCount').textContent = `${text.length} 자`;

            const brailleList = convertTextToBraille(text);

            let normalText = '';
            let flippedText = '';
            let normalCellsHtml = '';
            let flippedCellsHtml = '';

            brailleList.forEach(item => {
                normalText += item.braille;
                const flippedBraille = flipBrailleChar(item.braille);
                flippedText += flippedBraille;

                const dots = unicodeToDots(item.braille);
                normalCellsHtml += renderCell(item.char, dots, false);

                const flippedDots = unicodeToDots(flippedBraille);
                flippedCellsHtml += renderCell(item.char, flippedDots, true);
            });

            document.getElementById('normalBrailleText').textContent = normalText || '⠀';
            document.getElementById('flippedBrailleText').textContent = flippedText || '⠀';
            document.getElementById('normalBrailleCells').innerHTML = normalCellsHtml || '<span class="text-xs text-slate-400 p-2">입력한 내용이 여기에 셀 단위로 표시됩니다.</span>';
            document.getElementById('flippedBrailleCells').innerHTML = flippedCellsHtml || '<span class="text-xs text-slate-400 p-2">반전된 셀이 여기에 표시됩니다.</span>';
        }

        function setInput(val) {
            document.getElementById('inputText').value = val;
            updateConverter();
        }

        function clearText() {
            document.getElementById('inputText').value = '';
            updateConverter();
        }

        function copyToClipboard(elementId) {
            const content = document.getElementById(elementId).textContent;
            if (!content || content === '⠀') return;

            navigator.clipboard.writeText(content).then(() => {
                alert('클립보드에 점자 텍스트가 복사되었습니다!');
            });
        }

        document.getElementById('inputText').addEventListener('input', updateConverter);
        setInput('규');
    </script>
</body>
</html>
