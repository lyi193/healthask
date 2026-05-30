<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>遠距健康問診機器人</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@300;400;500;700;900&display=swap');
        body {
            font-family: 'Noto Sans TC', sans-serif;
        }
        /* 自訂滾動條 */
        ::-webkit-scrollbar {
            width: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f1f1;
        }
        ::-webkit-scrollbar-thumb {
            background: #cbd5e1;
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #94a3b8;
        }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 min-h-screen flex flex-col">

    <!-- 頂部導覽列 -->
    <header class="bg-white border-b border-slate-200 sticky top-0 z-50 shadow-sm">
        <div class="max-w-5xl mx-auto px-4 py-3 flex items-center justify-between">
            <div class="flex items-center space-x-3">
                <div class="bg-teal-500 text-white p-2 rounded-xl shadow-md shadow-teal-100">
                    <i data-lucide="heart-pulse" class="w-6 h-6"></i>
                </div>
                <div>
                    <h1 class="text-lg font-bold text-slate-900 flex items-center gap-2">
                        安心健康問診助手
                        <span class="text-xs bg-teal-50 text-teal-700 px-2 py-0.5 rounded-full border border-teal-200 font-normal">免登入版</span>
                    </h1>
                    <p class="text-xs text-slate-500">24小時守護您與家人的健康狀況</p>
                </div>
            </div>
            <div class="flex items-center space-x-2">
                <button onclick="toggleHistoryModal()" class="flex items-center space-x-1 text-slate-600 hover:text-teal-600 px-3 py-2 rounded-lg hover:bg-slate-100 transition text-sm">
                    <i data-lucide="history" class="w-4 h-4"></i>
                    <span class="hidden sm:inline">歷史紀錄</span>
                </button>
                <a href="tel:119" class="flex items-center space-x-1 bg-red-50 text-red-600 hover:bg-red-100 px-3 py-2 rounded-lg transition text-sm font-semibold border border-red-100">
                    <i data-lucide="phone-call" class="w-4 h-4 text-red-500"></i>
                    <span>緊急求助 (119)</span>
                </a>
            </div>
        </div>
    </header>

    <!-- 主要內容區 -->
    <main class="flex-1 max-w-5xl w-full mx-auto px-4 py-6 flex flex-col md:flex-row gap-6">
        
        <!-- 左側：對話問診區 (佔大版面) -->
        <section class="flex-1 bg-white rounded-2xl border border-slate-200 shadow-sm flex flex-col h-[calc(100vh-180px)] min-h-[500px]">
            <!-- 對話框頭部 -->
            <div class="px-6 py-4 border-b border-slate-100 bg-slate-50/50 rounded-t-2xl flex items-center justify-between">
                <div class="flex items-center space-x-2">
                    <span class="w-2.5 h-2.5 rounded-full bg-emerald-500 animate-pulse"></span>
                    <span class="text-sm font-medium text-slate-600">AI 護理師在線中</span>
                </div>
                <button onclick="resetChat()" class="text-xs text-slate-500 hover:text-red-500 flex items-center gap-1 transition">
                    <i data-lucide="refresh-cw" class="w-3 h-3"></i> 重置問診
                </button>
            </div>

            <!-- 訊息展示區 -->
            <div id="chat-messages" class="flex-1 overflow-y-auto p-6 space-y-4">
                <!-- 機器人歡迎語 -->
                <div class="flex items-start space-x-3">
                    <div class="w-8 h-8 rounded-full bg-teal-100 text-teal-600 flex items-center justify-center flex-shrink-0">
                        <i data-lucide="bot" class="w-5 h-5"></i>
                    </div>
                    <div class="bg-slate-100 text-slate-800 rounded-2xl rounded-tl-none px-4 py-3 max-w-[85%] text-sm shadow-sm leading-relaxed">
                        您好！我是您的「安心健康問診助手」。此系統為健康評估輔助工具，<b>不具備正式醫療診斷功能</b>。若有緊急生命危險，請立即撥打 119！
                        <br><br>
                        為了給您精確的建議，我們首先需要了解您的基本資料。請問您的<b>性別</b>與<b>年齡層</b>是？
                    </div>
                </div>

                <!-- 選擇基本資料 (動態交互按鈕) -->
                <div id="basic-info-selector" class="flex flex-col space-y-4 pl-11">
                    <div>
                        <span class="text-xs text-slate-400 block mb-2 font-medium">1. 選擇生理性別</span>
                        <div class="flex gap-2">
                            <button onclick="selectGender('男')" id="btn-gender-male" class="gender-btn px-4 py-2 rounded-xl border border-slate-200 text-sm bg-white hover:border-teal-500 hover:text-teal-600 transition flex items-center gap-1">
                                🚹 男性
                            </button>
                            <button onclick="selectGender('女')" id="btn-gender-female" class="gender-btn px-4 py-2 rounded-xl border border-slate-200 text-sm bg-white hover:border-teal-500 hover:text-teal-600 transition flex items-center gap-1">
                                🚺 女性
                            </button>
                            <button onclick="selectGender('其他')" id="btn-gender-other" class="gender-btn px-4 py-2 rounded-xl border border-slate-200 text-sm bg-white hover:border-teal-500 hover:text-teal-600 transition">
                                其他/不願透露
                            </button>
                        </div>
                    </div>

                    <div>
                        <span class="text-xs text-slate-400 block mb-2 font-medium">2. 選擇年齡層</span>
                        <div class="grid grid-cols-2 sm:grid-cols-4 gap-2">
                            <button onclick="selectAge('嬰幼兒 (0-12個月)')" class="age-btn px-3 py-2 rounded-xl border border-slate-200 text-xs bg-white hover:border-teal-500 hover:text-teal-600 transition text-center">
                                👶 嬰幼兒
                            </button>
                            <button onclick="selectAge('兒童 (1-12歲)')" class="age-btn px-3 py-2 rounded-xl border border-slate-200 text-xs bg-white hover:border-teal-500 hover:text-teal-600 transition text-center">
                                👦 兒童
                            </button>
                            <button onclick="selectAge('青壯年 (13-64歲)')" class="age-btn px-3 py-2 rounded-xl border border-slate-200 text-xs bg-white hover:border-teal-500 hover:text-teal-600 transition text-center">
                                👨‍🦱 青壯年
                            </button>
                            <button onclick="selectAge('年長者 (65歲以上)')" class="age-btn px-3 py-2 rounded-xl border border-slate-200 text-xs bg-white hover:border-teal-500 hover:text-teal-600 transition text-center">
                                👵 年長者
                            </button>
                        </div>
                    </div>
                </div>
            </div>

            <!-- 症狀快速選單 (在輸入框上方，按需要顯示) -->
            <div id="quick-symptoms" class="hidden px-6 py-3 border-t border-slate-100 bg-slate-50 flex flex-wrap gap-2">
                <span class="text-xs text-slate-500 self-center mr-1">常見症狀快選：</span>
                <button onclick="appendSymptom('跌倒流血')" class="px-2.5 py-1 bg-white border border-slate-200 hover:border-teal-500 hover:text-teal-600 rounded-full text-xs transition">🩹 跌倒流血</button>
                <button onclick="appendSymptom('骨頭疼痛')" class="px-2.5 py-1 bg-white border border-slate-200 hover:border-teal-500 hover:text-teal-600 rounded-full text-xs transition">🦴 骨頭疼痛</button>
                <button onclick="appendSymptom('胸悶、胸痛')" class="px-2.5 py-1 bg-white border border-slate-200 hover:border-teal-500 hover:text-teal-600 rounded-full text-xs transition">🚨 胸痛胸悶</button>
                <button onclick="appendSymptom('發燒/畏寒')" class="px-2.5 py-1 bg-white border border-slate-200 hover:border-teal-500 hover:text-teal-600 rounded-full text-xs transition">🌡️ 發燒/畏寒</button>
                <button onclick="appendSymptom('呼吸困難')" class="px-2.5 py-1 bg-white border border-slate-200 hover:border-teal-500 hover:text-teal-600 rounded-full text-xs transition">🫁 呼吸困難</button>
                <button onclick="appendSymptom('劇烈頭痛')" class="px-2.5 py-1 bg-white border border-slate-200 hover:border-teal-500 hover:text-teal-600 rounded-full text-xs transition">🧠 劇烈頭痛</button>
                <button onclick="appendSymptom('腹瀉/嘔吐')" class="px-2.5 py-1 bg-white border border-slate-200 hover:border-teal-500 hover:text-teal-600 rounded-full text-xs transition">🤢 腹瀉嘔吐</button>
            </div>

            <!-- 輸入控制區 -->
            <div class="p-4 border-t border-slate-200 bg-white rounded-b-2xl">
                <form id="chat-form" onsubmit="handleSendMessage(event)" class="flex gap-2">
                    <input type="text" id="user-input" disabled
                           class="flex-1 border border-slate-200 rounded-xl px-4 py-3 text-sm focus:outline-none focus:ring-2 focus:ring-teal-500/20 focus:border-teal-500 disabled:bg-slate-50 disabled:cursor-not-allowed" 
                           placeholder="請先選擇上方基本資料...">
                    <button type="submit" id="send-btn" disabled
                            class="bg-teal-500 hover:bg-teal-600 text-white px-5 py-3 rounded-xl transition flex items-center justify-center disabled:opacity-50 disabled:cursor-not-allowed shadow-md shadow-teal-500/10">
                        <i data-lucide="send" class="w-5 h-5"></i>
                    </button>
                </form>
                <p class="text-[10px] text-slate-400 mt-2 text-center">本系統採用專家篩檢規則。問診結果僅供參考，若症狀持續或加劇請務必及時就醫。</p>
            </div>
        </section>

        <!-- 右側：診斷建議與就醫提醒 (即時更新) -->
        <section class="w-full md:w-80 flex flex-col gap-6">
            
            <!-- 即時警示燈 -->
            <div class="bg-white rounded-2xl border border-slate-200 p-5 shadow-sm">
                <h3 class="text-sm font-bold text-slate-900 mb-3 flex items-center gap-1.5">
                    <i data-lucide="shield-alert" class="w-4 h-4 text-teal-600"></i>
                    就醫警示燈號
                </h3>
                
                <div id="status-display" class="flex flex-col items-center justify-center py-6 bg-slate-50 rounded-xl border border-slate-100">
                    <!-- 預設：等待輸入 -->
                    <div id="status-waiting" class="text-center">
                        <div class="w-12 h-12 rounded-full bg-slate-200 flex items-center justify-center mx-auto mb-2 animate-pulse">
                            <i data-lucide="hourglass" class="w-6 h-6 text-slate-400"></i>
                        </div>
                        <p class="text-sm font-medium text-slate-600">等待輸入資料</p>
                        <p class="text-xs text-slate-400 mt-1">請於左側輸入症狀</p>
                    </div>

                    <!-- 綠燈：居家觀察 -->
                    <div id="status-green" class="hidden text-center">
                        <div class="w-16 h-16 rounded-full bg-emerald-100 border-4 border-emerald-500 flex items-center justify-center mx-auto mb-3 shadow-lg shadow-emerald-100">
                            <i data-lucide="check-circle" class="w-8 h-8 text-emerald-600"></i>
                        </div>
                        <span class="bg-emerald-50 text-emerald-700 px-3 py-1 rounded-full text-xs font-bold border border-emerald-200">綠燈：安全觀察</span>
                        <p class="text-xs text-slate-500 mt-2.5 px-4">目前症狀較為輕微，建議多休息與補充水分，居家觀察即可。</p>
                    </div>

                    <!-- 黃燈：預約門診 -->
                    <div id="status-yellow" class="hidden text-center">
                        <div class="w-16 h-16 rounded-full bg-amber-100 border-4 border-amber-500 flex items-center justify-center mx-auto mb-3 shadow-lg shadow-amber-100">
                            <i data-lucide="alert-triangle" class="w-8 h-8 text-amber-600"></i>
                        </div>
                        <span class="bg-amber-50 text-amber-700 px-3 py-1 rounded-full text-xs font-bold border border-amber-200">黃燈：預約就醫</span>
                        <p class="text-xs text-slate-500 mt-2.5 px-4">症狀需要專業醫療診治。建議預約門診，請勿拖延病情。</p>
                    </div>

                    <!-- 紅燈：緊急就醫 -->
                    <div id="status-red" class="hidden text-center">
                        <div class="w-16 h-16 rounded-full bg-red-100 border-4 border-red-500 flex items-center justify-center mx-auto mb-3 shadow-lg shadow-red-100 animate-bounce">
                            <i data-lucide="octagon-alert" class="w-8 h-8 text-red-600 animate-pulse"></i>
                        </div>
                        <span class="bg-red-50 text-red-700 px-3 py-1 rounded-full text-xs font-bold border border-red-200">紅燈：立即就醫！</span>
                        <p class="text-xs text-slate-500 mt-2.5 px-4 font-semibold text-red-600">偵測到危險指標或紅旗症狀！請立即前往急診或撥打119求救！</p>
                    </div>
                </div>
            </div>

            <!-- 就醫指引 -->
            <div class="bg-white rounded-2xl border border-slate-200 p-5 shadow-sm flex-1">
                <h3 class="text-sm font-bold text-slate-900 mb-3 flex items-center gap-1.5">
                    <i data-lucide="map-pin" class="w-4 h-4 text-teal-600"></i>
                    就醫科別指引與關懷
                </h3>
                <div id="guide-content" class="text-xs text-slate-500 space-y-3 leading-relaxed">
                    <div class="border-l-2 border-slate-200 pl-3">
                        <p class="font-semibold text-slate-700 mb-0.5">推薦看診科別：</p>
                        <p id="guide-dept">暫無推薦（請完成問診）</p>
                    </div>
                    <div class="border-l-2 border-slate-200 pl-3">
                        <p class="font-semibold text-slate-700 mb-0.5">關鍵照護指引：</p>
                        <p id="guide-care">請先於左側輸入症狀，我們將為您量身打造居家照護重點。</p>
                    </div>
                    <div class="border-l-2 border-slate-200 pl-3">
                        <p class="font-semibold text-slate-700 mb-0.5">就醫黃金時間：</p>
                        <p id="guide-timeline">評估中...</p>
                    </div>
                </div>
            </div>
        </section>
    </main>

    <!-- 歷史紀錄 Modal -->
    <div id="history-modal" class="hidden fixed inset-0 bg-slate-900/50 backdrop-blur-sm flex items-center justify-center z-50 p-4">
        <div class="bg-white rounded-2xl max-w-lg w-full shadow-xl border border-slate-200 flex flex-col max-h-[80vh]">
            <div class="px-6 py-4 border-b border-slate-200 flex items-center justify-between">
                <h3 class="text-base font-bold text-slate-900 flex items-center gap-2">
                    <i data-lucide="history" class="w-5 h-5 text-teal-500"></i>
                    歷史問診紀錄
                </h3>
                <button onclick="toggleHistoryModal()" class="text-slate-400 hover:text-slate-600">
                    <i data-lucide="x" class="w-6 h-6"></i>
                </button>
            </div>
            <div id="history-list" class="flex-1 overflow-y-auto p-6 space-y-4">
                <!-- 動態注入 -->
                <p class="text-slate-500 text-sm text-center py-8">暫無歷史紀錄</p>
            </div>
            <div class="p-4 bg-slate-50 border-t border-slate-200 rounded-b-2xl flex justify-end gap-2">
                <button onclick="clearHistory()" class="px-4 py-2 border border-red-200 text-red-600 hover:bg-red-50 text-sm font-medium rounded-xl transition">
                    清除全部紀錄
                </button>
                <button onclick="toggleHistoryModal()" class="px-4 py-2 bg-slate-800 hover:bg-slate-900 text-white text-sm font-medium rounded-xl transition">
                    關閉
                </button>
            </div>
        </div>
    </div>

    <!-- 頁尾 -->
    <footer class="bg-slate-950 text-slate-500 text-xs py-6 border-t border-slate-900 mt-auto">
        <div class="max-w-5xl mx-auto px-4 text-center space-y-2">
            <p class="text-slate-400 font-medium">🚨 醫療免責聲明</p>
            <p class="max-w-2xl mx-auto">本問診機器人所提供之所有健康與就醫評估建議，均依據常見症狀之統計篩檢規則進行。本產品無法取代實體醫師之臨床診斷、治療或醫學建議。若您有呼吸困難、持續胸痛、意識不清、劇烈腹痛等嚴重不適，請立即撥打 119 求救或前往鄰近急診室診治。</p>
            <p class="pt-2 text-slate-600">© 2026 安心遠距健康守護網. All Rights Reserved.</p>
        </div>
    </footer>

    <!-- 邏輯腳本 -->
    <script>
        // 狀態管理
        let userData = {
            gender: '',
            age: '',
            symptoms: []
        };
        let chatStep = 0; // 0: 初始/選資料, 1: 詢問具體症狀, 2: 評估完成

        // 初始化 Lucide 圖標
        window.onload = function() {
            lucide.createIcons();
            loadHistory();
        }

        // 選擇生理性別
        function selectGender(gender) {
            userData.gender = gender;
            // 標示已選取
            document.querySelectorAll('.gender-btn').forEach(btn => {
                btn.classList.remove('border-teal-500', 'text-teal-600', 'bg-teal-50');
            });
            event.currentTarget.classList.add('border-teal-500', 'text-teal-600', 'bg-teal-50');
            checkBasicInfoComplete();
        }

        // 選擇年齡層
        function selectAge(age) {
            userData.age = age;
            document.querySelectorAll('.age-btn').forEach(btn => {
                btn.classList.remove('border-teal-500', 'text-teal-600', 'bg-teal-50');
            });
            event.currentTarget.classList.add('border-teal-500', 'text-teal-600', 'bg-teal-50');
            checkBasicInfoComplete();
        }

        // 檢查基本資料是否選擇完畢
        function checkBasicInfoComplete() {
            if (userData.gender && userData.age) {
                // 解鎖輸入框
                const inputEl = document.getElementById('user-input');
                const sendBtn = document.getElementById('send-btn');
                inputEl.removeAttribute('disabled');
                inputEl.placeholder = "請輸入您目前最主要的不舒服或症狀（例如：喉嚨痛、頭暈發燒、骨頭疼痛）...";
                sendBtn.removeAttribute('disabled');
                
                // 顯示常用症狀快選
                document.getElementById('quick-symptoms').classList.remove('hidden');

                // 模擬系統接收
                appendUserMessage(`性別：${userData.gender}，年齡層：${userData.age}`);
                // 隱藏選擇區
                document.getElementById('basic-info-selector').classList.add('hidden');

                setTimeout(() => {
                    appendBotMessage(`感謝您提供基本資料。請問您目前感到最不適的症狀是什麼？持續多久了？如果有其他伴隨症狀（例如：咳嗽、發燒、噁心、關節痛等）也可以一併告訴我。`);
                    chatStep = 1;
                }, 600);
            }
        }

        // 點選常用症狀直接填入輸入框
        function appendSymptom(text) {
            const inputEl = document.getElementById('user-input');
            if (inputEl.value) {
                inputEl.value += '、' + text;
            } else {
                inputEl.value = text;
            }
            inputEl.focus();
        }

        // 新增使用者訊息
        function appendUserMessage(text) {
            const chatBox = document.getElementById('chat-messages');
            const msgHtml = `
                <div class="flex items-start space-x-3 justify-end">
                    <div class="bg-teal-500 text-white rounded-2xl rounded-tr-none px-4 py-3 max-w-[85%] text-sm shadow-sm leading-relaxed">
                        ${text}
                    </div>
                    <div class="w-8 h-8 rounded-full bg-teal-100 text-teal-700 flex items-center justify-center flex-shrink-0 font-bold text-xs">
                        我
                    </div>
                </div>
            `;
            chatBox.insertAdjacentHTML('beforeend', msgHtml);
            chatBox.scrollTop = chatBox.scrollHeight;
        }

        // 新增機器人訊息
        function appendBotMessage(text) {
            const chatBox = document.getElementById('chat-messages');
            const msgHtml = `
                <div class="flex items-start space-x-3">
                    <div class="w-8 h-8 rounded-full bg-teal-100 text-teal-600 flex items-center justify-center flex-shrink-0">
                        <i data-lucide="bot" class="w-5 h-5"></i>
                    </div>
                    <div class="bg-slate-100 text-slate-800 rounded-2xl rounded-tl-none px-4 py-3 max-w-[85%] text-sm shadow-sm leading-relaxed">
                        ${text}
                    </div>
                </div>
            `;
            chatBox.insertAdjacentHTML('beforeend', msgHtml);
            chatBox.scrollTop = chatBox.scrollHeight;
            lucide.createIcons();
        }

        // 處理發送訊息
        function handleSendMessage(event) {
            event.preventDefault();
            const inputEl = document.getElementById('user-input');
            const text = inputEl.value.trim();
            if (!text) return;

            appendUserMessage(text);
            inputEl.value = '';

            // 存入症狀庫
            userData.symptoms.push(text);

            // 顯示正在輸入動畫
            showTypingIndicator();

            setTimeout(() => {
                removeTypingIndicator();
                analyzeSymptomsAndRespond(text);
            }, 1000);
        }

        // 顯示輸入中
        function showTypingIndicator() {
            const chatBox = document.getElementById('chat-messages');
            const indicatorHtml = `
                <div id="typing-indicator" class="flex items-start space-x-3">
                    <div class="w-8 h-8 rounded-full bg-teal-100 text-teal-600 flex items-center justify-center flex-shrink-0">
                        <i data-lucide="bot" class="w-5 h-5"></i>
                    </div>
                    <div class="bg-slate-100 text-slate-800 rounded-2xl rounded-tl-none px-4 py-3 text-sm shadow-sm">
                        <span class="inline-flex gap-1">
                            <span class="w-2 h-2 bg-slate-400 rounded-full animate-bounce"></span>
                            <span class="w-2 h-2 bg-slate-400 rounded-full animate-bounce" style="animation-delay: 0.2s"></span>
                            <span class="w-2 h-2 bg-slate-400 rounded-full animate-bounce" style="animation-delay: 0.4s"></span>
                        </span>
                    </div>
                </div>
            `;
            chatBox.insertAdjacentHTML('beforeend', indicatorHtml);
            chatBox.scrollTop = chatBox.scrollHeight;
            lucide.createIcons();
        }

        // 移除輸入中
        function removeTypingIndicator() {
            const indicator = document.getElementById('typing-indicator');
            if (indicator) indicator.remove();
        }

        // 紅旗症狀（緊急情況）關鍵字庫
        const RED_FLAGS = [
            '胸痛', '胸悶', '喘不過氣', '呼吸困難', '意識模糊', '昏迷', '半邊癱瘓', '口齒不清', '劇烈撕裂痛', '咳血', '大出血', '心絞痛', '高燒不退',
            '流血不止', '大量流血', '頭部撞擊昏迷', '骨折', '骨頭變形', '無法站立', '撞到頭嘔吐', '高處墜落',
            '骨痛發燒', '骨骼劇痛無法動彈', '脊椎劇痛'
        ];

        // 黃旗症狀（需看醫生，非即刻致命）關鍵字庫
        const YELLOW_FLAGS = [
            '發燒', '持續腹痛', '嘔吐不止', '持續拉肚子', '咳嗽超過兩週', '不明原因消瘦', '血尿', '關節腫痛', '耳鳴', '視力模糊',
            '跌倒', '流血', '擦傷', '扭傷', '挫傷', '紅腫', '撞到頭', '撕裂傷',
            '骨頭疼痛', '骨痛', '骨骼痛', '骨頭痛', '關節痛', '關節炎', '酸痛'
        ];

        // 症狀診斷與推薦科別引擎
        function analyzeSymptomsAndRespond(rawText) {
            let severity = 'green'; // 預設安全
            let recommendedDept = '家醫科 / 一般內科';
            let careGuideline = '目前症狀可能較為輕微。建議您先補充足夠水分、多休息，並維持充足睡眠。';
            let timeline = '觀察 1-2 天，若無改善或加劇請預約門診。';
            let replyText = '';

            // 1. 匹配危急紅燈症狀
            let matchedRed = RED_FLAGS.filter(keyword => rawText.includes(keyword));
            let matchedYellow = YELLOW_FLAGS.filter(keyword => rawText.includes(keyword));

            // 如果骨頭痛且伴隨高燒或不明消瘦，直接升級為紅燈/高度警覺
            const hasSevereBonePainFlags = (rawText.includes('骨') || rawText.includes('關節')) && 
                                           (rawText.includes('發燒') || rawText.includes('消瘦') || rawText.includes('體重減輕') || rawText.includes('夜間特別痛') || rawText.includes('劇痛'));

            if (matchedRed.length > 0 || hasSevereBonePainFlags) {
                severity = 'red';
                recommendedDept = '骨科 / 急診醫學科 / 血液腫瘤科';
                careGuideline = `發現疑似高風險骨骼或創傷指標！若伴隨<b>高燒</b>、<b>夜間劇烈疼痛、不明原因體重下降</b>或<b>骨頭變形無法活動</b>，可能是急性骨關節感染、病理性骨折或其他深層病變的表現。`;
                timeline = '<b>請儘速就醫！</b> 建議立即前往醫院急診，或於24小時內掛號大醫院骨科進行詳細影像學與血液學檢查。';
                
                replyText = `⚠️ <b>警告：偵測到潛在的高風險骨骼/關節病變警訊！</b><br><br>
                             您描述的症狀若伴隨深層夜間痛、發燒、或局部嚴重腫脹，可能不是一般的肌肉酸痛：<br>
                             <b>🚨 關鍵警訊：</b><br>
                             1. <b>病理性或感染風險：</b>骨骼深處若有持續且休息無法緩解的痛，需警惕骨髓炎或骨病變。<br>
                             2. <b>立即處置：</b>請儘速前往大型醫院<b>骨科</b>或<b>急診</b>，安排 X 光、MRI 或血液檢查。`;
            } 
            // 2. 匹配黃燈症狀
            else if (matchedYellow.length > 0) {
                severity = 'yellow';
                timeline = '建議在 24-48 小時內進行門診預約。';
                
                // 細分推薦科別
                if (rawText.includes('骨') || rawText.includes('關節') || rawText.includes('酸痛')) {
                    recommendedDept = '骨科 / 風濕免疫科 / 復健科';
                    careGuideline = '<b>🦴 骨骼與關節照護指引：</b><br>1. <b>適度休息與保護：</b>暫時減少患部的負重與劇烈運動，避免二次傷害。<br>2. <b>冷熱敷原則：</b>若為急性發炎紅腫熱痛，24小時內建議間歇冰敷；若為慢性酸痛緊繃，則可適度溫熱敷。<br>3. <b>疼痛記錄：</b>仔細觀察是單一關節痛還是對稱性多關節痛？早上剛起床時是否會僵硬？這對風濕關節炎的判斷非常關鍵。<br>4. <b>補充足夠鈣質：</b>日常可攝取足夠維生素 D3、鈣質與優質蛋白質，維護骨密度。';
                    timeline = '建議在一週內安排<b>骨科</b>（結構性、退化、創傷引起）或<b>風濕免疫科</b>（對稱性關節發炎、晨僵引起）門診評估。';
                    
                    replyText = `💡 <b>評估結果：建議至骨科、風濕免疫科或復健科診治。</b><br><br>
                                 您的「骨頭/關節疼痛」可能源於關節退化、過度使用、運動傷害或骨質流失問題。<br><br>
                                 <b>建議就醫方向：</b><br>
                                 - 若為局部、有明確痛點，可看 <b>骨科 / 復健科</b>。<br>
                                 - 若為全身多處對稱疼痛，且清晨起床時關節感到僵硬，建議看 <b>風濕免疫科</b>。<br><br>
                                 請參考右側的<b>骨骼照護指引</b>，在看診前適度保護患部。`;
                } else if (rawText.includes('發燒') || rawText.includes('咳嗽')) {
                    recommendedDept = '耳鼻喉科 / 感染科 / 呼吸胸腔科';
                    careGuideline = '多量體溫，多補充常溫水。若發燒超過 38.5 度可遵醫囑服用退燒藥。若持續高燒超過三日請盡速就醫。';
                    replyText = `💡 <b>評估結果：建議至耳鼻喉科、感染科或呼吸胸腔科診治。</b><br><br>
                                 您的症狀伴隨發燒或呼吸道不適，建議多喝水休息，並密切監測體溫。若是高燒不退或呼吸急促，請及時就診。`;
                } else if (rawText.includes('腹痛') || rawText.includes('嘔吐') || rawText.includes('拉')) {
                    recommendedDept = '胃腸肝膽科 / 一般內科';
                    careGuideline = '建議暫時禁食一兩餐，等腸胃平息。可以補充電解質水，飲食改為清淡（如稀飯、吐司），避免油膩刺激食物。';
                    replyText = `💡 <b>評估結果：建議至胃腸肝膽科或一般內科診治。</b><br><br>
                                 您的腸胃症狀可能由急性腸胃炎、飲食不潔或消化系統發炎引起。建議清淡飲食，並留意是否有嚴重脫水（如口乾、尿量減少）現象。`;
                } else if (rawText.includes('頭痛') || rawText.includes('視力')) {
                    recommendedDept = '神經內科 / 眼科 / 家醫科';
                    careGuideline = '減少看3C螢幕時間，於安靜、微暗房間休息。若伴隨噴射狀嘔吐，請立刻改掛急診。';
                    replyText = `💡 <b>評估結果：建議至神經內科、眼科或家醫科診治。</b><br><br>
                                 頭痛或視力模糊可能與神經、眼部疲勞或血壓異常有關。請於安靜微暗處充分休息，若伴隨劇烈嘔吐或手腳無力，請立刻就醫。`;
                } else if (rawText.includes('跌倒') || rawText.includes('流血') || rawText.includes('擦傷') || rawText.includes('扭傷') || rawText.includes('挫傷')) {
                    recommendedDept = '一般外科 / 骨科 / 整形外科';
                    careGuideline = '<b>🩹 外傷照護重點：</b><br>1. <b>清潔：</b>使用無菌生理食鹽水將傷口沙塵徹底沖洗乾淨。<br>2. <b>消毒：</b>用優碘由內向外消毒，並可用生理食鹽水擦拭掉避免色素沉澱。<br>3. <b>加壓：</b>使用無菌紗布覆蓋並加壓 5-10 分鐘止血。<br>4. <b>冰敷：</b>若是扭傷或撞擊腫脹，24小時內可間歇冰敷（每次15分鐘）。<br><br><i>⚠️ 備註：若為年長者或嬰幼兒撞到頭，請密切觀察 72 小時是否有噁心、嘔吐、嗜睡、神智不清等腦震盪徵兆。</i>';
                    timeline = '若傷口過深、見骨、邊緣不整齊（可能需要縫合），或 10 分鐘內加壓無法止血，請立刻前往外科門診或急診。';
                    replyText = `💡 <b>評估結果：建議至一般外科、骨科或整形外科處理。</b><br><br>
                                 您描述了跌倒、外傷或流血症狀。請立即參考右側的<b>外傷照護重點</b>進行初步傷口處理。<br><br>
                                 <b>🚨 注意事項：</b><br>
                                 - 如果傷口見骨、裂開較大、或按壓 10 分鐘以上仍血流不止，請立即至最近的<b>急診</b>處置。<br>
                                 - 若是年長者跌倒，請務必觀察後續是否有頭暈、噁心等延遲性腦震盪症狀。`;
                }

                if (!replyText) {
                    replyText = `💡 <b>評估結果：建議至相關科別進行評估。</b><br><br>
                                 您的症狀具有一定程度不適，建議您可以預約 <b>${recommendedDept}</b> 門診，請勿拖延病情。`;
                }
            } 
            // 3. 輕微或不確定症狀 (綠燈)
            else {
                severity = 'green';
                recommendedDept = '家醫科 / 一般內科';
                careGuideline = '建議注意保暖，多喝溫開水。可以適度居家休息，避免過度勞累。密切觀察症狀變化。';
                timeline = '若症狀於 3 天內未見改善，或出現新症狀，請再前往家醫科看診。';
                
                replyText = `✨ <b>評估結果：居家觀察即可。</b><br><br>
                             從您描述的症狀來看，目前暫無明顯的紅旗危急警示。
                             建議您多喝水、保持充足睡眠，並留意這幾天的體溫與精神狀態變化。
                             如果症狀加劇、或開始出現發燒、胸悶不適，請再次使用本機器人評估或直接就醫。`;
            }

            // 更新右側面板與警示燈
            updateStatusDashboard(severity, recommendedDept, careGuideline, timeline);

            // 送出機器人回覆
            appendBotMessage(replyText);

            // 自動保存至本地歷史紀錄
            saveToHistory(severity, rawText, recommendedDept);
        }

        // 更新右側視覺面板
        function updateStatusDashboard(severity, dept, care, timeline) {
            // 隱藏所有燈號
            document.getElementById('status-waiting').classList.add('hidden');
            document.getElementById('status-green').classList.add('hidden');
            document.getElementById('status-yellow').classList.add('hidden');
            document.getElementById('status-red').classList.add('hidden');

            // 顯示當前燈號
            document.getElementById(`status-${severity}`).classList.remove('hidden');

            // 更新文字引導
            document.getElementById('guide-dept').innerText = dept;
            document.getElementById('guide-care').innerHTML = care;
            document.getElementById('guide-timeline').innerHTML = timeline;
        }

        // 重置問診
        function resetChat() {
            userData = { gender: '', age: '', symptoms: [] };
            chatStep = 0;

            const chatMessages = document.getElementById('chat-messages');
            chatMessages.innerHTML = `
                <div class="flex items-start space-x-3">
                    <div class="w-8 h-8 rounded-full bg-teal-100 text-teal-600 flex items-center justify-center flex-shrink-0">
                        <i data-lucide="bot" class="w-5 h-5"></i>
                    </div>
                    <div class="bg-slate-100 text-slate-800 rounded-2xl rounded-tl-none px-4 py-3 max-w-[85%] text-sm shadow-sm leading-relaxed">
                        您好！我是您的「安心健康問診助手」。此系統為健康評估輔助工具，<b>不具備正式醫療診斷功能</b>。若有緊急生命危險，請立即撥打 119！
                        <br><br>
                        為了給您精確的建議，我們首先需要了解您的基本資料。請問您的<b>性別</b>與<b>年齡層</b>是？
                    </div>
                </div>
                <div id="basic-info-selector" class="flex flex-col space-y-4 pl-11">
                    <div>
                        <span class="text-xs text-slate-400 block mb-2 font-medium">1. 選擇生理性別</span>
                        <div class="flex gap-2">
                            <button onclick="selectGender('男')" class="gender-btn px-4 py-2 rounded-xl border border-slate-200 text-sm bg-white hover:border-teal-500 hover:text-teal-600 transition flex items-center gap-1">
                                🚹 男性
                            </button>
                            <button onclick="selectGender('女')" class="gender-btn px-4 py-2 rounded-xl border border-slate-200 text-sm bg-white hover:border-teal-500 hover:text-teal-600 transition flex items-center gap-1">
                                🚺 女性
                            </button>
                            <button onclick="selectGender('其他')" class="gender-btn px-4 py-2 rounded-xl border border-slate-200 text-sm bg-white hover:border-teal-500 hover:text-teal-600 transition">
                                其他/不願透露
                            </button>
                        </div>
                    </div>
                    <div>
                        <span class="text-xs text-slate-400 block mb-2 font-medium">2. 選擇年齡層</span>
                        <div class="grid grid-cols-2 sm:grid-cols-4 gap-2">
                            <button onclick="selectAge('嬰幼兒 (0-12個月)')" class="age-btn px-3 py-2 rounded-xl border border-slate-200 text-xs bg-white hover:border-teal-500 hover:text-teal-600 transition text-center">
                                👶 嬰幼兒
                            </button>
                            <button onclick="selectAge('兒童 (1-12歲)')" class="age-btn px-3 py-2 rounded-xl border border-slate-200 text-xs bg-white hover:border-teal-500 hover:text-teal-600 transition text-center">
                                👦 兒童
                            </button>
                            <button onclick="selectAge('青壯年 (13-64歲)')" class="age-btn px-3 py-2 rounded-xl border border-slate-200 text-xs bg-white hover:border-teal-500 hover:text-teal-600 transition text-center">
                                👨‍🦱 青壯年
                            </button>
                            <button onclick="selectAge('年長者 (65歲以上)')" class="age-btn px-3 py-2 rounded-xl border border-slate-200 text-xs bg-white hover:border-teal-500 hover:text-teal-600 transition text-center">
                                👵 年長者
                            </button>
                        </div>
                    </div>
                </div>
            `;

            // 禁用輸入框
            const inputEl = document.getElementById('user-input');
            inputEl.value = '';
            inputEl.disabled = true;
            inputEl.placeholder = "請先選擇上方基本資料...";
            document.getElementById('send-btn').disabled = true;

            // 隱藏快選症狀
            document.getElementById('quick-symptoms').classList.add('hidden');

            // 重設儀表板
            document.getElementById('status-waiting').classList.remove('hidden');
            document.getElementById('status-green').classList.add('hidden');
            document.getElementById('status-yellow').classList.add('hidden');
            document.getElementById('status-red').classList.add('hidden');
            document.getElementById('guide-dept').innerText = '暫無推薦（請完成問診）';
            document.getElementById('guide-care').innerText = '請先於左側輸入症狀，我們將為您量身打造居家照護重點。';
            document.getElementById('guide-timeline').innerText = '評估中...';

            lucide.createIcons();
        }

        // 保存到歷史紀錄 (Local Storage)
        function saveToHistory(severity, symptomText, dept) {
            let history = JSON.parse(localStorage.getItem('telehealth_history')) || [];
            const record = {
                id: Date.now(),
                time: new Date().toLocaleString('zh-TW'),
                gender: userData.gender,
                age: userData.age,
                symptoms: symptomText,
                severity: severity,
                dept: dept
            };
            history.unshift(record); // 最新放最前
            localStorage.setItem('telehealth_history', JSON.stringify(history));
            loadHistory();
        }

        // 載入歷史紀錄
        function loadHistory() {
            const listContainer = document.getElementById('history-list');
            const history = JSON.parse(localStorage.getItem('telehealth_history')) || [];
            
            if (history.length === 0) {
                listContainer.innerHTML = '<p class="text-slate-500 text-sm text-center py-8">暫無歷史紀錄</p>';
                return;
            }

            let html = '';
            history.forEach(item => {
                let badgeColor = 'bg-emerald-50 text-emerald-700 border-emerald-200';
                let severityText = '🟢 觀察';
                if (item.severity === 'yellow') {
                    badgeColor = 'bg-amber-50 text-amber-700 border-amber-200';
                    severityText = '🟡 門診';
                } else if (item.severity === 'red') {
                    badgeColor = 'bg-red-50 text-red-700 border-red-200';
                    severityText = '🔴 急診';
                }

                html += `
                    <div class="p-4 border border-slate-100 bg-slate-50 rounded-xl space-y-2">
                        <div class="flex justify-between items-center">
                            <span class="text-xs text-slate-400">${item.time}</span>
                            <span class="text-xs px-2.5 py-0.5 rounded-full border font-bold ${badgeColor}">
                                ${severityText}
                            </span>
                        </div>
                        <p class="text-xs text-slate-600"><b>基本資料：</b>${item.gender} (${item.age})</p>
                        <p class="text-xs text-slate-600"><b>描述症狀：</b>${item.symptoms}</p>
                        <p class="text-xs text-teal-700 font-semibold"><b>建議就醫科別：</b>${item.dept}</p>
                    </div>
                `;
            });
            listContainer.innerHTML = html;
        }

        // 清除歷史
        function clearHistory() {
            localStorage.removeItem('telehealth_history');
            loadHistory();
        }

        // 切換歷史 Modal 顯示
        function toggleHistoryModal() {
            const modal = document.getElementById('history-modal');
            modal.classList.toggle('hidden');
        }
    </script>
</body>
</html>
