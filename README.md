[醫療報告圖表平台.html](https://github.com/user-attachments/files/26481490/default.html)
<!DOCTYPE html>
<html lang="zh-HK">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>個人健康管理平台</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- FontAwesome -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <style>
        /* iOS Safe Area & Scroll hiding */
        body {
            -webkit-tap-highlight-color: transparent;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
        }
        .hide-scrollbar::-webkit-scrollbar {
            display: none;
        }
        .hide-scrollbar {
            -ms-overflow-style: none;
            scrollbar-width: none;
        }
        input[type="date"]::-webkit-calendar-picker-indicator {
            background: transparent;
            bottom: 0;
            color: transparent;
            cursor: pointer;
            height: auto;
            left: 0;
            position: absolute;
            right: 0;
            top: 0;
            width: auto;
        }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 min-h-screen pb-24">

    <!-- Header -->
    <header class="bg-white shadow-sm sticky top-0 z-50">
        <div class="max-w-3xl mx-auto px-4 py-4 text-center">
            <h1 class="text-xl font-bold text-blue-600"><i class="fa-solid fa-notes-medical mr-2"></i>健康管理平台</h1>
        </div>
    </header>

    <!-- Main Content -->
    <main class="max-w-3xl mx-auto px-4 py-6">
        
        <!-- ================= UPLOAD VIEW ================= -->
        <section id="upload-view" class="block">
            <div class="bg-white rounded-2xl shadow-sm p-5 mb-6">
                <h2 class="text-lg font-semibold mb-4 text-slate-700">新增化驗報告</h2>
                
                <!-- Category Selector -->
                <div class="flex flex-col sm:flex-row gap-2 mb-6 bg-slate-100 p-1 rounded-lg">
                    <button onclick="selectCategory('cat1')" id="btn-cat1" class="flex-1 py-2 px-3 rounded-md text-sm font-medium transition-colors bg-white shadow text-blue-600">類別一: CA125</button>
                    <button onclick="selectCategory('cat2')" id="btn-cat2" class="flex-1 py-2 px-3 rounded-md text-sm font-medium transition-colors text-slate-500 hover:text-slate-700">類別二: CBC</button>
                    <button onclick="selectCategory('cat3')" id="btn-cat3" class="flex-1 py-2 px-3 rounded-md text-sm font-medium transition-colors text-slate-500 hover:text-slate-700">類別三: 生化指標</button>
                </div>

                <p id="category-desc" class="text-sm text-slate-500 mb-6"></p>

                <!-- Upload Area -->
                <div id="upload-area" class="border-2 border-dashed border-blue-200 rounded-xl p-8 text-center bg-blue-50 relative transition-colors hover:bg-blue-100">
                    <input type="file" id="file-input" multiple accept="image/*" class="absolute inset-0 w-full h-full opacity-0 cursor-pointer" onchange="handleFileSelect(event)">
                    <i class="fa-solid fa-cloud-arrow-up text-4xl text-blue-400 mb-3"></i>
                    <p class="text-blue-700 font-medium">點擊上載截圖或拍照</p>
                    <p class="text-sm text-blue-500 mt-1">支援多張截圖同時上載</p>
                </div>
                
                <div id="manual-btn-area" class="mt-4 text-center">
                    <button onclick="startVerification({}, null)" class="text-blue-600 text-sm font-medium underline">或手動輸入數據</button>
                </div>

                <!-- Loading State -->
                <div id="loading-area" class="hidden py-10 text-center">
                    <i class="fa-solid fa-spinner fa-spin text-3xl text-blue-500 mb-3"></i>
                    <p class="text-slate-600 font-medium">AI 正在分析圖片數據，請稍候...</p>
                </div>
            </div>

            <!-- Verification Area (Hidden initially) -->
            <div id="verification-area" class="hidden bg-white rounded-2xl shadow-sm p-5 mb-6 animation-fade-in">
                <div class="flex justify-between items-center mb-2 border-b pb-2">
                    <h2 class="text-lg font-semibold text-slate-700">確認報告數據</h2>
                    <button onclick="resetUploadArea()" class="text-sm text-slate-400 hover:text-slate-600"><i class="fa-solid fa-times"></i> 取消</button>
                </div>
                <p class="text-sm text-slate-500 mb-4">請核對提取的數據。系統不會儲存圖片，只會記錄以下文字資料。異常數值會以紅色標示。</p>
                
                <div class="mb-5">
                    <label class="block text-sm font-medium text-slate-700 mb-1">報告日期 <span class="text-red-500">*</span></label>
                    <div class="relative">
                        <input type="date" id="report-date" required class="w-full border border-slate-300 rounded-lg py-2 px-3 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent text-slate-700">
                        <p id="date-warning" class="text-xs text-red-500 mt-1 hidden"><i class="fa-solid fa-circle-exclamation"></i> 圖片中找不到日期，請手動輸入</p>
                    </div>
                </div>

                <div id="metrics-form" class="space-y-4">
                    <!-- Metrics inputs injected here -->
                </div>

                <button onclick="saveReport()" class="w-full mt-6 bg-blue-600 hover:bg-blue-700 text-white font-bold py-3 px-4 rounded-xl shadow-lg transition-colors flex items-center justify-center">
                    <i class="fa-solid fa-save mr-2"></i> 儲存記錄
                </button>
            </div>
        </section>

        <!-- ================= DASHBOARD VIEW ================= -->
        <section id="dashboard-view" class="hidden">
            
            <div class="flex justify-between items-center mb-4">
                <h2 class="text-xl font-bold text-slate-800">數據趨勢概覽</h2>
                <select id="time-filter" onchange="renderCharts()" class="bg-white border border-slate-300 text-slate-700 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 p-2 shadow-sm">
                    <option value="all">全部時間</option>
                    <option value="1y">最近一年</option>
                    <option value="6m">最近半年</option>
                </select>
            </div>

            <!-- Dashboard Category Selector -->
            <div class="flex flex-col sm:flex-row gap-2 mb-6 bg-white shadow-sm p-1 rounded-lg">
                <button onclick="switchDashCategory('cat1')" id="dash-cat1" class="flex-1 py-2 px-3 rounded-md text-sm font-medium transition-colors bg-blue-100 text-blue-700">類別一 (CA125)</button>
                <button onclick="switchDashCategory('cat2')" id="dash-cat2" class="flex-1 py-2 px-3 rounded-md text-sm font-medium transition-colors text-slate-600 hover:bg-slate-50">類別二 (CBC)</button>
                <button onclick="switchDashCategory('cat3')" id="dash-cat3" class="flex-1 py-2 px-3 rounded-md text-sm font-medium transition-colors text-slate-600 hover:bg-slate-50">類別三 (生化)</button>
            </div>

            <!-- Empty State -->
            <div id="empty-state" class="hidden bg-white rounded-2xl shadow-sm p-10 text-center">
                <i class="fa-solid fa-folder-open text-4xl text-slate-300 mb-3"></i>
                <p class="text-slate-500">尚未有任何記錄</p>
                <button onclick="switchTab('upload')" class="mt-4 text-blue-600 font-medium hover:underline">立即上載報告</button>
            </div>

            <!-- Charts Container -->
            <div id="charts-container" class="space-y-6">
                <!-- Charts injected here -->
            </div>
        </section>

    </main>

    <!-- Bottom Navigation -->
    <nav class="fixed bottom-0 w-full bg-white border-t border-slate-200 pb-safe pt-2 px-6 flex justify-around items-center z-50">
        <button onclick="switchTab('upload')" id="nav-upload" class="flex flex-col items-center p-2 text-blue-600">
            <i class="fa-solid fa-camera text-xl mb-1"></i>
            <span class="text-xs font-medium">上載報告</span>
        </button>
        <button onclick="switchTab('dashboard')" id="nav-dashboard" class="flex flex-col items-center p-2 text-slate-400">
            <i class="fa-solid fa-chart-line text-xl mb-1"></i>
            <span class="text-xs font-medium">趨勢概覽</span>
        </button>
    </nav>

    <!-- Modal Message -->
    <div id="msg-modal" class="fixed inset-0 bg-black/50 z-[100] hidden items-center justify-center p-4">
        <div class="bg-white rounded-2xl p-6 max-w-sm w-full text-center shadow-xl">
            <div id="msg-icon" class="text-4xl mb-4"></div>
            <h3 id="msg-title" class="text-lg font-bold mb-2"></h3>
            <p id="msg-desc" class="text-slate-600 text-sm mb-6"></p>
            <button onclick="document.getElementById('msg-modal').classList.replace('flex', 'hidden')" class="w-full bg-blue-600 text-white font-bold py-2 rounded-lg">確定</button>
        </div>
    </div>

    <script>
        // --- System Data Definition ---
        const apiKey = ""; // Provided by environment at runtime
        
        const referenceRanges = {
            "CA125": { max: 35 },
            "Hemoglobin": { min: 11.9, max: 15.1 },
            "WBC": { min: 4.0, max: 9.7 },
            "Platelet": { min: 150, max: 384 },
            "MCV": { min: 83.0, max: 98.0 },
            "MCH": { min: 28.0, max: 34.0 },
            "MCHC": { min: 32.9, max: 35.3 },
            "RBC": { min: 3.70, max: 4.95 },
            "HCT": { min: 0.35, max: 0.44 },
            "RDW": { min: 12.0, max: 14.7 },
            "MPV": { min: 7.3, max: 10.5 },
            "Neutrophil_absolute": { min: 1.6, max: 7.0 },
            "Neutrophil_pct": { min: 41.0, max: 72.0 },
            "Lymphocyte_absolute": { min: 1.1, max: 2.9 },
            "Lymphocyte_pct": { min: 15.0, max: 44.0 },
            "Monocyte_absolute": { min: 0.3, max: 0.8 },
            "Monocyte_pct": { min: 4.0, max: 11.0 },
            "Eosinophil_absolute": { min: 0.0, max: 0.6 },
            "Eosinophil_pct": { min: 1.0, max: 9.0 },
            "Basophil_absolute": { min: 0.0, max: 0.1 },
            "Basophil_pct": { min: 0.0, max: 1.0 },
            "Sodium": { min: 136, max: 146 },
            "Potassium": { min: 3.5, max: 5.0 },
            "Urea": { min: 2.8, max: 7.2 },
            "Creatinine": { min: 49, max: 90 },
            "Protein_Total": { min: 66, max: 83 },
            "Albumin": { min: 35, max: 52 },
            "Globulin": { min: 26, max: 41 },
            "Bilirubin_Total": { min: 5, max: 21 },
            "Alkaline_Phosphatase": { min: 46, max: 122 },
            "Alanine_Aminotransferase": { max: 47 },
            "Calcium": { min: 2.15, max: 2.55 },
            "Calcium_Adjusted": { min: 2.15, max: 2.55 },
            "Phosphate": { min: 0.81, max: 1.45 }
        };

        const categorySetup = {
            cat1: { name: "CA125 antigen", metrics: ["CA125"] },
            cat2: { name: "CBC", metrics: ["Hemoglobin", "WBC", "Platelet", "MCV", "MCH", "MCHC", "RBC", "HCT", "RDW", "MPV", "Neutrophil_absolute", "Neutrophil_pct", "Lymphocyte_absolute", "Lymphocyte_pct", "Monocyte_absolute", "Monocyte_pct", "Eosinophil_absolute", "Eosinophil_pct", "Basophil_absolute", "Basophil_pct"] },
            cat3: { name: "AP, Ca+PO4, LFT, RFT", metrics: ["Sodium", "Potassium", "Urea", "Creatinine", "Protein_Total", "Albumin", "Globulin", "Bilirubin_Total", "Alkaline_Phosphatase", "Alanine_Aminotransferase", "Calcium", "Calcium_Adjusted", "Phosphate"] }
        };

        const chartColors = [
            '#3b82f6', '#ef4444', '#10b981', '#f59e0b', '#8b5cf6', '#ec4899', 
            '#06b6d4', '#84cc16', '#6366f1', '#f43f5e', '#14b8a6', '#d946ef'
        ];

        let currentTab = 'upload';
        let currentUploadCat = 'cat1';
        let currentDashCat = 'cat1';
        let db = JSON.parse(localStorage.getItem('healthData')) || { cat1: [], cat2: [], cat3: [] };
        let activeChartInstances = [];

        window.onload = () => {
            selectCategory('cat1');
            switchDashCategory('cat1');
        };

        function switchTab(tab) {
            currentTab = tab;
            document.getElementById('upload-view').classList.toggle('hidden', tab !== 'upload');
            document.getElementById('dashboard-view').classList.toggle('hidden', tab !== 'dashboard');
            
            document.getElementById('nav-upload').className = `flex flex-col items-center p-2 ${tab === 'upload' ? 'text-blue-600' : 'text-slate-400'}`;
            document.getElementById('nav-dashboard').className = `flex flex-col items-center p-2 ${tab === 'dashboard' ? 'text-blue-600' : 'text-slate-400'}`;

            if (tab === 'dashboard') {
                renderCharts();
            }
        }

        function selectCategory(cat) {
            currentUploadCat = cat;
            ['cat1', 'cat2', 'cat3'].forEach(c => {
                const btn = document.getElementById(`btn-${c}`);
                if (c === cat) {
                    btn.classList.replace('text-slate-500', 'text-blue-600');
                    btn.classList.replace('hover:text-slate-700', 'bg-white');
                    btn.classList.add('bg-white', 'shadow');
                } else {
                    btn.classList.remove('bg-white', 'shadow');
                    btn.classList.replace('text-blue-600', 'text-slate-500');
                    btn.classList.add('hover:text-slate-700');
                }
            });

            const desc = document.getElementById('category-desc');
            if (cat === 'cat1') desc.innerHTML = "只有一項指標 CA125，你可以上載圖片或直接手動輸入。";
            else if (cat === 'cat2') desc.innerHTML = "包含多項血液細胞指數，強烈建議使用截圖上載以防出錯。";
            else desc.innerHTML = "包含多項生化指標 (AP, Ca, LFT, RFT等)，強烈建議使用截圖上載。";

            document.getElementById('manual-btn-area').style.display = 'block';
            resetUploadArea();
        }

        function switchDashCategory(cat) {
            currentDashCat = cat;
            ['cat1', 'cat2', 'cat3'].forEach(c => {
                const btn = document.getElementById(`dash-${c}`);
                if (c === cat) {
                    btn.className = `flex-1 py-2 px-3 rounded-md text-sm font-medium transition-colors bg-blue-100 text-blue-700`;
                } else {
                    btn.className = `flex-1 py-2 px-3 rounded-md text-sm font-medium transition-colors text-slate-600 hover:bg-slate-50`;
                }
            });
            renderCharts();
        }

        function resetUploadArea() {
            document.getElementById('file-input').value = "";
            document.getElementById('loading-area').classList.add('hidden');
            document.getElementById('upload-area').classList.remove('hidden');
            document.getElementById('verification-area').classList.add('hidden');
            document.getElementById('manual-btn-area').style.display = 'block';
        }

        function showMessage(title, desc, isError = false) {
            document.getElementById('msg-title').innerText = title;
            document.getElementById('msg-desc').innerText = desc;
            document.getElementById('msg-icon').innerHTML = isError ? '<i class="fa-solid fa-circle-xmark text-red-500"></i>' : '<i class="fa-solid fa-circle-check text-green-500"></i>';
            document.getElementById('msg-modal').classList.replace('hidden', 'flex');
        }

        async function handleFileSelect(event) {
            const files = event.target.files;
            if (!files || files.length === 0) return;

            document.getElementById('upload-area').classList.add('hidden');
            document.getElementById('manual-btn-area').style.display = 'none';
            document.getElementById('loading-area').classList.remove('hidden');

            try {
                const base64Files = await Promise.all(Array.from(files).map(file => toBase64(file)));
                await processImagesWithGemini(base64Files);
            } catch (error) {
                console.error(error);
                showMessage("讀取錯誤", "無法處理圖片，請重試或手動輸入。", true);
                resetUploadArea();
            }
        }

        const toBase64 = file => new Promise((resolve, reject) => {
            const reader = new FileReader();
            reader.readAsDataURL(file);
            reader.onload = () => resolve(reader.result.split(',')[1]);
            reader.onerror = error => reject(error);
        });

        async function processImagesWithGemini(base64Images) {
            const metricsList = categorySetup[currentUploadCat].metrics.join(", ");
            const prompt = `
            You are a medical data extraction assistant. I will provide images of a blood test report.
            Please extract the test date and the following specific metrics: ${metricsList}.
            
            Return ONLY a JSON object in this exact format, with no markdown formatting or extra text:
            {
                "date": "YYYY-MM-DD",
                "metrics": {
                    "MetricName": numeric_value
                }
            }
            If the date cannot be found, set "date" to null.
            If a metric cannot be found, omit it from the metrics object.
            Map variations of names (like "%" to "_pct", "Abs" to "_absolute", "ALT" to "Alanine_Aminotransferase") to exactly match the list provided.
            `;

            const imageParts = base64Images.map(base64 => ({
                inlineData: { mimeType: "image/jpeg", data: base64 }
            }));

            const payload = {
                contents: [{
                    role: "user",
                    parts: [{ text: prompt }, ...imageParts]
                }],
                systemInstruction: { parts: [{ text: "You only output strictly valid JSON." }] },
                generationConfig: { responseMimeType: "application/json" }
            };

            let retryCount = 0;
            const maxRetries = 5;
            let success = false;
            let resultData = null;

            while (retryCount < maxRetries && !success) {
                try {
                    const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(payload)
                    });

                    if (!response.ok) throw new Error("API Error");

                    const data = await response.json();
                    const textResult = data.candidates?.[0]?.content?.parts?.[0]?.text;
                    resultData = JSON.parse(textResult);
                    success = true;

                } catch (error) {
                    retryCount++;
                    const delay = Math.pow(2, retryCount) * 1000;
                    await new Promise(r => setTimeout(r, delay));
                }
            }

            document.getElementById('loading-area').classList.add('hidden');

            if (success && resultData) {
                startVerification(resultData.metrics || {}, resultData.date);
            } else {
                showMessage("分析失敗", "無法從圖片提取資訊，請重試或改用手動輸入。", true);
                startVerification({}, null);
            }
        }

        function startVerification(extractedMetrics = {}, extractedDate = null) {
            document.getElementById('upload-area').classList.add('hidden');
            document.getElementById('manual-btn-area').style.display = 'none';
            document.getElementById('loading-area').classList.add('hidden');
            document.getElementById('verification-area').classList.remove('hidden');
            
            const dateInput = document.getElementById('report-date');
            const dateWarning = document.getElementById('date-warning');
            
            if (extractedDate && extractedDate.match(/^\d{4}-\d{2}-\d{2}$/)) {
                dateInput.value = extractedDate;
                dateWarning.classList.add('hidden');
            } else {
                dateInput.value = "";
                dateWarning.classList.remove('hidden');
            }

            const metricsContainer = document.getElementById('metrics-form');
            metricsContainer.innerHTML = '';

            categorySetup[currentUploadCat].metrics.forEach(metric => {
                const val = extractedMetrics[metric] !== undefined ? extractedMetrics[metric] : '';
                const rangeStr = getRangeString(metric);
                
                const div = document.createElement('div');
                div.className = "flex flex-col sm:flex-row sm:items-center justify-between border-b border-slate-100 pb-3";
                
                div.innerHTML = `
                    <div class="mb-2 sm:mb-0">
                        <span class="block text-sm font-semibold text-slate-700">${metric.replace(/_/g, ' ')}</span>
                        <span class="text-xs text-slate-400">參考值: ${rangeStr}</span>
                    </div>
                    <div class="relative w-full sm:w-1/3">
                        <input type="number" step="any" id="input-${metric}" value="${val}" 
                            class="w-full border border-slate-300 rounded-lg py-2 px-3 text-right focus:outline-none focus:ring-2 focus:ring-blue-500 font-mono transition-colors"
                            oninput="checkValueColor('${metric}', this)">
                    </div>
                `;
                metricsContainer.appendChild(div);
                
                const inputEle = document.getElementById(`input-${metric}`);
                if(val !== '') checkValueColor(metric, inputEle);
            });
        }

        function getRangeString(metric) {
            const r = referenceRanges[metric];
            if (!r) return "-";
            if (r.min !== undefined && r.max !== undefined) return `${r.min} - ${r.max}`;
            if (r.min === undefined && r.max !== undefined) return `< ${r.max}`;
            return "-";
        }

        function isAbnormal(metric, value) {
            if (value === '' || isNaN(value)) return false;
            const num = parseFloat(value);
            const r = referenceRanges[metric];
            if (!r) return false;
            if (r.min !== undefined && num < r.min) return true;
            if (r.max !== undefined && num > r.max) return true;
            return false;
        }

        function checkValueColor(metric, inputElement) {
            if (isAbnormal(metric, inputElement.value)) {
                inputElement.classList.add('text-red-600', 'font-bold', 'bg-red-50', 'border-red-300');
                inputElement.classList.remove('text-slate-700');
            } else {
                inputElement.classList.remove('text-red-600', 'font-bold', 'bg-red-50', 'border-red-300');
                inputElement.classList.add('text-slate-700');
            }
        }

        function saveReport() {
            const dateInput = document.getElementById('report-date').value;
            if (!dateInput) {
                showMessage("缺少資料", "請輸入報告日期", true);
                return;
            }

            const newRecord = {
                id: Date.now().toString(),
                date: dateInput,
                metrics: {}
            };

            let hasData = false;
            categorySetup[currentUploadCat].metrics.forEach(metric => {
                const val = document.getElementById(`input-${metric}`).value;
                if (val !== '' && !isNaN(parseFloat(val))) {
                    newRecord.metrics[metric] = parseFloat(val);
                    hasData = true;
                }
            });

            if (!hasData) {
                showMessage("缺少資料", "請至少填寫一個指標的數值", true);
                return;
            }

            db[currentUploadCat].push(newRecord);
            db[currentUploadCat].sort((a, b) => new Date(a.date) - new Date(b.date));
            localStorage.setItem('healthData', JSON.stringify(db));

            showMessage("儲存成功", "資料已成功記錄");
            resetUploadArea();
            switchTab('dashboard');
        }

        function renderCharts() {
            activeChartInstances.forEach(chart => chart.destroy());
            activeChartInstances = [];

            const container = document.getElementById('charts-container');
            container.innerHTML = '';

            const timeFilter = document.getElementById('time-filter').value;
            let categoryData = db[currentDashCat];

            if (timeFilter !== 'all') {
                const cutoff = new Date();
                if (timeFilter === '1y') cutoff.setFullYear(cutoff.getFullYear() - 1);
                if (timeFilter === '6m') cutoff.setMonth(cutoff.getMonth() - 6);
                
                categoryData = categoryData.filter(record => new Date(record.date) >= cutoff);
            }

            if (!categoryData || categoryData.length === 0) {
                document.getElementById('empty-state').classList.remove('hidden');
                container.classList.add('hidden');
                return;
            } else {
                document.getElementById('empty-state').classList.add('hidden');
                container.classList.remove('hidden');
            }

            const metrics = categorySetup[currentDashCat].metrics;
            
            metrics.forEach((metric, index) => {
                const hasDataForMetric = categoryData.some(r => r.metrics[metric] !== undefined);
                if (!hasDataForMetric) return;

                const dates = [];
                const values = [];
                const pointColors = [];
                const pointRadii = [];

                categoryData.forEach(r => {
                    if (r.metrics[metric] !== undefined) {
                        dates.push(r.date);
                        const val = r.metrics[metric];
                        values.push(val);
                        
                        if (isAbnormal(metric, val)) {
                            pointColors.push('#ef4444');
                            pointRadii.push(6);
                        } else {
                            pointColors.push(chartColors[index % chartColors.length]);
                            pointRadii.push(4);
                        }
                    }
                });

                const chartColor = chartColors[index % chartColors.length];
                const card = document.createElement('div');
                card.className = "bg-white p-4 rounded-2xl shadow-sm border border-slate-100";
                
                const latestVal = values[values.length - 1];
                const isLatestAbnormal = isAbnormal(metric, latestVal);
                const latestColorClass = isLatestAbnormal ? 'text-red-500 font-bold' : 'text-slate-700 font-semibold';
                const warningIcon = isLatestAbnormal ? '<i class="fa-solid fa-triangle-exclamation text-red-500 text-xs ml-1"></i>' : '';

                card.innerHTML = `
                    <div class="flex justify-between items-end mb-3">
                        <div>
                            <h3 class="text-sm font-bold text-slate-800">${metric.replace(/_/g, ' ')}</h3>
                            <p class="text-xs text-slate-400">參考值: ${getRangeString(metric)}</p>
                        </div>
                        <div class="text-right">
                            <span class="text-xs text-slate-500 block mb-0.5">最新 (${dates[dates.length - 1]})</span>
                            <span class="text-lg ${latestColorClass}">${latestVal} ${warningIcon}</span>
                        </div>
                    </div>
                    <div class="relative h-48 w-full">
                        <canvas id="chart-${metric}"></canvas>
                    </div>
                `;
                container.appendChild(card);

                const ctx = document.getElementById(`chart-${metric}`).getContext('2d');
                const chart = new Chart(ctx, {
                    type: 'line',
                    data: {
                        labels: dates,
                        datasets: [{
                            label: metric,
                            data: values,
                            borderColor: chartColor,
                            backgroundColor: chartColor + '33',
                            borderWidth: 2,
                            tension: 0.3,
                            pointBackgroundColor: pointColors,
                            pointBorderColor: pointColors,
                            pointRadius: pointRadii,
                            pointHoverRadius: 7,
                            fill: true
                        }]
                    },
                    options: {
                        responsive: true,
                        maintainAspectRatio: false,
                        plugins: {
                            legend: { display: false },
                            tooltip: {
                                backgroundColor: 'rgba(255, 255, 255, 0.9)',
                                titleColor: '#1e293b',
                                bodyColor: '#1e293b',
                                borderColor: '#e2e8f0',
                                borderWidth: 1,
                                padding: 10,
                                displayColors: false,
                                callbacks: {
                                    label: function(context) {
                                        let val = context.parsed.y;
                                        let status = isAbnormal(metric, val) ? ' (異常)' : '';
                                        return `數值: ${val}${status}`;
                                    }
                                }
                            }
                        },
                        scales: {
                            y: {
                                beginAtZero: false,
                                grid: { color: '#f1f5f9' },
                                ticks: { font: { size: 10 }, color: '#94a3b8' }
                            },
                            x: {
                                grid: { display: false },
                                ticks: { font: { size: 10 }, color: '#94a3b8', maxRotation: 45 }
                            }
                        },
                        interaction: {
                            intersect: false,
                            mode: 'index',
                        },
                    }
                });
                
                activeChartInstances.push(chart);
            });
        }

    </script>
</body>
</html>


