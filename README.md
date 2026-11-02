
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>農機具・農業資材 倉庫管理システム</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        agri: {
                            50: '#f0fdf4',
                            100: '#dcfce7',
                            500: '#22c55e',
                            600: '#16a34a',
                            700: '#15803d',
                            800: '#166534',
                            900: '#14532d',
                        }
                    }
                }
            }
        }
    </script>
    <style>
        body { font-family: 'Helvetica Neue', Arial, 'Hiragino Kaku Gothic ProN', 'Hiragino Sans', Meiryo, sans-serif; }
        .modal-backdrop { background-color: rgba(0, 0, 0, 0.5); }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 min-h-screen flex flex-col">

    <!-- Header -->
    <header class="bg-agri-800 text-white shadow-md">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-4 flex flex-wrap justify-between items-center gap-4">
            <div class="flex items-center space-x-3">
                <div class="bg-white p-2 rounded-lg text-agri-800 shadow">
                    <i data-lucide="sprout" class="w-7 h-7"></i>
                </div>
                <div>
                    <h1 class="text-xl sm:text-2xl font-bold tracking-tight">アグリ・ストック Nav</h1>
                    <p class="text-xs text-agri-100">農機具 & 農業資材 統合倉庫管理システム</p>
                </div>
            </div>
            <div class="flex items-center space-x-3 text-xs sm:text-sm">
                <span id="header-alert-badge" class="hidden items-center gap-1 bg-amber-500 text-white font-medium px-3 py-1 rounded-full shadow">
                    <i data-lucide="bell" class="w-4 h-4"></i>
                    <span id="alert-count-text">0 件の要対応</span>
                </span>
                <button onclick="exportData()" class="flex items-center gap-1 bg-agri-700 hover:bg-agri-600 px-3 py-1.5 rounded transition text-xs">
                    <i data-lucide="download" class="w-4 h-4"></i> エクスポート
                </button>
            </div>
        </div>
        
        <!-- Navigation Tabs -->
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 flex space-x-1 border-t border-agri-700/60 pt-2">
            <button id="tab-btn-dashboard" onclick="switchTab('dashboard')" class="tab-btn flex items-center gap-2 px-4 py-2.5 rounded-t-lg font-medium text-sm transition text-agri-100 hover:bg-agri-700">
                <i data-lucide="layout-dashboard" class="w-4 h-4"></i> ダッシュボード
            </button>
            <button id="tab-btn-materials" onclick="switchTab('materials')" class="tab-btn flex items-center gap-2 px-4 py-2.5 rounded-t-lg font-medium text-sm transition text-agri-100 hover:bg-agri-700">
                <i data-lucide="package" class="w-4 h-4"></i> 資材・在庫管理
            </button>
            <button id="tab-btn-machines" onclick="switchTab('machines')" class="tab-btn flex items-center gap-2 px-4 py-2.5 rounded-t-lg font-medium text-sm transition text-agri-100 hover:bg-agri-700">
                <i data-lucide="tractor" class="w-4 h-4"></i> 農機具・整備管理
            </button>
        </div>
    </header>

    <!-- Main Content Container -->
    <main class="flex-grow max-w-7xl w-full mx-auto px-4 sm:px-6 lg:px-8 py-6">

        <!-- 1. DASHBOARD TAB -->
        <div id="tab-dashboard" class="tab-content space-y-6">
            <!-- Summary Stats Grid -->
            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                <div class="bg-white rounded-xl p-5 border border-slate-200 shadow-sm flex items-center justify-between">
                    <div>
                        <p class="text-xs font-semibold text-slate-500 uppercase tracking-wider">登録資材数</p>
                        <h3 id="stat-material-count" class="text-2xl font-bold text-slate-800 mt-1">0</h3>
                    </div>
                    <div class="p-3 bg-agri-50 text-agri-600 rounded-lg">
                        <i data-lucide="boxes" class="w-6 h-6"></i>
                    </div>
                </div>

                <div class="bg-white rounded-xl p-5 border border-slate-200 shadow-sm flex items-center justify-between">
                    <div>
                        <p class="text-xs font-semibold text-slate-500 uppercase tracking-wider">発注推奨（低在庫）</p>
                        <h3 id="stat-low-stock-count" class="text-2xl font-bold text-amber-600 mt-1">0</h3>
                    </div>
                    <div class="p-3 bg-amber-50 text-amber-600 rounded-lg">
                        <i data-lucide="alert-triangle" class="w-6 h-6"></i>
                    </div>
                </div>

                <div class="bg-white rounded-xl p-5 border border-slate-200 shadow-sm flex items-center justify-between">
                    <div>
                        <p class="text-xs font-semibold text-slate-500 uppercase tracking-wider">保有農機具数</p>
                        <h3 id="stat-machine-count" class="text-2xl font-bold text-slate-800 mt-1">0</h3>
                    </div>
                    <div class="p-3 bg-blue-50 text-blue-600 rounded-lg">
                        <i data-lucide="wrench" class="w-6 h-6"></i>
                    </div>
                </div>

                <div class="bg-white rounded-xl p-5 border border-slate-200 shadow-sm flex items-center justify-between">
                    <div>
                        <p class="text-xs font-semibold text-slate-500 uppercase tracking-wider">要点検・故障機具</p>
                        <h3 id="stat-inspection-count" class="text-2xl font-bold text-red-600 mt-1">0</h3>
                    </div>
                    <div class="p-3 bg-red-50 text-red-600 rounded-lg">
                        <i data-lucide="shield-alert" class="w-6 h-6"></i>
                    </div>
                </div>
            </div>

            <!-- Alerts & Charts Grid -->
            <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                <!-- Urgent Alerts Box -->
                <div class="lg:col-span-2 space-y-6">
                    <!-- Low Stock Alert List -->
                    <div class="bg-white rounded-xl border border-slate-200 shadow-sm overflow-hidden">
                        <div class="bg-amber-50 border-b border-amber-100 px-5 py-3.5 flex items-center justify-between">
                            <h2 class="font-bold text-amber-900 flex items-center gap-2">
                                <i data-lucide="shopping-cart" class="w-5 h-5 text-amber-600"></i>
                                発注が必要な資材（不足・僅少）
                            </h2>
                            <span id="low-stock-badge" class="bg-amber-200 text-amber-800 text-xs font-bold px-2.5 py-0.5 rounded-full">0件</span>
                        </div>
                        <div class="p-4">
                            <div id="dashboard-low-stock-list" class="divide-y divide-slate-100">
                                <!-- Dynamic Insert -->
                            </div>
                        </div>
                    </div>

                    <!-- Machine Maintenance Alert List -->
                    <div class="bg-white rounded-xl border border-slate-200 shadow-sm overflow-hidden">
                        <div class="bg-red-50 border-b border-red-100 px-5 py-3.5 flex items-center justify-between">
                            <h2 class="font-bold text-red-900 flex items-center gap-2">
                                <i data-lucide="alert-circle" class="w-5 h-5 text-red-600"></i>
                                点検・整備が必要な農機具
                            </h2>
                            <span id="inspection-badge" class="bg-red-200 text-red-800 text-xs font-bold px-2.5 py-0.5 rounded-full">0件</span>
                        </div>
                        <div class="p-4">
                            <div id="dashboard-inspection-list" class="divide-y divide-slate-100">
                                <!-- Dynamic Insert -->
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Right Side: Chart & Quick Stats -->
                <div class="bg-white rounded-xl border border-slate-200 shadow-sm p-5 flex flex-col">
                    <h2 class="font-bold text-slate-800 flex items-center gap-2 mb-4">
                        <i data-lucide="pie-chart" class="w-5 h-5 text-agri-600"></i>
                        資材カテゴリ別 内訳
                    </h2>
                    <div class="flex-grow flex items-center justify-center relative min-h-[220px]">
                        <canvas id="categoryChart"></canvas>
                    </div>
                    <div class="mt-4 pt-4 border-t border-slate-100 text-xs text-slate-500">
                        ※登録資材のカテゴリ別割合を表示しています。適切な在庫バランスの把握にご活用ください。
                    </div>
                </div>
            </div>
        </div>

        <!-- 2. MATERIALS TAB -->
        <div id="tab-materials" class="tab-content hidden space-y-6">
            <!-- Control Bar -->
            <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm flex flex-col md:flex-row gap-4 items-center justify-between">
                <!-- Search & Filters -->
                <div class="flex flex-1 flex-wrap gap-3 w-full md:w-auto">
                    <div class="relative flex-1 min-w-[200px]">
                        <i data-lucide="search" class="w-4 h-4 absolute left-3 top-1/2 -translate-y-1/2 text-slate-400"></i>
                        <input type="text" id="material-search" oninput="renderMaterials()" placeholder="資材名・保管場所で検索..." class="w-full pl-9 pr-4 py-2 border border-slate-300 rounded-lg text-sm focus:outline-none focus:ring-2 focus:ring-agri-500">
                    </div>
                    <select id="material-filter-category" onchange="renderMaterials()" class="border border-slate-300 rounded-lg px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-agri-500 bg-white">
                        <option value="ALL">全カテゴリ</option>
                        <option value="肥料">肥料</option>
                        <option value="農薬">農薬</option>
                        <option value="種苗">種苗</option>
                        <option value="包装資材">包装資材</option>
                        <option value="その他">その他</option>
                    </select>
                    <select id="material-filter-alert" onchange="renderMaterials()" class="border border-slate-300 rounded-lg px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-agri-500 bg-white">
                        <option value="ALL">全ての在庫状況</option>
                        <option value="LOW">発注推奨（適正以下）</option>
                        <option value="OK">適正在庫</option>
                    </select>
                </div>
                <!-- Add Button -->
                <button onclick="openMaterialModal()" class="w-full md:w-auto bg-agri-600 hover:bg-agri-700 text-white font-medium px-4 py-2 rounded-lg text-sm shadow flex items-center justify-center gap-2 transition">
                    <i data-lucide="plus" class="w-4 h-4"></i> 新規資材を登録
                </button>
            </div>

            <!-- Material Table / Grid -->
            <div class="bg-white rounded-xl border border-slate-200 shadow-sm overflow-hidden">
                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse text-sm">
                        <thead>
                            <tr class="bg-slate-50 border-b border-slate-200 text-slate-600 font-medium">
                                <th class="p-4">資材名</th>
                                <th class="p-4">カテゴリ</th>
                                <th class="p-4">保管場所</th>
                                <th class="p-4 w-48">在庫状態 (現在 / 適正)</th>
                                <th class="p-4 text-center">クイック入出庫</th>
                                <th class="p-4 text-right">操作</th>
                            </tr>
                        </thead>
                        <tbody id="materials-table-body" class="divide-y divide-slate-100">
                            <!-- Dynamic Content -->
                        </tbody>
                    </table>
                </div>
                <div id="materials-empty-state" class="hidden p-8 text-center text-slate-400">
                    <i data-lucide="package-x" class="w-12 h-12 mx-auto mb-2 opacity-50"></i>
                    <p>該当する資材が見つかりません。</p>
                </div>
            </div>
        </div>

        <!-- 3. MACHINES TAB -->
        <div id="tab-machines" class="tab-content hidden space-y-6">
            <!-- Control Bar -->
            <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm flex flex-col md:flex-row gap-4 items-center justify-between">
                <div class="flex flex-1 flex-wrap gap-3 w-full md:w-auto">
                    <div class="relative flex-1 min-w-[200px]">
                        <i data-lucide="search" class="w-4 h-4 absolute left-3 top-1/2 -translate-y-1/2 text-slate-400"></i>
                        <input type="text" id="machine-search" oninput="renderMachines()" placeholder="機具名・型式・保管場所で検索..." class="w-full pl-9 pr-4 py-2 border border-slate-300 rounded-lg text-sm focus:outline-none focus:ring-2 focus:ring-agri-500">
                    </div>
                    <select id="machine-filter-status" onchange="renderMachines()" class="border border-slate-300 rounded-lg px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-agri-500 bg-white">
                        <option value="ALL">全ステータス</option>
                        <option value="稼働可能">稼働可能</option>
                        <option value="点検中">点検中</option>
                        <option value="故障中">故障中</option>
                    </select>
                </div>
                <button onclick="openMachineModal()" class="w-full md:w-auto bg-agri-600 hover:bg-agri-700 text-white font-medium px-4 py-2 rounded-lg text-sm shadow flex items-center justify-center gap-2 transition">
                    <i data-lucide="plus" class="w-4 h-4"></i> 新規農機具を登録
                </button>
            </div>

            <!-- Machines Grid -->
            <div id="machines-grid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                <!-- Dynamic Content -->
            </div>
            <div id="machines-empty-state" class="hidden bg-white rounded-xl p-8 text-center text-slate-400 border border-slate-200">
                <i data-lucide="wrench" class="w-12 h-12 mx-auto mb-2 opacity-50"></i>
                <p>該当する農機具が見つかりません。</p>
            </div>
        </div>

    </main>

    <!-- Modal: Add/Edit Material -->
    <div id="modal-material" class="fixed inset-0 modal-backdrop hidden flex items-center justify-center p-4 z-50">
        <div class="bg-white rounded-xl shadow-xl max-w-lg w-full overflow-hidden animate-fade-in">
            <div class="bg-agri-800 text-white px-6 py-4 flex justify-between items-center">
                <h3 id="modal-material-title" class="font-bold text-lg">資材の登録</h3>
                <button onclick="closeMaterialModal()" class="text-white/80 hover:text-white"><i data-lucide="x" class="w-5 h-5"></i></button>
            </div>
            <form id="form-material" onsubmit="saveMaterial(event)" class="p-6 space-y-4">
                <input type="hidden" id="mat-id">
                <div>
                    <label class="block text-xs font-semibold text-slate-600 mb-1">資材名称 <span class="text-red-500">*</span></label>
                    <input type="text" id="mat-name" required placeholder="例: 化成肥料 8-8-8" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-agri-500 outline-none">
                </div>
                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-xs font-semibold text-slate-600 mb-1">カテゴリ <span class="text-red-500">*</span></label>
                        <select id="mat-category" required class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-agri-500 outline-none bg-white">
                            <option value="肥料">肥料</option>
                            <option value="農薬">農薬</option>
                            <option value="種苗">種苗</option>
                            <option value="包装資材">包装資材</option>
                            <option value="その他">その他</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-600 mb-1">単位 <span class="text-red-500">*</span></label>
                        <input type="text" id="mat-unit" required placeholder="例: 袋, kg, L, 箱" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-agri-500 outline-none">
                    </div>
                </div>
                <div>
                    <label class="block text-xs font-semibold text-slate-600 mb-1">保管場所</label>
                    <input type="text" id="mat-location" placeholder="例: 第1倉庫 A-2棚" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-agri-500 outline-none">
                </div>
                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-xs font-semibold text-slate-600 mb-1">現在在庫数 <span class="text-red-500">*</span></label>
                        <input type="number" step="0.1" id="mat-stock" required class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-agri-500 outline-none">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-600 mb-1">適正在庫数（目標） <span class="text-red-500">*</span></label>
                        <input type="number" step="0.1" id="mat-target" required class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-agri-500 outline-none">
                    </div>
                </div>
                <div class="flex justify-end gap-3 pt-4 border-t border-slate-100">
                    <button type="button" onclick="closeMaterialModal()" class="px-4 py-2 border border-slate-300 rounded-lg text-sm font-medium text-slate-600 hover:bg-slate-50">キャンセル</button>
                    <button type="submit" class="px-4 py-2 bg-agri-600 hover:bg-agri-700 text-white rounded-lg text-sm font-medium shadow">保存する</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Modal: Add/Edit Machine -->
    <div id="modal-machine" class="fixed inset-0 modal-backdrop hidden flex items-center justify-center p-4 z-50">
        <div class="bg-white rounded-xl shadow-xl max-w-lg w-full overflow-hidden animate-fade-in">
            <div class="bg-agri-800 text-white px-6 py-4 flex justify-between items-center">
                <h3 id="modal-machine-title" class="font-bold text-lg">農機具の登録</h3>
                <button onclick="closeMachineModal()" class="text-white/80 hover:text-white"><i data-lucide="x" class="w-5 h-5"></i></button>
            </div>
            <form id="form-machine" onsubmit="saveMachine(event)" class="p-6 space-y-4">
                <input type="hidden" id="mac-id">
                <div>
                    <label class="block text-xs font-semibold text-slate-600 mb-1">機具名称 <span class="text-red-500">*</span></label>
                    <input type="text" id="mac-name" required placeholder="例: トラクター 30馬力" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-agri-500 outline-none">
                </div>
                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-xs font-semibold text-slate-600 mb-1">型式 / メーカー</label>
                        <input type="text" id="mac-model" placeholder="例: YT333A / ヤンマー" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-agri-500 outline-none">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-600 mb-1">状態 <span class="text-red-500">*</span></label>
                        <select id="mac-status" required class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-agri-500 outline-none bg-white">
                            <option value="稼働可能">稼働可能</option>
                            <option value="点検中">点検中</option>
                            <option value="故障中">故障中</option>
                        </select>
                    </div>
                </div>
                <div>
                    <label class="block text-xs font-semibold text-slate-600 mb-1">保管場所</label>
                    <input type="text" id="mac-location" placeholder="例: 農機具車庫" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-agri-500 outline-none">
                </div>
                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-xs font-semibold text-slate-600 mb-1">次回点検予定日</label>
                        <input type="date" id="mac-next-date" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-agri-500 outline-none">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-600 mb-1">アワーメーター / 使用時間(h)</label>
                        <input type="number" step="0.1" id="mac-hours" placeholder="例: 120.5" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-agri-500 outline-none">
                    </div>
                </div>
                <div class="flex justify-end gap-3 pt-4 border-t border-slate-100">
                    <button type="button" onclick="closeMachineModal()" class="px-4 py-2 border border-slate-300 rounded-lg text-sm font-medium text-slate-600 hover:bg-slate-50">キャンセル</button>
                    <button type="submit" class="px-4 py-2 bg-agri-600 hover:bg-agri-700 text-white rounded-lg text-sm font-medium shadow">保存する</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Script Area -->
    <script>
        // --- DATA STATE ---
        let materials = [];
        let machines = [];
        let categoryChart = null;

        // Default sample data if local storage is empty
        const defaultMaterials = [
            { id: "m1", name: "元肥 化成肥料 14-14-14", category: "肥料", location: "肥料倉庫 A-1", stock: 5, target: 20, unit: "袋" },
            { id: "m2", name: "有機質ペレット肥料", category: "肥料", location: "肥料倉庫 A-2", stock: 18, target: 15, unit: "袋" },
            { id: "m3", name: "水稲用殺虫剤（粒剤）", category: "農薬", location: "毒劇物保管庫", stock: 2, target: 10, unit: "本" },
            { id: "m4", name: "玉ねぎ 種子 (極早生)", category: "種苗", location: "種子保冷庫", stock: 12, target: 10, unit: "缶" },
            { id: "m5", name: "出荷用段ボール 10kg用", category: "包装資材", location: "資材庫 2F", stock: 45, target: 200, unit: "枚" }
        ];

        const defaultMachines = [
            { id: "mac1", name: "33馬力 トラクター", model: "YT333A", location: "第1車庫", status: "稼働可能", nextDate: "2026-11-15", hours: 450.2 },
            { id: "mac2", name: "コンバイン 4条刈", model: "YH448A", location: "第1車庫", status: "点検中", nextDate: "2026-10-30", hours: 210.0 },
            { id: "mac3", name: "スピードスプレヤー", model: "SSV653F", location: "第2車庫", status: "故障中", nextDate: "2026-12-01", hours: 620.8 },
            { id: "mac4", name: "乗用草刈機", model: "MASAO", location: "道具小屋", status: "稼働可能", nextDate: "2026-11-05", hours: 85.4 }
        ];

        // --- INIT ---
        window.onload = function() {
            loadData();
            switchTab('dashboard');
            lucide.createIcons();
        };

        function loadData() {
            const savedMats = localStorage.getItem('agri_materials');
            const savedMacs = localStorage.getItem('agri_machines');

            materials = savedMats ? JSON.parse(savedMats) : defaultMaterials;
            machines = savedMacs ? JSON.parse(savedMacs) : defaultMachines;

            saveData();
        }

        function saveData() {
            localStorage.setItem('agri_materials', JSON.stringify(materials));
            localStorage.setItem('agri_machines', JSON.stringify(machines));
            updateAllViews();
        }

        // --- TAB NAVIGATION ---
        function switchTab(tabName) {
            document.querySelectorAll('.tab-content').forEach(el => el.classList.add('hidden'));
            document.querySelectorAll('.tab-btn').forEach(btn => {
                btn.classList.remove('bg-agri-700', 'text-white');
                btn.classList.add('text-agri-100');
            });

            document.getElementById(`tab-${tabName}`).classList.remove('hidden');
            const activeBtn = document.getElementById(`tab-btn-${tabName}`);
            activeBtn.classList.add('bg-agri-700', 'text-white');
            activeBtn.classList.remove('text-agri-100');

            if (tabName === 'dashboard') {
                renderDashboard();
            } else if (tabName === 'materials') {
                renderMaterials();
            } else if (tabName === 'machines') {
                renderMachines();
            }
        }

        function updateAllViews() {
            renderDashboard();
            renderMaterials();
            renderMachines();
        }

        // --- DASHBOARD RENDER ---
        function renderDashboard() {
            // Stats
            document.getElementById('stat-material-count').innerText = materials.length;
            document.getElementById('stat-machine-count').innerText = machines.length;

            const lowStockItems = materials.filter(m => m.stock < m.target);
            document.getElementById('stat-low-stock-count').innerText = lowStockItems.length;

            const today = new Date().toISOString().split('T')[0];
            const inspectionItems = machines.filter(m => m.status !== '稼働可能' || (m.nextDate && m.nextDate <= today));
            document.getElementById('stat-inspection-count').innerText = inspectionItems.length;

            // Header Alert Badge
            const totalAlerts = lowStockItems.length + inspectionItems.length;
            const badge = document.getElementById('header-alert-badge');
            if (totalAlerts > 0) {
                badge.classList.remove('hidden');
                badge.classList.add('flex');
                document.getElementById('alert-count-text').innerText = `${totalAlerts} 件の要対応`;
            } else {
                badge.classList.add('hidden');
                badge.classList.remove('flex');
            }

            // Low Stock Dashboard List
            const lowStockContainer = document.getElementById('dashboard-low-stock-list');
            document.getElementById('low-stock-badge').innerText = `${lowStockItems.length}件`;
            if (lowStockItems.length === 0) {
                lowStockContainer.innerHTML = `<p class="text-xs text-slate-400 py-4 text-center">現在、補充が必要な資材はありません。</p>`;
            } else {
                lowStockContainer.innerHTML = lowStockItems.map(m => {
                    const ratio = Math.min(100, Math.round((m.stock / m.target) * 100));
                    return `
                        <div class="py-3 flex items-center justify-between">
                            <div>
                                <div class="font-bold text-slate-800 text-sm">${escapeHtml(m.name)}</div>
                                <div class="text-xs text-slate-500">保管: ${escapeHtml(m.location || '未設定')} | カテゴリ: ${m.category}</div>
                            </div>
                            <div class="text-right">
                                <span class="text-sm font-bold text-red-600">${m.stock} ${m.unit}</span>
                                <span class="text-xs text-slate-400"> / 適正 ${m.target} ${m.unit} (${ratio}%)</span>
                                <div class="w-24 bg-slate-100 rounded-full h-1.5 mt-1 overflow-hidden">
                                    <div class="bg-red-500 h-1.5 rounded-full" style="width: ${ratio}%"></div>
                                </div>
                            </div>
                        </div>
                    `;
                }).join('');
            }

            // Inspection Dashboard List
            const inspectionContainer = document.getElementById('dashboard-inspection-list');
            document.getElementById('inspection-badge').innerText = `${inspectionItems.length}件`;
            if (inspectionItems.length === 0) {
                inspectionContainer.innerHTML = `<p class="text-xs text-slate-400 py-4 text-center">要点検・故障中の農機具はありません。</p>`;
            } else {
                inspectionContainer.innerHTML = inspectionItems.map(m => {
                    let statusBadge = '';
                    if (m.status === '故障中') {
                        statusBadge = `<span class="bg-red-100 text-red-800 text-xs font-medium px-2 py-0.5 rounded border border-red-200">故障中</span>`;
                    } else if (m.status === '点検中') {
                        statusBadge = `<span class="bg-amber-100 text-amber-800 text-xs font-medium px-2 py-0.5 rounded border border-amber-200">点検中</span>`;
                    } else {
                        statusBadge = `<span class="bg-orange-100 text-orange-800 text-xs font-medium px-2 py-0.5 rounded border border-orange-200">期限切れ/直前</span>`;
                    }

                    return `
                        <div class="py-3 flex items-center justify-between">
                            <div>
                                <div class="font-bold text-slate-800 text-sm flex items-center gap-2">
                                    ${escapeHtml(m.name)} ${statusBadge}
                                </div>
                                <div class="text-xs text-slate-500">型式: ${escapeHtml(m.model || '-')} | 保管: ${escapeHtml(m.location || '-')}</div>
                            </div>
                            <div class="text-right text-xs">
                                <div class="font-medium text-slate-700">点検期限: ${m.nextDate || '未設定'}</div>
                                <div class="text-slate-400">使用時間: ${m.hours || 0} h</div>
                            </div>
                        </div>
                    `;
                }).join('');
            }

            renderChart();
            lucide.createIcons();
        }

        // --- CHART RENDER ---
        function renderChart() {
            const ctx = document.getElementById('categoryChart').getContext('2d');
            
            const categories = ["肥料", "農薬", "種苗", "包装資材", "その他"];
            const counts = categories.map(cat => materials.filter(m => m.category === cat).length);

            if (categoryChart) {
                categoryChart.destroy();
            }

            categoryChart = new Chart(ctx, {
                type: 'doughnut',
                data: {
                    labels: categories,
                    datasets: [{
                        data: counts,
                        backgroundColor: [
                            '#16a34a', // 肥料
                            '#eab308', // 農薬
                            '#06b6d4', // 種苗
                            '#8b5cf6', // 包装
                            '#64748b'  // その他
                        ],
                        borderWidth: 2,
                        borderColor: '#ffffff'
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: { position: 'bottom' }
                    }
                }
            });
        }

        // --- MATERIALS LOGIC ---
        function renderMaterials() {
            const tbody = document.getElementById('materials-table-body');
            const searchVal = document.getElementById('material-search').value.toLowerCase();
            const catVal = document.getElementById('material-filter-category').value;
            const alertVal = document.getElementById('material-filter-alert').value;

            const filtered = materials.filter(m => {
                const matchesSearch = m.name.toLowerCase().includes(searchVal) || (m.location && m.location.toLowerCase().includes(searchVal));
                const matchesCat = catVal === 'ALL' || m.category === catVal;
                const isLow = m.stock < m.target;
                const matchesAlert = alertVal === 'ALL' || (alertVal === 'LOW' && isLow) || (alertVal === 'OK' && !isLow);
                return matchesSearch && matchesCat && matchesAlert;
            });

            if (filtered.length === 0) {
                tbody.innerHTML = '';
                document.getElementById('materials-empty-state').classList.remove('hidden');
                return;
            }
            document.getElementById('materials-empty-state').classList.add('hidden');

            tbody.innerHTML = filtered.map(m => {
                const ratio = Math.min(100, Math.round((m.stock / m.target) * 100));
                const isLow = m.stock < m.target;
                const progressColor = isLow ? 'bg-red-500' : 'bg-agri-600';
                const badgeColor = isLow ? 'bg-red-100 text-red-700 border-red-200' : 'bg-emerald-50 text-emerald-700 border-emerald-200';

                return `
                    <tr class="hover:bg-slate-50 transition">
                        <td class="p-4 font-bold text-slate-800">
                            ${escapeHtml(m.name)}
                            ${isLow ? '<span class="ml-2 inline-flex items-center text-[10px] bg-red-100 text-red-600 px-1.5 py-0.5 rounded font-normal"><i data-lucide="alert-triangle" class="w-3 h-3 mr-0.5"></i>残量少</span>' : ''}
                        </td>
                        <td class="p-4"><span class="bg-slate-100 text-slate-700 px-2.5 py-1 rounded-full text-xs font-medium">${m.category}</span></td>
                        <td class="p-4 text-slate-600 text-xs">${escapeHtml(m.location || '-')}</td>
                        <td class="p-4">
                            <div class="flex justify-between text-xs mb-1">
                                <span class="font-bold ${isLow ? 'text-red-600' : 'text-slate-700'}">${m.stock} ${m.unit}</span>
                                <span class="text-slate-400">目標 ${m.target} ${m.unit}</span>
                            </div>
                            <div class="w-full bg-slate-100 rounded-full h-2 overflow-hidden">
                                <div class="${progressColor} h-2 rounded-full" style="width: ${ratio}%"></div>
                            </div>
                        </td>
                        <td class="p-4 text-center">
                            <div class="inline-flex items-center border border-slate-200 rounded-lg overflow-hidden bg-white shadow-sm">
                                <button onclick="quickAdjustStock('${m.id}', -1)" class="px-2.5 py-1 text-slate-600 hover:bg-slate-100 active:bg-slate-200 transition font-bold">-</button>
                                <span class="px-3 py-1 border-x border-slate-200 text-xs font-bold text-slate-700 bg-slate-50 min-w-[36px] text-center">${m.stock}</span>
                                <button onclick="quickAdjustStock('${m.id}', 1)" class="px-2.5 py-1 text-slate-600 hover:bg-slate-100 active:bg-slate-200 transition font-bold">+</button>
                            </div>
                        </td>
                        <td class="p-4 text-right space-x-2">
                            <button onclick="editMaterial('${m.id}')" class="text-slate-400 hover:text-agri-600 transition"><i data-lucide="edit-3" class="w-4 h-4"></i></button>
                            <button onclick="deleteMaterial('${m.id}')" class="text-slate-400 hover:text-red-600 transition"><i data-lucide="trash-2" class="w-4 h-4"></i></button>
                        </td>
                    </tr>
                `;
            }).join('');

            lucide.createIcons();
        }

        function quickAdjustStock(id, amount) {
            const mat = materials.find(m => m.id === id);
            if (mat) {
                mat.stock = Math.max(0, parseFloat((mat.stock + amount).toFixed(2)));
                saveData();
            }
        }

        function openMaterialModal(isEdit = false) {
            if (!isEdit) {
                document.getElementById('form-material').reset();
                document.getElementById('mat-id').value = '';
                document.getElementById('modal-material-title').innerText = '資材の新規登録';
            }
            document.getElementById('modal-material').classList.remove('hidden');
        }

        function closeMaterialModal() {
            document.getElementById('modal-material').classList.add('hidden');
        }

        function saveMaterial(e) {
            e.preventDefault();
            const id = document.getElementById('mat-id').value;
            const matData = {
                id: id || 'm_' + Date.now(),
                name: document.getElementById('mat-name').value,
                category: document.getElementById('mat-category').value,
                unit: document.getElementById('mat-unit').value,
                location: document.getElementById('mat-location').value,
                stock: parseFloat(document.getElementById('mat-stock').value) || 0,
                target: parseFloat(document.getElementById('mat-target').value) || 0,
            };

            if (id) {
                const idx = materials.findIndex(m => m.id === id);
                if (idx !== -1) materials[idx] = matData;
            } else {
                materials.push(matData);
            }

            saveData();
            closeMaterialModal();
        }

        function editMaterial(id) {
            const m = materials.find(item => item.id === id);
            if (!m) return;

            document.getElementById('mat-id').value = m.id;
            document.getElementById('mat-name').value = m.name;
            document.getElementById('mat-category').value = m.category;
            document.getElementById('mat-unit').value = m.unit;
            document.getElementById('mat-location').value = m.location || '';
            document.getElementById('mat-stock').value = m.stock;
            document.getElementById('mat-target').value = m.target;

            document.getElementById('modal-material-title').innerText = '資材情報の編集';
            openMaterialModal(true);
        }

        function deleteMaterial(id) {
            if (confirm('この資材を削除してもよろしいですか？')) {
                materials = materials.filter(m => m.id !== id);
                saveData();
            }
        }

        // --- MACHINES LOGIC ---
        function renderMachines() {
            const container = document.getElementById('machines-grid');
            const searchVal = document.getElementById('machine-search').value.toLowerCase();
            const statusVal = document.getElementById('machine-filter-status').value;

            const filtered = machines.filter(m => {
                const matchesSearch = m.name.toLowerCase().includes(searchVal) || 
                                      (m.model && m.model.toLowerCase().includes(searchVal)) ||
                                      (m.location && m.location.toLowerCase().includes(searchVal));
                const matchesStatus = statusVal === 'ALL' || m.status === statusVal;
                return matchesSearch && matchesStatus;
            });

            if (filtered.length === 0) {
                container.innerHTML = '';
                document.getElementById('machines-empty-state').classList.remove('hidden');
                return;
            }
            document.getElementById('machines-empty-state').classList.add('hidden');

            const today = new Date().toISOString().split('T')[0];

            container.innerHTML = filtered.map(m => {
                let statusBadge = '';
                if (m.status === '稼働可能') {
                    statusBadge = `<span class="bg-emerald-100 text-emerald-800 text-xs font-bold px-2.5 py-1 rounded-full flex items-center gap-1"><span class="w-2 h-2 rounded-full bg-emerald-500"></span>稼働可能</span>`;
                } else if (m.status === '点検中') {
                    statusBadge = `<span class="bg-amber-100 text-amber-800 text-xs font-bold px-2.5 py-1 rounded-full flex items-center gap-1"><span class="w-2 h-2 rounded-full bg-amber-500"></span>点検中</span>`;
                } else {
                    statusBadge = `<span class="bg-red-100 text-red-800 text-xs font-bold px-2.5 py-1 rounded-full flex items-center gap-1"><span class="w-2 h-2 rounded-full bg-red-500"></span>故障中</span>`;
                }

                const isOverdue = m.nextDate && m.nextDate <= today;

                return `
                    <div class="bg-white rounded-xl border border-slate-200 shadow-sm hover:shadow-md transition flex flex-col justify-between overflow-hidden">
                        <div class="p-5">
                            <div class="flex justify-between items-start mb-3">
                                <div>
                                    <h3 class="font-bold text-slate-800 text-base">${escapeHtml(m.name)}</h3>
                                    <p class="text-xs text-slate-500 font-mono mt-0.5">${escapeHtml(m.model || '型式未登録')}</p>
                                </div>
                                ${statusBadge}
                            </div>
                            
                            <div class="space-y-2 text-xs text-slate-600 mt-4 border-t border-slate-100 pt-3">
                                <div class="flex justify-between">
                                    <span class="text-slate-400">保管場所:</span>
                                    <span class="font-medium text-slate-700">${escapeHtml(m.location || '-')}</span>
                                </div>
                                <div class="flex justify-between">
                                    <span class="text-slate-400">使用時間:</span>
                                    <span class="font-medium text-slate-700">${m.hours !== undefined && m.hours !== '' ? m.hours + ' 時間' : '-'}</span>
                                </div>
                                <div class="flex justify-between items-center">
                                    <span class="text-slate-400">次回点検予定:</span>
                                    <span class="font-medium ${isOverdue ? 'text-red-600 font-bold flex items-center gap-1' : 'text-slate-700'}">
                                        ${isOverdue ? '<i data-lucide="clock" class="w-3.5 h-3.5 inline"></i>' : ''}
                                        ${m.nextDate || '未設定'}
                                    </span>
                                </div>
                            </div>
                        </div>

                        <div class="bg-slate-50 border-t border-slate-100 px-5 py-3 flex justify-between items-center">
                            <div class="flex items-center gap-1">
                                <button onclick="quickUpdateMachineHours('${m.id}')" class="text-xs text-agri-700 hover:text-agri-900 font-medium underline flex items-center gap-1">
                                    <i data-lucide="plus-circle" class="w-3.5 h-3.5"></i> 時間更新
                                </button>
                            </div>
                            <div class="space-x-3">
                                <button onclick="editMachine('${m.id}')" class="text-slate-400 hover:text-agri-600 transition"><i data-lucide="edit-3" class="w-4 h-4"></i></button>
                                <button onclick="deleteMachine('${m.id}')" class="text-slate-400 hover:text-red-600 transition"><i data-lucide="trash-2" class="w-4 h-4"></i></button>
                            </div>
                        </div>
                    </div>
                `;
            }).join('');

            lucide.createIcons();
        }

        function openMachineModal(isEdit = false) {
            if (!isEdit) {
                document.getElementById('form-machine').reset();
                document.getElementById('mac-id').value = '';
                document.getElementById('modal-machine-title').innerText = '農機具の新規登録';
            }
            document.getElementById('modal-machine').classList.remove('hidden');
        }

        function closeMachineModal() {
            document.getElementById('modal-machine').classList.add('hidden');
        }

        function saveMachine(e) {
            e.preventDefault();
            const id = document.getElementById('mac-id').value;
            const macData = {
                id: id || 'mac_' + Date.now(),
                name: document.getElementById('mac-name').value,
                model: document.getElementById('mac-model').value,
                status: document.getElementById('mac-status').value,
                location: document.getElementById('mac-location').value,
                nextDate: document.getElementById('mac-next-date').value,
                hours: parseFloat(document.getElementById('mac-hours').value) || 0,
            };

            if (id) {
                const idx = machines.findIndex(m => m.id === id);
                if (idx !== -1) machines[idx] = macData;
            } else {
                machines.push(macData);
            }

            saveData();
            closeMachineModal();
        }

        function editMachine(id) {
            const m = machines.find(item => item.id === id);
            if (!m) return;

            document.getElementById('mac-id').value = m.id;
            document.getElementById('mac-name').value = m.name;
            document.getElementById('mac-model').value = m.model || '';
            document.getElementById('mac-status').value = m.status;
            document.getElementById('mac-location').value = m.location || '';
            document.getElementById('mac-next-date').value = m.nextDate || '';
            document.getElementById('mac-hours').value = m.hours || '';

            document.getElementById('modal-machine-title').innerText = '農機具情報の編集';
            openMachineModal(true);
        }

        function quickUpdateMachineHours(id) {
            const mac = machines.find(m => m.id === id);
            if (!mac) return;

            const input = prompt(`${mac.name} の最新稼働時間(h)を入力してください:`, mac.hours || 0);
            if (input !== null) {
                const val = parseFloat(input);
                if (!isNaN(val)) {
                    mac.hours = val;
                    saveData();
                }
            }
        }

        function deleteMachine(id) {
            if (confirm('この農機具の情報を削除してもよろしいですか？')) {
                machines = machines.filter(m => m.id !== id);
                saveData();
            }
        }

        // --- EXPORT FUNCTION ---
        function exportData() {
            const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify({ materials, machines }, null, 2));
            const downloadAnchor = document.createElement('a');
            downloadAnchor.setAttribute("href", dataStr);
            downloadAnchor.setAttribute("download", `agri_inventory_backup_${new Date().toISOString().split('T')[0]}.json`);
            document.body.appendChild(downloadAnchor);
            downloadAnchor.click();
            downloadAnchor.remove();
        }

        // UTILITY
        function escapeHtml(str) {
            if (!str) return '';
            return str.replace(/[&<>"']/g, function(m) {
                return {
                    '&': '&amp;',
                    '<': '&lt;',
                    '>': '&gt;',
                    '"': '&quot;',
                    "'": '&#039;'
                }[m];
            });
        }
    </script>
</body>
</html>
