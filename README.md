[MummyPretty.html](https://github.com/user-attachments/files/26481618/MummyPretty.html)
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
    <!-- Tesseract.js v5 -->
    <script src="https://cdn.jsdelivr.net/npm/tesseract.js@5/dist/tesseract.min.js"></script>
    
    <style>
        body {
            -webkit-tap-highlight-color: transparent;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            background-color: #f8fafc;
        }
        .hide-scrollbar::-webkit-scrollbar { display: none; }
        .hide-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
        input[type="date"]::-webkit-calendar-picker-indicator {
            background: transparent; bottom: 0; color: transparent; cursor: pointer;
            height: auto; left: 0; position: absolute; right: 0; top: 0; width: auto;
        }
        .progress-bar-container {
            width: 100%; background-color: #e2e8f0; border-radius: 9999px;
            height: 0.5rem; margin-top: 0.5rem; overflow: hidden;
        }
        .progress-bar {
            height: 100%; background-color: #3b82f6; width: 0%; transition: width 0.3s ease;
        }
        /* 避免 iOS 輸入框放大 */
        input, select, textarea { font-size: 16px !important; }
    </style>
</head>
<body class="text-slate-800 min-h-screen pb-24">

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
            <div class="bg-white rounded-2xl shadow-sm p-5 mb-6 border border-slate-100">
                <h2 class="text-lg font-bold mb-4 text-slate-800 border-b pb-2">新增化驗報告</h2>
                
                <!-- Category Selector -->
                <div class="flex flex-col sm:flex-row gap-2 mb-4 bg-slate-100 p-1.5 rounded-lg">
                    <button onclick="selectCategory('cat1')" id="btn-cat1" class="flex-1 py-2.5 px-3 rounded-md text-sm font-bold transition-all shadow bg-white text-blue-600">類別一: CA125</button>
                    <button onclick="selectCategory('cat2')" id="btn-cat2" class="flex-1 py-2.5 px-3 rounded-md text-sm font-bold transition-all text-slate-500 hover:text-slate-700">類別二: CBC</button>
                    <button onclick="selectCategory('cat3')" id="btn-cat3" class="flex-1 py-2.5 px-3 rounded-md text-sm font-bold transition-all text-slate-500 hover:text-slate-700">類別三: 生化指標</button>
                </div>

                <p id="category-desc" class="text-sm text-slate-500 mb-6 px-1"></p>

                <!-- Input Methods Area -->
                <div id="input-methods-area" class="space-y-5">
                    
                    <!-- Method 1: Image Upload -->
                    <div class="border-2 border-dashed border-blue-200 rounded-xl p-6 text-center bg-blue-50 relative transition-colors hover:bg-blue-100 cursor-pointer">
                        <input type="file" id="file-input" multiple accept="image/*" class="absolute inset-0 w-full h-full opacity-0 cursor-pointer" onchange="handleFileSelect(event)">
                        <i class="fa-solid fa-image text-3xl text-blue-400 mb-2"></i>
                        <p class="text-blue-700 font-bold">1. 選擇或拍攝報告圖片</p>
                        <p class="text-xs text-blue-500 mt-1">內建自動壓縮與強化，提取數字</p>
                    </div>

                    <div class="flex items-center justify-between">
                        <div class="h-px bg-slate-200 flex-1"></div>
                        <span class="px-3 text-xs text-slate-400 font-bold">或</span>
                        <div class="h-px bg-slate-200 flex-1"></div>
                    </div>

                    <!-- Method 2: iOS Live Text Paste (Highly Recommended for iOS) -->
                    <div class="border border-slate-200 rounded-xl p-4 bg-white shadow-sm focus-within:border-blue-400 focus-within:ring-1 focus-within:ring-blue-400">
                        <label class="block text-sm font-bold text-slate-700 mb-2">
                            <i class="fa-brands fa-apple text-slate-500 mr-1"></i> 2. 貼上相片內的文字 <span class="bg-green-100 text-green-700 text-[10px] px-2 py-0.5 rounded ml-2">最準確</span>
                        </label>
                        <textarea id="raw-text-input" rows="3" class="w-full text-sm p-3 border border-slate-200 bg-slate-50 rounded-lg focus:ring-0 resize-none text-slate-600 mb-3" placeholder="在 iPhone/iPad 照片中長按單據並「拷貝」所有文字，然後貼上到這裡..."></textarea>
                        <button onclick="processPastedText()" class="w-full bg-slate-800 hover:bg-slate-700 text-white text-sm font-bold py-2.5 rounded-lg transition-colors">
                            分析貼上的文字
                        </button>
                    </div>

                    <div class="flex items-center justify-between">
                        <div class="h-px bg-slate-200 flex-1"></div>
                        <span class="px-3 text-xs text-slate-400 font-bold">或</span>
                        <div class="h-px bg-slate-200 flex-1"></div>
                    </div>

                    <!-- Method 3: Manual Input -->
                    <div class="text-center pt-2">
                        <button onclick="startVerification()" class="text-blue-600 text-sm font-bold hover:underline py-2 px-6 bg-blue-50 rounded-full">
                            <i class="fa-solid fa-pen mr-1"></i> 3. 完全手動輸入所有數據
                        </button>
                    </div>
                </div>

                <!-- Loading State -->
                <div id="loading-area" class="hidden py-10 text-center">
                    <i class="fa-solid fa-microchip text-4xl text-blue-500 mb-4 animate-bounce"></i>
                    <p class="text-slate-800 font-bold text-lg mb-1">正在處理中...</p>
                    <p id="ocr-status" class="text-sm text-slate-500 mb-3">準備壓縮圖片以提升速度</p>
                    <div class="progress-bar-container max-w-xs mx-auto">
                        <div id="ocr-progress" class="progress-bar"></div>
                    </div>
                </div>
            </div>

            <!-- Verification Area (Hidden initially) -->
            <div id="verification-area" class="hidden bg-white rounded-2xl shadow-sm p-5 mb-6 border border-slate-100">
                <div class="flex justify-between items-center mb-4 border-b pb-3">
                    <h2 class="text-lg font-bold text-slate-800"><i class="fa-solid fa-list-check text-blue-500 mr-2"></i>確認數據</h2>
                    <button onclick="resetUploadArea()" class="text-sm text-slate-500 hover:text-red-500 bg-slate-100 px-3 py-1 rounded-full"><i class="fa-solid fa-arrow-left mr-1"></i>重選</button>
                </div>
                
                <div class="bg-blue-50 p-3 rounded-lg mb-5 border border-blue-100 text-sm text-blue-800">
                    請仔細核對自動提取的數據，如有空白或錯誤，**請直接點擊數字進行修改**。
                </div>
                
                <div class="mb-6 bg-white border border-slate-200 p-4 rounded-xl shadow-sm">
                    <label class="block text-sm font-bold text-slate-700 mb-2">報告日期 <span class="text-red-500">*</span></label>
                    <div class="relative">
                        <input type="date" id="report-date" required class="w-full border border-slate-300 rounded-lg py-2.5 px-3 focus:outline-none focus:ring-2 focus:ring-blue-500 text-slate-800 font-bold">
                        <p id="date-warning" class="text-xs text-red-500 mt-2 font-medium hidden"><i class="fa-solid fa-circle-exclamation"></i> 未找到日期，已暫時預設為今日，請更正</p>
                    </div>
                </div>

                <div id="metrics-form" class="space-y-4">
                    <!-- Metrics inputs injected here -->
                </div>

                <button onclick="saveReport()" class="w-full mt-8 bg-blue-600 hover:bg-blue-700 text-white font-bold py-3.5 px-4 rounded-xl shadow-md transition-colors flex items-center justify-center text-lg">
                    <i class="fa-solid fa-save mr-2"></i> 儲存此份報告
                </button>
            </div>
        </section>

        <!-- ================= DASHBOARD VIEW ================= -->
        <section id="dashboard-view" class="hidden">
            <div class="flex justify-between items-center mb-5">
                <h2 class="text-xl font-bold text-slate-800"><i class="fa-solid fa-chart-line text-blue-500 mr-2"></i>數據趨勢</h2>
                <select id="time-filter" onchange="renderCharts()" class="bg-white border border-slate-300 text-slate-700 font-medium text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 p-2 shadow-sm">
                    <option value="all">全部時間</option>
                    <option value="1y">最近一年</option>
                    <option value="6m">最近半年</option>
                </select>
            </div>

            <!-- Dashboard Category Selector -->
            <div class="flex flex-col sm:flex-row gap-2 mb-6 bg-slate-100 p-1.5 rounded-lg">
                <button onclick="switchDashCategory('cat1')" id="dash-cat1" class="flex-1 py-2 px-3 rounded-md text-sm font-bold transition-all bg-white shadow text-blue-600">類別一: CA125</button>
                <button onclick="switchDashCategory('cat2')" id="dash-cat2" class="flex-1 py-2 px-3 rounded-md text-sm font-bold transition-all text-slate-500 hover:text-slate-700">類別二: CBC</button>
                <button onclick="switchDashCategory('cat3')" id="dash-cat3" class="flex-1 py-2 px-3 rounded-md text-sm font-bold transition-all text-slate-500 hover:text-slate-700">類別三: 生化</button>
            </div>

            <!-- Empty State -->
            <div id="empty-state" class="hidden bg-white rounded-2xl shadow-sm border border-slate-100 p-10 text-center mt-8">
                <div class="bg-slate-50 w-20 h-20 rounded-full flex items-center justify-center mx-auto mb-4">
                    <i class="fa-solid fa-folder-open text-3xl text-slate-300"></i>
                </div>
                <h3 class="text-lg font-bold text-slate-700 mb-1">尚未有任何記錄</h3>
                <p class="text-sm text-slate-500">此類別目前沒有已儲存的報告數據。</p>
                <button onclick="switchTab('upload')" class="mt-6 text-blue-600 font-bold hover:underline bg-blue-50 px-6 py-2 rounded-full">去新增記錄</button>
            </div>

            <!-- Charts Container -->
            <div id="charts-container" class="space-y-6">
                <!-- Charts injected here -->
            </div>
        </section>

    </main>

    <!-- Bottom Navigation -->
    <nav class="fixed bottom-0 w-full bg-white border-t border-slate-200 pb-safe pt-2 px-6 flex justify-around items-center z-50">
        <button onclick="switchTab('upload')" id="nav-upload" class="flex flex-col items-center p-2 text-blue-600 transition-colors">
            <i class="fa-solid fa-file-medical text-xl mb-1"></i>
            <span class="text-xs font-bold">新增記錄</span>
        </button>
        <button onclick="switchTab('dashboard')" id="nav-dashboard" class="flex flex-col items-center p-2 text-slate-400 transition-colors">
            <i class="fa-solid fa-chart-area text-xl mb-1"></i>
            <span class="text-xs font-bold">趨勢概覽</span>
        </button>
    </nav>

    <!-- Modal Message -->
    <div id="msg-modal" class="fixed inset-0 bg-black/60 z-[100] hidden items-center justify-center p-4 backdrop-blur-sm">
        <div class="bg-white rounded-2xl p-6 max-w-sm w-full text-center shadow-2xl transform transition-all">
            <div id="msg-icon" class="text-5xl mb-4"></div>
            <h3 id="msg-title" class="text-xl font-bold mb-2 text-slate-800"></h3>
            <p id="msg-desc" class="text-slate-600 text-sm mb-6"></p>
            <button onclick="document.getElementById('msg-modal').classList.replace('flex', 'hidden')" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-3 rounded-xl transition-colors">我知道了</button>
        </div>
    </div>

    <script>
        // ================= SYSTEM DATA =================
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
            cat1: { name: "CA125", metrics: ["CA125"] },
            cat2: { name: "CBC", metrics: ["Hemoglobin", "WBC", "Platelet", "MCV", "MCH", "MCHC", "RBC", "HCT", "RDW", "MPV", "Neutrophil_absolute", "Neutrophil_pct", "Lymphocyte_absolute", "Lymphocyte_pct", "Monocyte_absolute", "Monocyte_pct", "Eosinophil_absolute", "Eosinophil_pct", "Basophil_absolute", "Basophil_pct"] },
            cat3: { name: "Biochem", metrics: ["Sodium", "Potassium", "Urea", "Creatinine", "Protein_Total", "Albumin", "Globulin", "Bilirubin_Total", "Alkaline_Phosphatase", "Alanine_Aminotransferase", "Calcium", "Calcium_Adjusted", "Phosphate"] }
        };

        // 容忍度更高的正則表達式，適應各種雜亂排版
        const extractionRules = {
            "CA125": /(?:CA[-\s]?125|CA125)[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Hemoglobin": /(?:Hemoglobin|Hgb|Hb)[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "WBC": /(?:WBC|White Blood Cell)[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Platelet": /(?:Platelet|PLT)[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "MCV": /(?:MCV)[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "MCH": /(?:MCH)[^C\n]*?(\d+(?:\.\d+)?)/i, 
            "MCHC": /(?:MCHC)[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "RBC": /(?:RBC|Red Blood Cell)[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "HCT": /(?:HCT|Hematocrit)[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "RDW": /(?:RDW)[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "MPV": /(?:MPV)[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Neutrophil_absolute": /(?:Neutrophil(?:s)?\s*absolute|Neutrophil(?:s)?(?![\s\S]{0,15}\%))[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Neutrophil_pct": /(?:Neutrophil(?:s)?\s*\%|Neutrophil(?:s)?(?=[\s\S]{0,15}\%))[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Lymphocyte_absolute": /(?:Lymphocyte(?:s)?\s*absolute|Lymphocyte(?:s)?(?![\s\S]{0,15}\%))[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Lymphocyte_pct": /(?:Lymphocyte(?:s)?\s*\%|Lymphocyte(?:s)?(?=[\s\S]{0,15}\%))[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Monocyte_absolute": /(?:Monocyte(?:s)?\s*absolute|Monocyte(?:s)?(?![\s\S]{0,15}\%))[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Monocyte_pct": /(?:Monocyte(?:s)?\s*\%|Monocyte(?:s)?(?=[\s\S]{0,15}\%))[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Eosinophil_absolute": /(?:Eosinophil(?:s)?\s*absolute|Eosinophil(?:s)?(?![\s\S]{0,15}\%))[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Eosinophil_pct": /(?:Eosinophil(?:s)?\s*\%|Eosinophil(?:s)?(?=[\s\S]{0,15}\%))[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Basophil_absolute": /(?:Basophil(?:s)?\s*absolute|Basophil(?:s)?(?![\s\S]{0,15}\%))[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Basophil_pct": /(?:Basophil(?:s)?\s*\%|Basophil(?:s)?(?=[\s\S]{0,15}\%))[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Sodium": /(?:Sodium|Na\+)[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Potassium": /(?:Potassium|K\+)[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Urea": /(?:Urea|BUN)[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Creatinine": /(?:Creatinine|Cr)[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Protein_Total": /(?:Protein\s*Total|Total\s*Protein)[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Albumin": /(?:Albumin|ALB)(?![\s\S]{0,15}Adjusted)[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Globulin": /(?:Globulin|GLB)[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Bilirubin_Total": /(?:Bilirubin\s*Total|Total\s*Bilirubin)[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Alkaline_Phosphatase": /(?:Alkaline\s*Phosphatase|ALP)[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Alanine_Aminotransferase": /(?:Alanine\s*Aminotransferase|ALT|SGPT)[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Calcium": /Calcium(?![\s\S]{0,15}Adjusted)[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Calcium_Adjusted": /(?:Calcium\s*Adjusted|Adjusted\s*Calcium)[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Phosphate": /(?:Phosphate|PO4)[\s\S]{0,50}?(\d+(?:\.\d+)?)/i,
            "Date": /(\d{4}[-/]\d{1,2}[-/]\d{1,2}|\d{1,2}[-/]\d{1,2}[-/]\d{4})/
        };

        const chartColors = ['#3b82f6', '#ef4444', '#10b981', '#f59e0b', '#8b5cf6', '#ec4899', '#06b6d4', '#84cc16', '#6366f1', '#f43f5e', '#14b8a6', '#d946ef'];

        // ================= APP STATE =================
        let currentTab = 'upload';
        let currentUploadCat = 'cat1';
        let currentDashCat = 'cat1';
        let db = JSON.parse(localStorage.getItem('healthData')) || { cat1: [], cat2: [], cat3: [] };
        let activeChartInstances = [];

        window.onload = () => {
            selectCategory('cat1');
            switchDashCategory('cat1');
        };

        // ================= UI NAVIGATION =================
        function switchTab(tab) {
            currentTab = tab;
            document.getElementById('upload-view').classList.toggle('hidden', tab !== 'upload');
            document.getElementById('dashboard-view').classList.toggle('hidden', tab !== 'dashboard');
            
            document.getElementById('nav-upload').className = `flex flex-col items-center p-2 transition-colors ${tab === 'upload' ? 'text-blue-600' : 'text-slate-400'}`;
            document.getElementById('nav-dashboard').className = `flex flex-col items-center p-2 transition-colors ${tab === 'dashboard' ? 'text-blue-600' : 'text-slate-400'}`;

            if (tab === 'dashboard') renderCharts();
            if (tab === 'upload') resetUploadArea(); // 確保回到上載頁面時是乾淨的狀態
        }

        function selectCategory(cat) {
            currentUploadCat = cat;
            ['cat1', 'cat2', 'cat3'].forEach(c => {
                const btn = document.getElementById(`btn-${c}`);
                if (c === cat) {
                    btn.className = "flex-1 py-2.5 px-3 rounded-md text-sm font-bold transition-all shadow bg-white text-blue-600";
                } else {
                    btn.className = "flex-1 py-2.5 px-3 rounded-md text-sm font-bold transition-all text-slate-500 hover:text-slate-700 bg-transparent";
                }
            });

            const desc = document.getElementById('category-desc');
            if (cat === 'cat1') desc.innerHTML = "報告只有一個指標 CA125，你可以選擇拍照、貼上文字，或直接手動輸入。";
            else if (cat === 'cat2') desc.innerHTML = "此報告包含大量血液指數，強烈建議你在相片中複製文字並使用「貼上」功能。";
            else desc.innerHTML = "此報告包含多項生化指標，強烈建議你在相片中複製文字並使用「貼上」功能。";

            resetUploadArea();
        }

        function switchDashCategory(cat) {
            currentDashCat = cat;
            ['cat1', 'cat2', 'cat3'].forEach(c => {
                const btn = document.getElementById(`dash-${c}`);
                btn.className = c === cat 
                    ? "flex-1 py-2 px-3 rounded-md text-sm font-bold transition-all bg-white shadow text-blue-600"
                    : "flex-1 py-2 px-3 rounded-md text-sm font-bold transition-all text-slate-500 hover:text-slate-700 bg-transparent";
            });
            renderCharts();
        }

        function resetUploadArea() {
            document.getElementById('file-input').value = "";
            document.getElementById('raw-text-input').value = "";
            document.getElementById('loading-area').classList.add('hidden');
            document.getElementById('input-methods-area').classList.remove('hidden');
            document.getElementById('verification-area').classList.add('hidden');
            document.getElementById('ocr-progress').style.width = '0%';
        }

        function showMessage(title, desc, isError = false) {
            document.getElementById('msg-title').innerText = title;
            document.getElementById('msg-desc').innerText = desc;
            document.getElementById('msg-icon').innerHTML = isError ? '<i class="fa-solid fa-circle-xmark text-red-500"></i>' : '<i class="fa-solid fa-circle-check text-green-500"></i>';
            document.getElementById('msg-modal').classList.replace('hidden', 'flex');
        }

        // ================= METHOD 1: IMAGE COMPRESSION & OCR =================
        async function compressImage(file, maxWidth = 1000) {
            return new Promise((resolve) => {
                const reader = new FileReader();
                reader.readAsDataURL(file);
                reader.onload = (event) => {
                    const img = new Image();
                    img.src = event.target.result;
                    img.onload = () => {
                        const canvas = document.createElement('canvas');
                        let width = img.width;
                        let height = img.height;

                        // 大幅壓縮圖片尺寸，加快讀取速度
                        if (width > maxWidth || height > maxWidth) {
                            if (width > height) {
                                height = Math.round((height * maxWidth) / width);
                                width = maxWidth;
                            } else {
                                width = Math.round((width * maxWidth) / height);
                                height = maxWidth;
                            }
                        }

                        canvas.width = width;
                        canvas.height = height;
                        const ctx = canvas.getContext('2d');
                        
                        // 增強對比度及轉為灰階，有助於 Tesseract 識別數字
                        ctx.filter = 'grayscale(100%) contrast(150%)';
                        ctx.drawImage(img, 0, 0, width, height);
                        
                        // 以較低的品質輸出 JPEG 以節省記憶體
                        resolve(canvas.toDataURL('image/jpeg', 0.7));
                    };
                };
            });
        }

        async function handleFileSelect(event) {
            const files = event.target.files;
            if (!files || files.length === 0) return;

            document.getElementById('input-methods-area').classList.add('hidden');
            document.getElementById('loading-area').classList.remove('hidden');
            
            const statusEl = document.getElementById('ocr-status');
            const progressEl = document.getElementById('ocr-progress');
            progressEl.style.width = '5%';

            try {
                let fullText = "";

                for (let i = 0; i < files.length; i++) {
                    statusEl.innerText = `正在壓縮圖片 ${i + 1}/${files.length} ...`;
                    const compressedDataUrl = await compressImage(files[i]);
                    
                    statusEl.innerText = `啟動識別引擎 ...`;
                    
                    // 初始化 Tesseract (使用標準 Promise 寫法確保穩定)
                    const worker = await Tesseract.createWorker({
                        logger: m => {
                            if (m.status === 'recognizing text') {
                                const percent = Math.round(m.progress * 100);
                                progressEl.style.width = `${percent}%`;
                                statusEl.innerText = `正在識別文字: ${percent}%`;
                            }
                        }
                    });
                    
                    await worker.loadLanguage('eng');
                    await worker.initialize('eng');
                    // 限制只讀取這些字符，速度更快，減少亂碼
                    await worker.setParameters({
                        tessedit_char_whitelist: '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ.,%/-+:<> ',
                    });

                    const { data: { text } } = await worker.recognize(compressedDataUrl);
                    fullText += text + "\n";
                    
                    await worker.terminate();
                }
                
                analyzeText(fullText);

            } catch (error) {
                console.error("OCR Error:", error);
                document.getElementById('loading-area').classList.add('hidden');
                showMessage("識別失敗", "由於圖片太過複雜或瀏覽器記憶體不足，無法完成自動識別。請改用「完全手動輸入」或「貼上相片文字」功能。", true);
                resetUploadArea();
            }
        }

        // ================= METHOD 2: iOS LIVE TEXT PASTE =================
        function processPastedText() {
            const rawText = document.getElementById('raw-text-input').value;
            if (!rawText.trim()) {
                showMessage("提示", "請先在文字框內貼上內容，再點擊分析。", true);
                return;
            }
            
            // 隱藏輸入區，直接跳轉驗證
            document.getElementById('input-methods-area').classList.add('hidden');
            analyzeText(rawText);
        }

        // ================= COMMON TEXT ANALYSIS =================
        function analyzeText(text) {
            // 清理多餘空格，但保留換行符號
            const cleanText = text.replace(/[ \t]+/g, ' '); 
            
            const extractedMetrics = {};
            const metricsToFind = categorySetup[currentUploadCat].metrics;

            metricsToFind.forEach(metric => {
                const rule = extractionRules[metric];
                if (rule) {
                    const match = cleanText.match(rule);
                    if (match && match[1]) {
                        extractedMetrics[metric] = parseFloat(match[1]);
                    }
                }
            });
            
            let extractedDate = null;
            const dateMatch = cleanText.match(extractionRules["Date"]);
            if (dateMatch) {
                let d = dateMatch[1].replace(/\//g, '-');
                // 處理 DD-MM-YYYY 格式轉換為 YYYY-MM-DD
                if (d.match(/^\d{1,2}-\d{1,2}-\d{4}$/)) {
                    const parts = d.split('-');
                    d = `${parts[2]}-${parts[1].padStart(2,'0')}-${parts[0].padStart(2,'0')}`;
                }
                extractedDate = d;
            }

            document.getElementById('loading-area').classList.add('hidden');
            startVerification(extractedMetrics, extractedDate);
        }

        // ================= METHOD 3 & VERIFICATION =================
        // 當沒有參數傳入時 (例如點擊手動輸入)，會帶入預設空物件
        function startVerification(extractedMetrics = {}, extractedDate = null) {
            document.getElementById('input-methods-area').classList.add('hidden');
            document.getElementById('verification-area').classList.remove('hidden');
            
            const dateInput = document.getElementById('report-date');
            const dateWarning = document.getElementById('date-warning');
            const today = new Date().toISOString().split('T')[0];
            
            if (extractedDate && extractedDate.match(/^\d{4}-\d{2}-\d{2}$/)) {
                dateInput.value = extractedDate;
                dateWarning.classList.add('hidden');
            } else {
                dateInput.value = today; 
                dateWarning.classList.remove('hidden');
            }

            const metricsContainer = document.getElementById('metrics-form');
            metricsContainer.innerHTML = '';

            categorySetup[currentUploadCat].metrics.forEach(metric => {
                const val = extractedMetrics[metric] !== undefined ? extractedMetrics[metric] : '';
                const rangeStr = getRangeString(metric);
                
                const div = document.createElement('div');
                div.className = "flex flex-col sm:flex-row sm:items-center justify-between border border-slate-100 bg-slate-50 rounded-xl p-3";
                
                div.innerHTML = `
                    <div class="mb-2 sm:mb-0">
                        <span class="block text-[15px] font-bold text-slate-800">${metric.replace(/_/g, ' ')}</span>
                        <span class="text-xs text-slate-500 font-medium">參考值: ${rangeStr}</span>
                    </div>
                    <div class="relative w-full sm:w-1/3">
                        <input type="number" step="any" id="input-${metric}" value="${val}" 
                            class="w-full border border-slate-300 rounded-lg py-2.5 px-3 text-right focus:outline-none focus:ring-2 focus:ring-blue-500 font-mono transition-colors text-lg"
                            oninput="checkValueColor('${metric}', this)">
                    </div>
                `;
                metricsContainer.appendChild(div);
                
                // 觸發初始顏色檢查
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
                inputElement.classList.add('text-red-600', 'font-bold', 'bg-red-50', 'border-red-400', 'ring-1', 'ring-red-400');
                inputElement.classList.remove('text-slate-800', 'border-slate-300');
            } else {
                inputElement.classList.remove('text-red-600', 'font-bold', 'bg-red-50', 'border-red-400', 'ring-1', 'ring-red-400');
                inputElement.classList.add('text-slate-800', 'border-slate-300');
            }
        }

        function saveReport() {
            const dateInput = document.getElementById('report-date').value;
            if (!dateInput) {
                showMessage("缺少資料", "請輸入報告日期", true);
                return;
            }

            const newRecord = { id: Date.now().toString(), date: dateInput, metrics: {} };
            let hasData = false;

            categorySetup[currentUploadCat].metrics.forEach(metric => {
                const val = document.getElementById(`input-${metric}`).value;
                if (val !== '' && !isNaN(parseFloat(val))) {
                    newRecord.metrics[metric] = parseFloat(val);
                    hasData = true;
                }
            });

            if (!hasData) {
                showMessage("缺少資料", "請至少填寫一個指標的數值才能儲存", true);
                return;
            }

            db[currentUploadCat].push(newRecord);
            // 按照日期舊到新排序
            db[currentUploadCat].sort((a, b) => new Date(a.date) - new Date(b.date));
            localStorage.setItem('healthData', JSON.stringify(db));

            showMessage("儲存成功", "資料已安全記錄在你的設備中");
            resetUploadArea();
            switchTab('dashboard');
        }

        // ================= DASHBOARD & CHARTS =================
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
                card.className = "bg-white p-5 rounded-2xl shadow-sm border border-slate-100";
                
                const latestVal = values[values.length - 1];
                const isLatestAbnormal = isAbnormal(metric, latestVal);
                const latestColorClass = isLatestAbnormal ? 'text-red-500 font-bold bg-red-50 px-2 py-1 rounded' : 'text-slate-800 font-bold';
                const warningIcon = isLatestAbnormal ? '<i class="fa-solid fa-triangle-exclamation text-red-500 ml-1"></i>' : '';

                card.innerHTML = `
                    <div class="flex justify-between items-start mb-4">
                        <div>
                            <h3 class="text-[15px] font-bold text-slate-800">${metric.replace(/_/g, ' ')}</h3>
                            <p class="text-xs text-slate-400 font-medium mt-1">參考區間: ${getRangeString(metric)}</p>
                        </div>
                        <div class="text-right">
                            <span class="text-[10px] text-slate-400 block mb-1 uppercase font-bold tracking-wider">最新記錄 (${dates[dates.length - 1]})</span>
                            <span class="text-xl ${latestColorClass}">${latestVal} ${warningIcon}</span>
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
                            backgroundColor: chartColor + '22', // 很淡的背景色
                            borderWidth: 2.5,
                            tension: 0.3, // 曲線平滑度
                            pointBackgroundColor: pointColors,
                            pointBorderColor: '#ffffff',
                            pointBorderWidth: 1.5,
                            pointRadius: pointRadii,
                            pointHoverRadius: 8,
                            fill: true
                        }]
                    },
                    options: {
                        responsive: true, 
                        maintainAspectRatio: false,
                        plugins: {
                            legend: { display: false },
                            tooltip: {
                                backgroundColor: 'rgba(15, 23, 42, 0.9)', 
                                titleColor: '#fff', 
                                bodyColor: '#fff',
                                padding: 12, 
                                displayColors: false,
                                cornerRadius: 8,
                                callbacks: {
                                    label: function(context) {
                                        let val = context.parsed.y;
                                        let status = isAbnormal(metric, val) ? ' (異常數值)' : '';
                                        return `數值: ${val}${status}`;
                                    }
                                }
                            }
                        },
                        scales: {
                            y: { 
                                grid: { color: '#f1f5f9', borderDash: [5, 5] }, 
                                ticks: { font: { size: 10, family: 'sans-serif' }, color: '#94a3b8' } 
                            },
                            x: { 
                                grid: { display: false }, 
                                ticks: { font: { size: 10 }, color: '#94a3b8', maxRotation: 45 } 
                            }
                        },
                        interaction: { intersect: false, mode: 'index' },
                    }
                });
                
                activeChartInstances.push(chart);
            });
        }
    </script>
</body>
</html>


