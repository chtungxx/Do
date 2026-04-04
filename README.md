[MyPrettyMummyyy.html](https://github.com/user-attachments/files/26481703/MyPrettyMummyyy.html)
<!DOCTYPE html>
<html lang="zh-HK">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>健康管理平台</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- FontAwesome -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Tesseract.js -->
    <script src="https://cdn.jsdelivr.net/npm/tesseract.js@5/dist/tesseract.min.js"></script>
    
    <style>
        :root { --safe-top: env(safe-area-inset-top); --safe-bottom: env(safe-area-inset-bottom); }
        body { -webkit-tap-highlight-color: transparent; font-family: -apple-system, system-ui, BlinkMacSystemFont, sans-serif; background-color: #f1f5f9; padding-bottom: calc(80px + var(--safe-bottom)); }
        .card { background: white; border-radius: 1.25rem; box-shadow: 0 1px 3px rgba(0,0,0,0.05); border: 1px solid #e2e8f0; margin-bottom: 1rem; padding: 1.25rem; }
        .btn-active { background: white; color: #2563eb; shadow: 0 4px 6px -1px rgba(0,0,0,0.1); font-weight: 700; }
        input[type="number"] { font-size: 18px !important; } /* 防止 iOS 縮放 */
        .progress-bar { height: 6px; background: #e2e8f0; border-radius: 10px; overflow: hidden; margin: 10px 0; }
        #ocr-inner-progress { width: 0%; height: 100%; background: #3b82f6; transition: width 0.3s; }
    </style>
</head>
<body>

    <!-- Top Nav -->
    <nav class="bg-white/80 backdrop-blur-md border-b sticky top-0 z-40 px-4 py-3">
        <div class="max-w-xl mx-auto flex justify-between items-center">
            <h1 class="text-xl font-extrabold text-blue-600 tracking-tight">健康報告管理</h1>
            <div id="status-dot" class="flex items-center text-[10px] text-slate-400">
                <span class="w-2 h-2 bg-green-500 rounded-full mr-1 animate-pulse"></span> 離線運作中
            </div>
        </div>
    </nav>

    <main class="max-w-xl mx-auto px-4 mt-4">
        
        <!-- ================= 新增畫面 ================= -->
        <div id="view-upload">
            <div class="card">
                <h2 class="text-lg font-bold text-slate-800 mb-3">1. 選擇報告類別</h2>
                <div class="flex p-1 bg-slate-100 rounded-xl gap-1">
                    <button onclick="setCat('cat1')" id="t-cat1" class="flex-1 py-2 text-sm rounded-lg transition-all btn-active">CA125</button>
                    <button onclick="setCat('cat2')" id="t-cat2" class="flex-1 py-2 text-sm rounded-lg transition-all text-slate-500">CBC (血常規)</button>
                    <button onclick="setCat('cat3')" id="t-cat3" class="flex-1 py-2 text-sm rounded-lg transition-all text-slate-500">生化指標</button>
                </div>
            </div>

            <div id="method-selector">
                <!-- 推薦方式 -->
                <div class="card border-blue-200 bg-blue-50/30">
                    <div class="flex items-center mb-3">
                        <i class="fa-brands fa-apple text-xl mr-2 text-slate-700"></i>
                        <h2 class="text-lg font-bold text-slate-800">2. 推薦方式：原況文字貼上</h2>
                        <span class="ml-2 bg-green-500 text-white text-[10px] px-2 py-0.5 rounded-full uppercase">最快最準</span>
                    </div>
                    <p class="text-xs text-slate-500 mb-3">在 iPhone 相簿中打開單據，<b>長按文字並拷貝</b>，然後貼到下面：</p>
                    <textarea id="paste-box" rows="3" class="w-full p-3 border rounded-xl bg-white focus:ring-2 focus:ring-blue-500 outline-none text-sm mb-3" placeholder="按一下這裡選擇「貼上」..."></textarea>
                    <button onclick="doPaste()" class="w-full bg-blue-600 text-white font-bold py-3 rounded-xl shadow-lg active:scale-[0.98] transition-transform">立即分析文字</button>
                </div>

                <!-- 備用方式 -->
                <div class="card border-dashed">
                    <h2 class="text-sm font-bold text-slate-600 mb-3">備用方式：上載圖片識別</h2>
                    <div class="relative border-2 border-dashed border-slate-200 rounded-xl p-6 text-center hover:bg-slate-50">
                        <input type="file" id="img-input" accept="image/*" multiple class="absolute inset-0 opacity-0" onchange="doImgOCR(this)">
                        <i class="fa-solid fa-camera text-2xl text-slate-400 mb-2"></i>
                        <p class="text-sm text-slate-500 font-medium">拍攝單據或選取截圖</p>
                    </div>
                </div>

                <button onclick="showVerify({})" class="w-full py-2 text-sm text-blue-500 font-medium underline">或直接手動輸入</button>
            </div>

            <!-- Loading Area -->
            <div id="ocr-loader" class="hidden card text-center py-8">
                <div class="inline-block animate-spin rounded-full h-8 w-8 border-4 border-blue-500 border-t-transparent mb-4"></div>
                <p id="ocr-text" class="font-bold text-slate-700">正在處理圖片...</p>
                <div class="progress-bar max-w-[200px] mx-auto"><div id="ocr-inner-progress"></div></div>
                <p class="text-xs text-slate-400 mt-2">首次啟動需下載引擎 (約 10秒)，請保持網絡連線</p>
            </div>

            <!-- Verification Area -->
            <div id="verify-area" class="hidden">
                <div class="card">
                    <div class="flex justify-between items-center mb-4">
                        <h2 class="text-lg font-bold">確認數據</h2>
                        <button onclick="resetAll()" class="text-xs text-red-500 font-bold">重新上載</button>
                    </div>
                    
                    <div class="mb-5">
                        <label class="block text-xs font-bold text-slate-400 mb-1 uppercase tracking-widest">報告日期</label>
                        <input type="date" id="r-date" class="w-full p-3 border rounded-xl font-bold bg-slate-50">
                    </div>

                    <div id="metrics-list" class="space-y-3">
                        <!-- Metrics auto-filled here -->
                    </div>

                    <button onclick="saveToDB()" class="w-full mt-6 bg-slate-900 text-white font-extrabold py-4 rounded-2xl shadow-xl active:scale-95 transition-all">儲存健康記錄</button>
                </div>
            </div>
        </div>

        <!-- ================= 圖表畫面 ================= -->
        <div id="view-charts" class="hidden">
            <div class="flex justify-between items-center mb-4">
                <h2 class="text-xl font-bold text-slate-800">數據趨勢</h2>
                <select id="time-filter" onchange="renderCharts()" class="bg-white border rounded-lg text-sm px-2 py-1">
                    <option value="all">所有時間</option>
                    <option value="6m">最近半年</option>
                    <option value="1y">最近一年</option>
                </select>
            </div>
            
            <div id="chart-cat-btns" class="flex p-1 bg-slate-200 rounded-xl gap-1 mb-6">
                <button onclick="setDashCat('cat1')" id="d-cat1" class="flex-1 py-2 text-xs rounded-lg btn-active">CA125</button>
                <button onclick="setDashCat('cat2')" id="d-cat2" class="flex-1 py-2 text-xs rounded-lg">CBC</button>
                <button onclick="setDashCat('cat3')" id="d-cat3" class="flex-1 py-2 text-xs rounded-lg">生化指標</button>
            </div>

            <div id="chart-container" class="space-y-4">
                <!-- Charts here -->
            </div>
        </div>

    </main>

    <!-- Bottom Nav -->
    <nav class="fixed bottom-0 w-full bg-white/90 backdrop-blur-lg border-t flex justify-around items-center py-3 pb-[calc(12px+var(--safe-bottom))] z-50">
        <button onclick="tab('upload')" id="n-upload" class="flex flex-col items-center text-blue-600">
            <i class="fa-solid fa-plus-circle text-2xl"></i>
            <span class="text-[10px] font-bold mt-1">新增記錄</span>
        </button>
        <button onclick="tab('charts')" id="n-charts" class="flex flex-col items-center text-slate-400">
            <i class="fa-solid fa-chart-line text-2xl"></i>
            <span class="text-[10px] font-bold mt-1">數據趨勢</span>
        </button>
    </nav>

    <script>
        // --- 核心數據配置 ---
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

        const regexes = {
            "CA125": /CA125.*?(\d+\.?\d*)/i, "Hemoglobin": /Hemoglobin.*?(\d+\.?\d*)|Hgb.*?(\d+\.?\d*)/i,
            "WBC": /WBC.*?(\d+\.?\d*)/i, "Platelet": /Platelet.*?(\d+\.?\d*)|PLT.*?(\d+\.?\d*)/i,
            "MCV": /MCV.*?(\d+\.?\d*)/i, "MCH": /MCH(?!C).*?(\d+\.?\d*)/i, "MCHC": /MCHC.*?(\d+\.?\d*)/i,
            "Sodium": /Sodium.*?(\d+\.?\d*)/i, "Potassium": /Potassium.*?(\d+\.?\d*)/i, "Creatinine": /Creatinine.*?(\d+\.?\d*)/i,
            "Date": /(\d{4}[-/]\d{1,2}[-/]\d{1,2}|\d{1,2}[-/]\d{1,2}[-/]\d{4})/
        };

        // --- 狀態管理 ---
        let curCat = 'cat1';
        let dashCat = 'cat1';
        let db = JSON.parse(localStorage.getItem('health_db')) || { cat1: [], cat2: [], cat3: [] };
        let charts = [];

        // --- UI 控制 ---
        function tab(t) {
            document.getElementById('view-upload').classList.toggle('hidden', t !== 'upload');
            document.getElementById('view-charts').classList.toggle('hidden', t !== 'charts');
            document.getElementById('n-upload').className = t === 'upload' ? 'flex flex-col items-center text-blue-600' : 'flex flex-col items-center text-slate-400';
            document.getElementById('n-charts').className = t === 'charts' ? 'flex flex-col items-center text-blue-600' : 'flex flex-col items-center text-slate-400';
            if (t === 'charts') renderCharts();
        }

        function setCat(c) {
            curCat = c;
            ['cat1','cat2','cat3'].forEach(i => {
                document.getElementById('t-'+i).className = i === c ? "flex-1 py-2 text-sm rounded-lg transition-all btn-active" : "flex-1 py-2 text-sm rounded-lg transition-all text-slate-500";
            });
            resetAll();
        }

        function setDashCat(c) {
            dashCat = c;
            ['cat1','cat2','cat3'].forEach(i => {
                document.getElementById('d-'+i).className = i === c ? "flex-1 py-2 text-xs rounded-lg btn-active" : "flex-1 py-2 text-xs rounded-lg";
            });
            renderCharts();
        }

        function resetAll() {
            document.getElementById('method-selector').classList.remove('hidden');
            document.getElementById('verify-area').classList.add('hidden');
            document.getElementById('ocr-loader').classList.add('hidden');
            document.getElementById('paste-box').value = "";
        }

        // --- OCR 核心邏輯 ---
        async function doImgOCR(input) {
            if (!input.files.length) return;
            document.getElementById('method-selector').classList.add('hidden');
            document.getElementById('ocr-loader').classList.remove('hidden');
            
            let combinedText = "";
            const progress = document.getElementById('ocr-inner-progress');
            const statusText = document.getElementById('ocr-text');

            try {
                const worker = await Tesseract.createWorker("eng", 1, {
                    logger: m => {
                        if (m.status === 'recognizing text') {
                            progress.style.width = (m.progress * 100) + "%";
                            statusText.innerText = `識別中: ${Math.round(m.progress * 100)}%`;
                        }
                    },
                    errorHandler: e => { throw new Error(e); }
                });

                for (let file of input.files) {
                    const compressed = await compress(file);
                    const { data: { text } } = await worker.recognize(compressed);
                    combinedText += text + "\n";
                }
                await worker.terminate();
                analyze(combinedText);
            } catch (e) {
                alert("識別引擎啟動失敗，請檢查網絡或改用「貼上文字」方式。");
                resetAll();
            }
        }

        function doPaste() {
            const txt = document.getElementById('paste-box').value;
            if (!txt.trim()) return alert("請先貼上文字內容");
            analyze(txt);
        }

        async function compress(file) {
            return new Promise(res => {
                const reader = new FileReader();
                reader.readAsDataURL(file);
                reader.onload = e => {
                    const img = new Image();
                    img.src = e.target.result;
                    img.onload = () => {
                        const canvas = document.createElement('canvas');
                        const scale = Math.min(1, 800 / Math.max(img.width, img.height));
                        canvas.width = img.width * scale;
                        canvas.height = img.height * scale;
                        const ctx = canvas.getContext('2d');
                        ctx.filter = 'contrast(1.2) grayscale(1)';
                        ctx.drawImage(img, 0, 0, canvas.width, canvas.height);
                        res(canvas.toDataURL('image/jpeg', 0.8));
                    };
                };
            });
        }

        function analyze(text) {
            const results = {};
            config[curCat].metrics.forEach(m => {
                const r = regexes[m] || new RegExp(m + ".*?(\\d+\\.?\\d*)", "i");
                const match = text.match(r);
                if (match) results[m] = parseFloat(match[1] || match[2]);
            });

            let date = new Date().toISOString().split('T')[0];
            const dateMatch = text.match(regexes.Date);
            if (dateMatch) date = dateMatch[0].replace(/\//g, '-');

            showVerify(results, date);
        }

        // --- 驗證與儲存 ---
        function showVerify(data, date) {
            document.getElementById('method-selector').classList.add('hidden');
            document.getElementById('ocr-loader').classList.add('hidden');
            document.getElementById('verify-area').classList.remove('hidden');
            
            document.getElementById('r-date').value = date || new Date().toISOString().split('T')[0];
            const list = document.getElementById('metrics-list');
            list.innerHTML = "";

            config[curCat].metrics.forEach(m => {
                const val = data[m] || "";
                const range = ranges[m];
                const isErr = checkAbnormal(m, val);
                
                const item = document.createElement('div');
                item.className = "flex justify-between items-center p-3 border rounded-xl bg-slate-50";
                item.innerHTML = `
                    <div class="flex-1">
                        <p class="text-sm font-bold text-slate-700">${m.replace(/_/g, ' ')}</p>
                        <p class="text-[10px] text-slate-400">參考值: ${range.min || 0} - ${range.max || '-'}</p>
                    </div>
                    <input type="number" step="any" value="${val}" id="in-${m}" oninput="updateColor(this, '${m}')" 
                        class="w-24 p-2 text-right rounded-lg border bg-white font-mono ${isErr ? 'text-red-600 font-extrabold border-red-300' : ''}">
                `;
                list.appendChild(item);
            });
        }

        function checkAbnormal(m, v) {
            if (v === "" || v === undefined) return false;
            const n = parseFloat(v);
            const r = ranges[m];
            if (!r) return false;
            if (r.min !== undefined && n < r.min) return true;
            if (r.max !== undefined && n > r.max) return true;
            return false;
        }

        function updateColor(el, m) {
            if (checkAbnormal(m, el.value)) {
                el.classList.add('text-red-600', 'font-extrabold', 'border-red-300');
            } else {
                el.classList.remove('text-red-600', 'font-extrabold', 'border-red-300');
            }
        }

        function saveToDB() {
            const date = document.getElementById('r-date').value;
            const entry = { date, metrics: {} };
            config[curCat].metrics.forEach(m => {
                const v = document.getElementById('in-'+m).value;
                if (v !== "") entry.metrics[m] = parseFloat(v);
            });

            db[curCat].push(entry);
            db[curCat].sort((a,b) => new Date(a.date) - new Date(b.date));
            localStorage.setItem('health_db', JSON.stringify(db));
            alert("儲存成功！");
            tab('charts');
        }

        // --- 圖表繪製 ---
        function renderCharts() {
            charts.forEach(c => c.destroy());
            charts = [];
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
                container.innerHTML = "<div class='text-center py-10 text-slate-400'>尚無資料</div>";
                return;
            }

            const colors = ['#3b82f6', '#ef4444', '#10b981', '#f59e0b', '#8b5cf6'];
            config[dashCat].metrics.forEach((m, idx) => {
                const points = data.filter(i => i.metrics[m] !== undefined);
                if (!points.length) return;

                const card = document.createElement('div');
                card.className = "card";
                card.innerHTML = `<h3 class='text-xs font-bold text-slate-400 mb-2'>${m.replace(/_/g, ' ')}</h3><div class='h-40'><canvas id='c-${m}'></canvas></div>`;
                container.appendChild(card);

                const ctx = document.getElementById('c-'+m).getContext('2d');
                const chart = new Chart(ctx, {
                    type: 'line',
                    data: {
                        labels: points.map(i => i.date),
                        datasets: [{
                            data: points.map(i => i.metrics[m]),
                            borderColor: colors[idx % colors.length],
                            borderWidth: 2,
                            tension: 0.2,
                            pointBackgroundColor: points.map(i => checkAbnormal(m, i.metrics[m]) ? '#ef4444' : colors[idx % colors.length]),
                            pointRadius: 4
                        }]
                    },
                    options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { display: false } }, scales: { x: { ticks: { font: { size: 9 } } }, y: { ticks: { font: { size: 9 } } } } }
                });
                charts.push(chart);
            });
        }
    </script>
</body>
</html>

