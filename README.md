[V20Mum.html](https://github.com/user-attachments/files/26485904/V20Mum.html)
<!DOCTYPE html>
<html lang="zh-HK">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>個人健康管理</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <style>
        :root { --safe-bottom: env(safe-area-inset-bottom); }
        body { -webkit-tap-highlight-color: transparent; font-family: -apple-system, system-ui, sans-serif; background-color: #f4f4f5; padding-bottom: calc(100px + var(--safe-bottom)); }
        .card { background: white; border-radius: 1.5rem; box-shadow: 0 4px 20px -2px rgba(0,0,0,0.05); border: 1px solid #e4e4e7; margin-bottom: 1.25rem; padding: 1.5rem; }
        .btn-active { background: #2563eb; color: white; font-weight: 800; box-shadow: 0 4px 12px rgba(37, 99, 235, 0.3); }
        input[type="number"], input[type="date"] { font-size: 16px !important; }
        .tab-btn { transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1); }
        .progress-fill { height: 100%; background: linear-gradient(90deg, #3b82f6, #60a5fa); transition: width 0.3s ease; }
    </style>
</head>
<body>

    <nav class="bg-white/90 backdrop-blur-xl border-b border-slate-200 sticky top-0 z-40 px-6 py-4 shadow-sm">
        <div class="max-w-xl mx-auto flex justify-between items-center">
            <h1 class="text-xl font-black text-blue-600 tracking-tighter"><i class="fa-solid fa-heart-pulse mr-2 text-red-500"></i>健康管理</h1>
            <div class="flex items-center text-[10px] font-black text-blue-600 bg-blue-50 px-2 py-1 rounded-full uppercase tracking-widest border border-blue-200">
                <i class="fa-solid fa-brain mr-1"></i> 頂級 AI 解鎖版
            </div>
        </div>
    </nav>

    <main class="max-w-xl mx-auto px-4 mt-6">
        
        <!-- ================= 新增記錄視窗 ================= -->
        <div id="view-upload">
            <div class="card p-2 bg-slate-200/50">
                <div class="flex p-1 gap-1">
                    <button onclick="setCat('cat1')" id="t-cat1" class="tab-btn flex-1 py-3 text-sm rounded-xl btn-active">CA125</button>
                    <button onclick="setCat('cat2')" id="t-cat2" class="tab-btn flex-1 py-3 text-sm rounded-xl text-slate-500 font-bold bg-white/50">CBC</button>
                    <button onclick="setCat('cat3')" id="t-cat3" class="tab-btn flex-1 py-3 text-sm rounded-xl text-slate-500 font-bold bg-white/50">生化指標</button>
                </div>
            </div>

            <div id="input-section">
                <!-- 方案 A: 圖片上載 (完美解鎖 AI) -->
                <div class="card border-blue-200 bg-gradient-to-b from-blue-50 to-white">
                    <div class="flex items-center mb-4">
                        <div class="w-12 h-12 bg-blue-600 rounded-2xl flex items-center justify-center text-white mr-4 shadow-lg shadow-blue-200 transform rotate-3">
                            <i class="fa-solid fa-file-image text-2xl"></i>
                        </div>
                        <div>
                            <h2 class="text-lg font-black text-slate-800 tracking-tight">AI 視覺掃描</h2>
                            <p class="text-[11px] text-blue-600 font-bold uppercase tracking-widest mt-0.5">直接閱讀截圖，無視排版錯位</p>
                        </div>
                    </div>
                    
                    <div class="relative border-2 border-dashed border-blue-300 rounded-2xl p-8 text-center hover:bg-blue-50 transition-colors cursor-pointer bg-white">
                        <input type="file" id="img-input" accept="image/*" multiple class="absolute inset-0 opacity-0 cursor-pointer w-full h-full z-10" onchange="startAIVision(this)">
                        <div class="w-16 h-16 bg-blue-100 rounded-full flex items-center justify-center mx-auto mb-4 text-blue-600">
                            <i class="fa-solid fa-cloud-arrow-up text-2xl"></i>
                        </div>
                        <p class="text-base text-slate-700 font-black mb-1">點擊上載單據截圖</p>
                        <p class="text-[11px] text-slate-400 font-bold tracking-widest">系統內建免費 AI，自動提取數字</p>
                    </div>
                </div>

                <!-- 方案 B: 針對醫管局錯位優化的文字貼上 -->
                <div class="card border-dashed border-2">
                    <h2 class="text-sm font-bold text-slate-600 mb-4">備用方案：原況文字貼上</h2>
                    <div class="bg-slate-50 rounded-xl p-3 mb-4 text-xs text-slate-500 font-medium leading-relaxed">
                        已載入「醫健通排版重組演算法」。貼上文字後，系統會自動剔除單位與參考區間。
                    </div>
                    <textarea id="paste-box" rows="4" class="w-full p-4 border border-slate-200 rounded-2xl bg-white focus:border-blue-500 focus:ring-2 outline-none text-sm mb-4 transition-all resize-none shadow-inner" placeholder="長按相片並拷貝文字，貼到此處..."></textarea>
                    <button onclick="processText()" class="w-full bg-slate-800 hover:bg-slate-700 text-white font-black py-4 rounded-2xl shadow-xl active:scale-[0.98] transition-transform">
                        <i class="fa-solid fa-wand-magic-sparkles mr-2 text-yellow-400"></i> 強制重組配對
                    </button>
                </div>

                <div class="text-center mt-6 mb-2">
                    <button onclick="showVerify({}, null)" class="text-sm font-bold text-slate-400 hover:text-slate-600 transition-colors bg-slate-200/50 px-6 py-2 rounded-full">
                        直接手動輸入數據
                    </button>
                </div>
            </div>

            <!-- ================= AI 處理中載入區 ================= -->
            <div id="ai-loader" class="hidden card text-center py-12 bg-slate-50 border-blue-100">
                <div class="relative w-20 h-20 mx-auto mb-6">
                    <div class="absolute inset-0 border-4 border-blue-100 rounded-full"></div>
                    <div class="absolute inset-0 border-4 border-blue-600 rounded-full border-t-transparent animate-spin"></div>
                    <div class="absolute inset-0 flex items-center justify-center text-blue-600">
                        <i class="fa-solid fa-robot text-xl animate-pulse"></i>
                    </div>
                </div>
                <h3 id="ai-status-title" class="text-xl font-black text-slate-800 tracking-tight mb-2">AI 視覺分析中...</h3>
                <p id="ai-status-detail" class="text-xs text-blue-600 font-bold bg-blue-100/50 inline-block px-3 py-1 rounded-lg mb-6">正在理解表格結構</p>
                <div class="w-full h-2.5 bg-slate-200 rounded-full overflow-hidden max-w-[240px] mx-auto mb-4">
                    <div id="ai-progress-bar" class="progress-fill" style="width: 0%"></div>
                </div>
            </div>

            <!-- ================= 驗證與編輯區域 ================= -->
            <div id="verify-area" class="hidden">
                <div class="card border-green-200 bg-green-50/10">
                    <div class="flex justify-between items-center mb-6 border-b border-slate-100 pb-4">
                        <div>
                            <h2 class="text-xl font-black text-slate-800 tracking-tight">核對報告結果</h2>
                            <p class="text-xs text-slate-500 font-medium mt-1">請檢查數值是否正確</p>
                        </div>
                        <button onclick="resetAll()" class="w-10 h-10 rounded-full bg-slate-100 text-slate-500 flex items-center justify-center active:bg-slate-200 transition-colors"><i class="fa-solid fa-rotate-left"></i></button>
                    </div>
                    
                    <div class="mb-8 bg-white p-4 rounded-2xl border border-slate-200 shadow-sm">
                        <label class="block text-[11px] font-black text-slate-400 uppercase tracking-widest mb-2"><i class="fa-regular fa-calendar text-blue-500 mr-1"></i>報告日期 (必需)</label>
                        <input type="date" id="r-date" class="w-full p-3 border-0 bg-slate-50 rounded-xl font-black text-slate-800 focus:ring-2 focus:ring-blue-500 outline-none text-lg">
                    </div>

                    <div id="metrics-list" class="space-y-3">
                        <!-- Javascript 會將配對好的數據插入這裡 -->
                    </div>

                    <button onclick="saveToDB()" class="w-full mt-8 bg-blue-600 text-white font-black py-5 rounded-2xl shadow-xl shadow-blue-200 active:scale-95 transition-all text-lg flex justify-center items-center">
                        <i class="fa-solid fa-check-circle mr-2"></i> 確認儲存記錄
                    </button>
                    
                    <!-- 隱藏的調試工具 -->
                    <details class="mt-8 text-center group">
                        <summary class="text-[10px] text-slate-300 font-bold uppercase tracking-widest cursor-pointer list-none group-hover:text-slate-400 transition-colors">
                            <i class="fa-solid fa-bug mr-1"></i> 開發者：查看系統原始讀取的文字
                        </summary>
                        <pre id="debug-raw-text" class="mt-4 p-4 bg-slate-800 text-green-400 rounded-xl text-left whitespace-pre-wrap overflow-x-auto font-mono text-[10px] leading-relaxed"></pre>
                    </details>
                </div>
            </div>
        </div>

        <!-- ================= 圖表區域 ================= -->
        <div id="view-charts" class="hidden">
            <div class="flex justify-between items-center mb-6">
                <h2 class="text-2xl font-black text-slate-800 tracking-tight">趨勢分析</h2>
                <select id="time-filter" onchange="renderCharts()" class="bg-white border border-slate-200 shadow-sm rounded-xl text-xs font-bold px-4 py-2.5 outline-none text-slate-700">
                    <option value="all">所有時間記錄</option>
                    <option value="6m">最近半年 (6個月)</option>
                    <option value="1y">最近一年 (12個月)</option>
                </select>
            </div>
            
            <div class="card p-2 bg-slate-200/50 mb-6">
                <div class="flex p-1 gap-1">
                    <button onclick="setDashCat('cat1')" id="d-cat1" class="flex-1 py-3 text-xs rounded-xl font-bold transition-all btn-active">CA125</button>
                    <button onclick="setDashCat('cat2')" id="d-cat2" class="flex-1 py-3 text-xs rounded-xl font-bold transition-all text-slate-500 bg-white/50">CBC</button>
                    <button onclick="setDashCat('cat3')" id="d-cat3" class="flex-1 py-3 text-xs rounded-xl font-bold transition-all text-slate-500 bg-white/50">生化</button>
                </div>
            </div>

            <div id="chart-container" class="space-y-5 pb-10"></div>
        </div>

    </main>

    <!-- 底部導航列 -->
    <nav class="fixed bottom-0 w-full bg-white/90 backdrop-blur-2xl border-t border-slate-200 flex justify-around items-center py-3 pb-[calc(12px+var(--safe-bottom))] z-50">
        <button onclick="tab('upload')" id="n-upload" class="flex flex-col items-center text-blue-600 group w-20">
            <div class="p-2 transition-transform group-active:scale-75"><i class="fa-solid fa-house-medical text-2xl"></i></div>
            <span class="text-[10px] font-black uppercase tracking-tighter">新增報告</span>
        </button>
        <button onclick="tab('charts')" id="n-charts" class="flex flex-col items-center text-slate-400 group w-20">
            <div class="p-2 transition-transform group-active:scale-75"><i class="fa-solid fa-chart-line text-2xl"></i></div>
            <span class="text-[10px] font-black uppercase tracking-tighter">數據趨勢</span>
        </button>
    </nav>

    <!-- 錯誤提示彈窗 -->
    <div id="error-modal" class="fixed inset-0 bg-slate-900/60 z-[100] hidden items-center justify-center p-6 backdrop-blur-sm opacity-0 transition-opacity">
        <div class="bg-white rounded-3xl p-6 max-w-sm w-full text-center shadow-2xl transform scale-95 transition-transform" id="error-modal-content">
            <div class="w-16 h-16 bg-red-100 rounded-full flex items-center justify-center mx-auto mb-4 text-red-500">
                <i class="fa-solid fa-triangle-exclamation text-3xl"></i>
            </div>
            <h3 class="text-xl font-black text-slate-800 mb-2">處理失敗</h3>
            <p id="error-msg" class="text-sm text-slate-500 mb-6 leading-relaxed"></p>
            <button onclick="closeError()" class="w-full bg-slate-900 text-white font-black py-4 rounded-xl active:scale-95 transition-transform">我知道了</button>
        </div>
    </div>

    <script>
        // 環境變數注入 API Key (已取消阻擋驗證)
        const apiKey = ""; 
        
        // ================= 核心設定 =================
        const config = {
            cat1: { name: "CA125", metrics: ["CA125"] },
            cat2: { name: "CBC", metrics: ["Hemoglobin", "WBC", "Platelet", "MCV", "MCH", "MCHC", "RBC", "HCT", "RDW", "MPV", "Neutrophil_absolute", "Neutrophil_pct", "Lymphocyte_absolute", "Lymphocyte_pct", "Monocyte_absolute", "Monocyte_pct", "Eosinophil_absolute", "Eosinophil_pct", "Basophil_absolute", "Basophil_pct"] },
            cat3: { name: "Biochem", metrics: ["Sodium", "Potassium", "Urea", "Creatinine", "Protein_Total", "Albumin", "Globulin", "Bilirubin_Total", "Alkaline_Phosphatase", "Alanine_Aminotransferase", "Calcium", "Calcium_Adjusted", "Phosphate"] }
        };

        const ranges = {
            "CA125": { max: 35 },
            "Hemoglobin": { min: 11.9, max: 15.1 }, "WBC": { min: 4.0, max: 9.7 }, "Platelet": { min: 150, max: 384 },
            "MCV": { min: 83.0, max: 98.0 }, "MCH": { min: 28.0, max: 34.0 }, "MCHC": { min: 32.9, max: 35.3 },
            "RBC": { min: 3.70, max: 4.95 }, "HCT": { min: 0.35, max: 0.44 }, "RDW": { min: 12.0, max: 14.7 }, "MPV": { min: 7.3, max: 10.5 },
            "Neutrophil_absolute": { min: 1.6, max: 7.0 }, "Neutrophil_pct": { min: 41.0, max: 72.0 },
            "Lymphocyte_absolute": { min: 1.1, max: 2.9 }, "Lymphocyte_pct": { min: 15.0, max: 44.0 },
            "Monocyte_absolute": { min: 0.3, max: 0.8 }, "Monocyte_pct": { min: 4.0, max: 11.0 },
            "Eosinophil_absolute": { min: 0.0, max: 0.6 }, "Eosinophil_pct": { min: 1.0, max: 9.0 },
            "Basophil_absolute": { min: 0.0, max: 0.1 }, "Basophil_pct": { min: 0.0, max: 1.0 },
            "Sodium": { min: 136, max: 146 }, "Potassium": { min: 3.5, max: 5.0 }, "Urea": { min: 2.8, max: 7.2 }, "Creatinine": { min: 49, max: 90 },
            "Protein_Total": { min: 66, max: 83 }, "Albumin": { min: 35, max: 52 }, "Globulin": { min: 26, max: 41 },
            "Bilirubin_Total": { min: 5, max: 21 }, "Alkaline_Phosphatase": { min: 46, max: 122 }, "Alanine_Aminotransferase": { max: 47 },
            "Calcium": { min: 2.15, max: 2.55 }, "Calcium_Adjusted": { min: 2.15, max: 2.55 }, "Phosphate": { min: 0.81, max: 1.45 }
        };

        const nameMap = {
            "CA125": "CA\\s*[-]?\\s*125", "Hemoglobin": "H[ae]moglobin.*?Blood|Hgb|Hb", "WBC": "WBC", "Platelet": "Platelet|PLT",
            "MCV": "MCV", "MCH": "MCH(?!C)", "MCHC": "MCHC", "RBC": "RBC", "HCT": "HCT", "RDW": "RDW", "MPV": "MPV",
            "Neutrophil_absolute": "Neutrophil.*?absolute", "Neutrophil_pct": "Neutrophil.*?%",
            "Lymphocyte_absolute": "Lymphocyte.*?absolute", "Lymphocyte_pct": "Lymphocyte.*?%",
            "Monocyte_absolute": "Monocyte.*?absolute", "Monocyte_pct": "Monocyte.*?%",
            "Eosinophil_absolute": "Eosinophil.*?absolute", "Eosinophil_pct": "Eosinophil.*?%",
            "Basophil_absolute": "Basophil.*?absolute", "Basophil_pct": "Basophil.*?%",
            "Sodium": "Sodium|Na\\+", "Potassium": "Potassium|K\\+", "Urea": "Urea|BUN", "Creatinine": "Creatinine|Cr",
            "Protein_Total": "Protein.*?Total", "Albumin": "Albumin(?!.*?Adjusted)", "Globulin": "Globulin",
            "Bilirubin_Total": "Bilirubin.*?Total", "Alkaline_Phosphatase": "Alkaline.*?Phosphatase|ALP",
            "Alanine_Aminotransferase": "Alanine.*?Aminotransferase|ALT|SGPT", "Calcium": "Calcium(?!.*?Adjusted)",
            "Calcium_Adjusted": "Calcium.*?Adjusted", "Phosphate": "Phosphate|PO4"
        };

        let curCat = 'cat1', dashCat = 'cat1';
        let db = JSON.parse(localStorage.getItem('health_hub_v7')) || { cat1: [], cat2: [], cat3: [] };
        let activeCharts = [];

        // ================= UI 導航 =================
        function tab(t) {
            document.getElementById('view-upload').classList.toggle('hidden', t !== 'upload');
            document.getElementById('view-charts').classList.toggle('hidden', t !== 'charts');
            document.getElementById('n-upload').className = t === 'upload' ? 'flex flex-col items-center text-blue-600 group w-20' : 'flex flex-col items-center text-slate-300 group w-20';
            document.getElementById('n-charts').className = t === 'charts' ? 'flex flex-col items-center text-blue-600 group w-20' : 'flex flex-col items-center text-slate-300 group w-20';
            if (t === 'charts') renderCharts();
        }

        function setCat(c) {
            curCat = c;
            ['cat1','cat2','cat3'].forEach(i => {
                document.getElementById('t-'+i).className = i===c ? "tab-btn flex-1 py-3 text-sm rounded-xl btn-active" : "tab-btn flex-1 py-3 text-sm rounded-xl text-slate-500 font-bold bg-white/50";
            });
            resetAll();
        }

        function setDashCat(c) {
            dashCat = c;
            ['cat1','cat2','cat3'].forEach(i => {
                document.getElementById('d-'+i).className = i===c ? "flex-1 py-3 text-xs rounded-xl font-bold transition-all btn-active" : "flex-1 py-3 text-xs rounded-xl font-bold transition-all text-slate-500 bg-white/50";
            });
            renderCharts();
        }

        function resetAll() {
            document.getElementById('input-section').classList.remove('hidden');
            document.getElementById('ai-loader').classList.add('hidden');
            document.getElementById('verify-area').classList.add('hidden');
            document.getElementById('paste-box').value = "";
            document.getElementById('img-input').value = "";
        }

        function showError(msg) {
            document.getElementById('error-msg').innerText = msg;
            const modal = document.getElementById('error-modal');
            const content = document.getElementById('error-modal-content');
            modal.classList.remove('hidden');
            modal.classList.add('flex');
            setTimeout(() => {
                modal.classList.remove('opacity-0');
                content.classList.remove('scale-95');
            }, 10);
            resetAll();
        }

        function closeError() {
            const modal = document.getElementById('error-modal');
            const content = document.getElementById('error-modal-content');
            modal.classList.add('opacity-0');
            content.classList.add('scale-95');
            setTimeout(() => {
                modal.classList.add('hidden');
                modal.classList.remove('flex');
            }, 300);
        }

        // ================= 方案 A：Gemini 視覺 AI 處理 =================
        async function compressAndEncodeImage(file) {
            return new Promise((resolve, reject) => {
                const objectUrl = URL.createObjectURL(file);
                const img = new Image();
                img.onload = () => {
                    URL.revokeObjectURL(objectUrl);
                    const canvas = document.createElement('canvas');
                    const MAX_SIZE = 1500; 
                    let width = img.width; let height = img.height;
                    if (width > height && width > MAX_SIZE) { height *= MAX_SIZE / width; width = MAX_SIZE; } 
                    else if (height > MAX_SIZE) { width *= MAX_SIZE / height; height = MAX_SIZE; }
                    canvas.width = width; canvas.height = height;
                    const ctx = canvas.getContext('2d');
                    ctx.drawImage(img, 0, 0, width, height);
                    const base64Data = canvas.toDataURL('image/jpeg', 0.85).split(',')[1]; 
                    canvas.width = 0; canvas.height = 0; 
                    resolve(base64Data);
                };
                img.onerror = reject;
                img.src = objectUrl;
            });
        }

        async function startAIVision(input) {
            if (!input.files || input.files.length === 0) return;

            document.getElementById('input-section').classList.add('hidden');
            const loader = document.getElementById('ai-loader');
            loader.classList.remove('hidden');
            
            const titleEl = document.getElementById('ai-status-title');
            const detailEl = document.getElementById('ai-status-detail');
            const progressEl = document.getElementById('ai-progress-bar');
            
            try {
                titleEl.innerText = "正在準備相片";
                detailEl.innerText = "極速壓縮中...";
                progressEl.style.width = '20%';
                
                let base64Images = [];
                for (let i=0; i<input.files.length; i++) {
                    base64Images.push(await compressAndEncodeImage(input.files[i]));
                }

                titleEl.innerText = "呼叫視覺 AI";
                detailEl.innerText = "正在分析表格排版，大約需時 5-8 秒...";
                progressEl.style.width = '60%';

                const metricsToFind = config[curCat].metrics;
                const promptText = `你是一個醫療數據提取專家。我會提供化驗報告的截圖。
這些表格的格式可能會因為排版而導致名稱與數值錯開，請你運用視覺理解將它們精準配對。
請提取「報告日期」以及以下具體指標的數值：${metricsToFind.join(", ")}
注意：
1. 排除括號內的參考區間（例如不要提取 11.9-15.1）。
2. 返回純 JSON 格式，不要包含任何 Markdown 標記，直接以 { 開始。
期望的 JSON 格式：
{ "date": "YYYY-MM-DD", "metrics": { "指標名稱": 數字 } }`;

                const imageParts = base64Images.map(b64 => ({ inlineData: { mimeType: "image/jpeg", data: b64 } }));
                const payload = {
                    contents: [{ role: "user", parts: [{ text: promptText }, ...imageParts] }],
                    systemInstruction: { parts: [{ text: "You are a precise data extraction API. Only output valid JSON without markdown formatting." }] },
                    generationConfig: { responseMimeType: "application/json" }
                };

                const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`;
                let responseData = null;
                let retries = 0;
                const delays = [1000, 2000, 4000, 8000, 16000];

                while (retries < 5) {
                    try {
                        const res = await fetch(url, {
                            method: 'POST',
                            headers: { 'Content-Type': 'application/json' },
                            body: JSON.stringify(payload)
                        });
                        
                        if (!res.ok) throw new Error(`API 錯誤: ${res.status}`);
                        const data = await res.json();
                        const resultText = data.candidates?.[0]?.content?.parts?.[0]?.text;
                        if (!resultText) throw new Error("無效的 AI 回應");
                        
                        responseData = JSON.parse(resultText);
                        break; 
                    } catch (err) {
                        retries++;
                        if (retries >= 5) throw new Error("AI 伺服器忙碌，重試達上限。請改用「原況文字貼上」功能。");
                        detailEl.innerText = `伺服器處理中，正在重試 (${retries}/5)...`;
                        await new Promise(r => setTimeout(r, delays[retries-1]));
                    }
                }

                progressEl.style.width = '100%';
                setTimeout(() => showVerify(responseData.metrics || {}, responseData.date), 500);

            } catch (error) {
                console.error(error);
                showError(error.message);
            }
        }

        // ================= 方案 B：針對醫管局錯位優化的重組演算法 =================
        function processText() {
            const text = document.getElementById('paste-box').value;
            if (!text.trim()) { alert("請先貼上文字！"); return; }
            
            document.getElementById('debug-raw-text').innerText = text;
            
            let cleanText = text;
            // 1. 移除 (11.9 - 15.1) 這類參考區間
            cleanText = cleanText.replace(/[(（][\d\.\s\-<>]+[)）]/g, ' '); 
            // 2. 移除日期，避免干擾數字抓取
            cleanText = cleanText.replace(/\d{4}\s*年\s*\d{1,2}\s*月\s*\d{1,2}\s*日/g, ' '); 
            cleanText = cleanText.replace(/\d{1,2}[-/]\d{1,2}[-/]\d{4}/g, ' ');
            cleanText = cleanText.replace(/\d{4}[-/]\d{1,2}[-/]\d{1,2}/g, ' ');
            // 3. 移除科學記號單位 x10^9/L, % 等
            cleanText = cleanText.replace(/x10\^[-+]?\d+/gi, ' '); 
            cleanText = cleanText.replace(/[a-zA-Z\/%]+(?![,])/g, ' '); // 移除純字母單位，但保留名稱中的逗號

            // 找出名稱
            let detectedNames = [];
            config[curCat].metrics.forEach(m => {
                let reg = new RegExp(nameMap[m] || m, 'i');
                let match = text.match(reg);
                if(match) detectedNames.push({name: m, index: match.index});
            });
            detectedNames.sort((a,b) => a.index - b.index);

            // 找出獨立的數字
            let foundNumbers = [];
            let numRegex = /\b\d+(?:\.\d+)?\b/g;
            let nMatch;
            while((nMatch = numRegex.exec(cleanText)) !== null) {
                foundNumbers.push(parseFloat(nMatch[0]));
            }

            // 拉鍊式結合：按出現順序，把名字跟數字一對一配起來
            let results = {};
            for(let i=0; i<Math.min(detectedNames.length, foundNumbers.length); i++){
                results[detectedNames[i].name] = foundNumbers[i];
            }

            // 擷取日期
            let date = new Date().toISOString().split('T')[0];
            const zhDateMatch = text.match(/(\d{4})\s*年\s*(\d{1,2})\s*月\s*(\d{1,2})\s*日/);
            if (zhDateMatch) {
                date = `${zhDateMatch[1]}-${zhDateMatch[2].padStart(2, '0')}-${zhDateMatch[3].padStart(2, '0')}`;
            } else {
                const slashDateMatch = text.match(/(\d{4}[-/]\d{1,2}[-/]\d{1,2}|\d{1,2}[-/]\d{1,2}[-/]\d{4})/);
                if (slashDateMatch) {
                    let d = slashDateMatch[0].replace(/\//g, '-');
                    if (d.match(/^\d{1,2}-\d{1,2}-\d{4}$/)) {
                        const parts = d.split('-');
                        d = `${parts[2]}-${parts[1].padStart(2,'0')}-${parts[0].padStart(2,'0')}`;
                    }
                    date = d;
                }
            }

            showVerify(results, date);
        }

        // ================= 顯示結果與核對 =================
        function showVerify(data, date) {
            document.getElementById('input-section').classList.add('hidden');
            document.getElementById('ai-loader').classList.add('hidden');
            const area = document.getElementById('verify-area');
            area.classList.remove('hidden');
            
            document.getElementById('r-date').value = date || new Date().toISOString().split('T')[0];
            const list = document.getElementById('metrics-list');
            list.innerHTML = "";

            config[curCat].metrics.forEach(m => {
                const val = data[m] !== undefined ? data[m] : "";
                const range = ranges[m];
                const isAbn = checkAbn(m, val);
                
                const div = document.createElement('div');
                div.className = "flex justify-between items-center p-4 border border-slate-200 rounded-2xl bg-white shadow-sm";
                div.innerHTML = `
                    <div class="flex-1 pr-3">
                        <p class="text-[14px] font-black text-slate-800 leading-tight">${m.replace(/_/g, ' ')}</p>
                        <div class="flex items-center mt-1">
                            <span class="bg-slate-100 text-slate-500 text-[10px] px-2 py-0.5 rounded-md font-bold tracking-wider">REF</span>
                            <span class="text-[11px] text-slate-500 font-bold ml-1.5">${range.min !== undefined ? range.min : 0} - ${range.max !== undefined ? range.max : '無上限'}</span>
                        </div>
                    </div>
                    <div class="relative">
                        <input type="number" step="any" value="${val}" id="in-${m}" oninput="updateInColor(this, '${m}')" 
                            class="w-24 p-3 text-right rounded-xl font-mono shadow-inner text-lg outline-none transition-all ${isAbn ? 'text-red-600 font-black border-2 border-red-300 bg-red-50' : 'text-blue-700 font-bold border border-slate-200 bg-slate-50 focus:border-blue-500 focus:bg-white'}">
                    </div>
                `;
                list.appendChild(div);
            });
            
            window.scrollTo({ top: 0, behavior: 'smooth' });
        }

        function checkAbn(m, v) {
            if (v === "" || isNaN(v)) return false;
            const n = parseFloat(v), r = ranges[m];
            return (r.min !== undefined && n < r.min) || (r.max !== undefined && n > r.max);
        }

        function updateInColor(el, m) {
            if (checkAbn(m, el.value)) {
                el.className = "w-24 p-3 text-right rounded-xl font-mono shadow-inner text-lg outline-none transition-all text-red-600 font-black border-2 border-red-300 bg-red-50";
            } else {
                el.className = "w-24 p-3 text-right rounded-xl font-mono shadow-inner text-lg outline-none transition-all text-blue-700 font-bold border border-slate-200 bg-slate-50 focus:border-blue-500 focus:bg-white";
            }
        }

        function saveToDB() {
            const date = document.getElementById('r-date').value;
            if (!date) { showError("請輸入有效的報告日期"); return; }
            
            const entry = { date, metrics: {} };
            let hasData = false;
            config[curCat].metrics.forEach(m => {
                const v = document.getElementById('in-'+m).value;
                if (v !== "") { entry.metrics[m] = parseFloat(v); hasData = true; }
            });

            if (!hasData) { showError("無法儲存：請至少填寫一個指標數值。"); return; }
            
            db[curCat].push(entry);
            db[curCat].sort((a,b) => new Date(a.date) - new Date(b.date));
            localStorage.setItem('health_hub_v7', JSON.stringify(db));
            
            resetAll();
            tab('charts');
        }

        // ================= 圖表與數據可視化 =================
        function renderCharts() {
            activeCharts.forEach(c => c.destroy());
            activeCharts = [];
            const container = document.getElementById('chart-container');
            container.innerHTML = "";

            let data = db[dashCat];
            const filter = document.getElementById('time-filter').value;
            if (filter !== 'all') {
                const limit = new Date();
                if (filter === '6m') limit.setMonth(limit.getMonth() - 6);
                if (filter === '1y') limit.setFullYear(limit.getFullYear() - 1);
                data = data.filter(i => new Date(i.date) >= limit);
            }

            if (!data.length) {
                container.innerHTML = `
                    <div class='text-center py-24 bg-white rounded-3xl border-2 border-dashed border-slate-200 m-2'>
                        <div class='w-16 h-16 bg-slate-100 rounded-full flex items-center justify-center mx-auto mb-4'>
                            <i class='fa-solid fa-file-medical text-slate-300 text-2xl'></i>
                        </div>
                        <h3 class='text-lg font-black text-slate-600'>尚無數據記錄</h3>
                        <p class='text-sm text-slate-400 mt-1'>請前往「新增報告」添加你的化驗資料</p>
                    </div>`;
                return;
            }

            const colors = ['#3b82f6', '#ef4444', '#10b981', '#f59e0b', '#8b5cf6'];
            
            config[dashCat].metrics.forEach((m, idx) => {
                const pts = data.filter(i => i.metrics[m] !== undefined);
                if (!pts.length) return;

                const latest = pts[pts.length-1].metrics[m];
                const abn = checkAbn(m, latest);
                
                const card = document.createElement('div');
                card.className = "card border-0 shadow-md ring-1 ring-slate-100";
                
                card.innerHTML = `
                    <div class="flex justify-between items-start mb-5">
                        <div>
                            <h3 class='text-[15px] font-black text-slate-800 leading-none'>${m.replace(/_/g, ' ')}</h3>
                            <p class="text-[10px] text-slate-400 font-bold uppercase tracking-wider mt-2">參考值: ${ranges[m].min !== undefined ? ranges[m].min : 0} - ${ranges[m].max !== undefined ? ranges[m].max : '無上限'}</p>
                        </div>
                        <div class="text-right bg-slate-50 px-3 py-2 rounded-xl">
                            <p class="text-[9px] text-slate-400 font-black mb-0.5 uppercase tracking-tighter">最新數值</p>
                            <span class="text-2xl font-black ${abn ? 'text-red-500' : 'text-blue-600'}">${latest}</span>
                        </div>
                    </div>
                    <div class='h-48 w-full'><canvas id='c-${m}'></canvas></div>
                `;
                container.appendChild(card);

                const ctx = document.getElementById('c-'+m).getContext('2d');
                activeCharts.push(new Chart(ctx, {
                    type: 'line',
                    data: {
                        labels: pts.map(i => {
                            const d = new Date(i.date);
                            return `${d.getMonth()+1}/${d.getDate()}`;
                        }),
                        datasets: [{
                            data: pts.map(i => i.metrics[m]),
                            borderColor: colors[idx % colors.length],
                            borderWidth: 3,
                            tension: 0.4,
                            pointBackgroundColor: pts.map(i => checkAbn(m, i.metrics[m]) ? '#ef4444' : '#ffffff'),
                            pointBorderColor: colors[idx % colors.length],
                            pointBorderWidth: 2,
                            pointRadius: 5,
                            pointHoverRadius: 7,
                            fill: true,
                            backgroundColor: (context) => {
                                const chart = context.chart;
                                const {ctx, chartArea} = chart;
                                if (!chartArea) return;
                                const gradient = ctx.createLinearGradient(0, chartArea.top, 0, chartArea.bottom);
                                gradient.addColorStop(0, colors[idx % colors.length] + '33');
                                gradient.addColorStop(1, colors[idx % colors.length] + '00');
                                return gradient;
                            }
                        }]
                    },
                    options: { 
                        responsive: true, 
                        maintainAspectRatio: false, 
                        plugins: { legend: { display: false } }, 
                        scales: { 
                            x: { grid: { display: false }, ticks: { font: { size: 10, weight: 'bold', family: 'system-ui' }, color: '#94a3b8' } }, 
                            y: { border: { dash: [4, 4] }, grid: { color: '#f1f5f9' }, ticks: { font: { size: 11, weight: 'bold', family: 'system-ui' }, color: '#94a3b8' } } 
                        },
                        interaction: { intersect: false, mode: 'index' }
                    }
                }));
            });
        }
    </script>
</body>
</html>


