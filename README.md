<!DOCTYPE html>
<html lang="zh-TW" class="h-full">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>單位智慧勤務管理系統 V1.0 - 離線安全版</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');
    body {
      font-family: 'Inter', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
      -webkit-tap-highlight-color: transparent;
    }
    .custom-scrollbar::-webkit-scrollbar {
      width: 4px;
      height: 4px;
    }
    .custom-scrollbar::-webkit-scrollbar-track {
      background: rgba(0, 0, 0, 0.05);
    }
    .custom-scrollbar::-webkit-scrollbar-thumb {
      background: rgba(0, 0, 0, 0.2);
      border-radius: 4px;
    }
  </style>
</head>
<body class="bg-slate-900 text-slate-100 min-h-full flex flex-col font-sans antialiased selection:bg-blue-600 selection:text-white">

  <!-- Top Navigation Header -->
  <header class="bg-slate-950 border-b border-slate-800 sticky top-0 z-30 shadow-md">
    <div class="max-w-5xl mx-auto px-4 py-3 flex items-center justify-between">
      <div class="flex items-center gap-2.5">
        <div class="w-9 h-9 rounded-lg bg-blue-600 flex items-center justify-center text-white font-bold text-lg shadow-lg shadow-blue-500/20">
          <i class="fa-solid fa-shield-halved"></i>
        </div>
        <div>
          <h1 class="text-base font-bold text-white leading-none tracking-wide flex items-center gap-2">
            智慧勤務管理系統
            <span class="text-[10px] px-1.5 py-0.5 rounded bg-blue-950 text-blue-400 border border-blue-800 font-mono">資安保密合規</span>
          </h1>
          <p class="text-xs text-slate-400 mt-0.5">智慧勤務 · 本機儲存版</p>
        </div>
      </div>
      <div class="flex items-center gap-2">
        <span class="inline-flex items-center gap-1.5 px-2.5 py-1 rounded-full text-xs font-medium bg-emerald-950/80 text-emerald-400 border border-emerald-800/60">
          <span class="w-2 h-2 rounded-full bg-emerald-400 animate-pulse"></span>
          已本機儲存
        </span>
      </div>
    </div>

    <!-- Tab Switching Buttons -->
    <div class="max-w-5xl mx-auto px-4 flex border-t border-slate-800/60">
      <button id="tab-handover-btn" onclick="switchTab('handover')" class="flex-1 py-3 px-2 text-center text-sm font-semibold border-b-2 border-blue-500 text-blue-400 flex items-center justify-center gap-2 transition-all">
        <i class="fa-solid fa-clipboard-list text-base"></i>
        <span>交接事項與備忘錄</span>
      </button>
      <button id="tab-access-btn" onclick="switchTab('access')" class="flex-1 py-3 px-2 text-center text-sm font-semibold border-b-2 border-transparent text-slate-400 hover:text-slate-200 flex items-center justify-center gap-2 transition-all">
        <i class="fa-solid fa-id-card-clip text-base"></i>
        <span>門禁人流管制登記</span>
      </button>
    </div>
  </header>

  <main class="flex-1 max-w-5xl w-full mx-auto p-4 sm:p-6 space-y-6">

    <!-- ==================== TOOL 1: HANDOVER & MEMO TOOL ==================== -->
    <section id="tool-handover" class="space-y-5">
      <!-- Top Overview & Stats -->
      <div class="bg-slate-800/80 backdrop-blur border border-slate-700/60 rounded-2xl p-4 sm:p-5 shadow-xl">
        <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-4 mb-4">
          <div>
            <h2 class="text-lg font-bold text-white flex items-center gap-2">
              <i class="fa-solid fa-list-check text-blue-400"></i>
              交接事項與列管案件
            </h2>
            <p class="text-xs text-slate-400 mt-1">追蹤主官交代事項、重要列管案件與負責幹部完成進度</p>
          </div>
          <button onclick="openHandoverModal()" class="w-full sm:w-auto px-4 py-2.5 bg-blue-600 hover:bg-blue-500 active:bg-blue-700 text-white font-medium rounded-xl text-sm transition-all shadow-lg shadow-blue-600/30 flex items-center justify-center gap-2">
            <i class="fa-solid fa-plus"></i>
            新增交接事項
          </button>
        </div>

        <!-- Progress Metrics -->
        <div class="grid grid-cols-3 gap-3 pt-3 border-t border-slate-700/50">
          <div class="bg-slate-900/60 rounded-xl p-3 border border-slate-700/40 text-center">
            <div class="text-xs text-slate-400">總交接數</div>
            <div id="stat-total-handover" class="text-2xl font-black text-white mt-0.5">0</div>
          </div>
          <div class="bg-slate-900/60 rounded-xl p-3 border border-slate-700/40 text-center">
            <div class="text-xs text-amber-400">處理/待辦</div>
            <div id="stat-pending-handover" class="text-2xl font-black text-amber-400 mt-0.5">0</div>
          </div>
          <div class="bg-slate-900/60 rounded-xl p-3 border border-slate-700/40 text-center">
            <div class="text-xs text-emerald-400">已完成</div>
            <div id="stat-completed-handover" class="text-2xl font-black text-emerald-400 mt-0.5">0</div>
          </div>
        </div>

        <!-- Progress Bar -->
        <div class="mt-4">
          <div class="flex justify-between text-xs text-slate-400 mb-1">
            <span>完成進度</span>
            <span id="handover-progress-percent" class="font-bold text-blue-400">0%</span>
          </div>
          <div class="w-full bg-slate-900 h-2.5 rounded-full overflow-hidden p-0.5 border border-slate-700/50">
            <div id="handover-progress-bar" class="bg-gradient-to-r from-blue-500 to-emerald-400 h-full rounded-full transition-all duration-500 w-0"></div>
          </div>
        </div>
      </div>

      <!-- Controls: Filter & Search -->
      <div class="flex flex-col sm:flex-row gap-3">
        <div class="relative flex-1">
          <i class="fa-solid fa-magnifying-glass absolute left-3.5 top-1/2 -translate-y-1/2 text-slate-400 text-sm"></i>
          <input type="text" id="handover-search" oninput="renderHandoverList()" placeholder="搜尋事項名稱、負責人、細節..." class="w-full bg-slate-800/90 border border-slate-700 rounded-xl pl-10 pr-4 py-2.5 text-sm text-white placeholder-slate-400 focus:outline-none focus:border-blue-500 focus:ring-1 focus:ring-blue-500 transition-all">
        </div>
        <div class="flex gap-2">
          <select id="handover-filter-status" onchange="renderHandoverList()" class="bg-slate-800 border border-slate-700 text-slate-200 text-sm rounded-xl px-3 py-2.5 focus:outline-none focus:border-blue-500">
            <option value="all">全部狀態</option>
            <option value="待辦">待辦 (Pending)</option>
            <option value="處理中">處理中 (In Progress)</option>
            <option value="已完成">已完成 (Completed)</option>
          </select>
          <select id="handover-filter-priority" onchange="renderHandoverList()" class="bg-slate-800 border border-slate-700 text-slate-200 text-sm rounded-xl px-3 py-2.5 focus:outline-none focus:border-blue-500">
            <option value="all">全部優先度</option>
            <option value="緊急">緊急</option>
            <option value="重要">重要</option>
            <option value="一般">一般</option>
          </select>
        </div>
      </div>

      <!-- Handover Cards List -->
      <div id="handover-list-container" class="grid grid-cols-1 md:grid-cols-2 gap-3.5">
        <!-- Rendered dynamically via JS -->
      </div>
    </section>

    <!-- ==================== TOOL 2: CAMP ENTRY/EXIT ACCESS CONTROL ==================== -->
    <section id="tool-access" class="space-y-5 hidden">
      <!-- Top Overview Dashboard -->
      <div class="grid grid-cols-2 sm:grid-cols-4 gap-3">
        <div class="bg-slate-800/80 border border-slate-700/60 rounded-2xl p-4 text-center shadow-lg">
          <div class="text-xs font-medium text-emerald-400 flex items-center justify-center gap-1">
            <span class="w-2 h-2 rounded-full bg-emerald-400 animate-ping"></span>
            目前在營人員
          </div>
          <div id="stat-in-camp-personnel" class="text-3xl font-black text-emerald-400 mt-1">0</div>
          <div class="text-[11px] text-slate-400 mt-0.5">含訪客與官兵</div>
        </div>

        <div class="bg-slate-800/80 border border-slate-700/60 rounded-2xl p-4 text-center shadow-lg">
          <div class="text-xs font-medium text-blue-400 flex items-center justify-center gap-1">
            <i class="fa-solid fa-car text-xs"></i>
            目前在營車輛
          </div>
          <div id="stat-in-camp-vehicles" class="text-3xl font-black text-blue-400 mt-1">0</div>
          <div class="text-[11px] text-slate-400 mt-0.5">各式管制車輛</div>
        </div>

        <div class="bg-slate-800/80 border border-slate-700/60 rounded-2xl p-4 text-center shadow-lg">
          <div class="text-xs font-medium text-slate-400">今日進營人次</div>
          <div id="stat-today-entry" class="text-3xl font-black text-white mt-1">0</div>
          <div class="text-[11px] text-slate-400 mt-0.5">當日累計進營</div>
        </div>

        <div class="bg-slate-800/80 border border-slate-700/60 rounded-2xl p-4 text-center shadow-lg">
          <div class="text-xs font-medium text-slate-400">今日出營人次</div>
          <div id="stat-today-exit" class="text-3xl font-black text-slate-300 mt-1">0</div>
          <div class="text-[11px] text-slate-400 mt-0.5">當日累計離營</div>
        </div>
      </div>

      <!-- Main Action Buttons -->
      <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
        <button onclick="openAccessModal('進營')" class="bg-gradient-to-r from-emerald-600 to-teal-600 hover:from-emerald-500 hover:to-teal-500 text-white font-bold p-4 rounded-2xl shadow-lg shadow-emerald-900/30 flex items-center justify-between transition-all active:scale-[0.99]">
          <div class="text-left">
            <div class="text-lg flex items-center gap-2">
              <i class="fa-solid fa-right-to-bracket"></i>
              登記進營 (Check-In)
            </div>
            <div class="text-xs text-emerald-100/80 font-normal mt-0.5">新增官兵、民用車輛、會客人員進營紀錄</div>
          </div>
          <i class="fa-solid fa-circle-chevron-right text-xl opacity-80"></i>
        </button>

        <button onclick="openAccessModal('出營')" class="bg-gradient-to-r from-amber-600 to-orange-600 hover:from-amber-500 hover:to-orange-500 text-white font-bold p-4 rounded-2xl shadow-lg shadow-orange-900/30 flex items-center justify-between transition-all active:scale-[0.99]">
          <div class="text-left">
            <div class="text-lg flex items-center gap-2">
              <i class="fa-solid fa-right-from-bracket"></i>
              登記出營 (Check-Out)
            </div>
            <div class="text-xs text-amber-100/80 font-normal mt-0.5">公差、外宿、車輛派飛出營登記</div>
          </div>
          <i class="fa-solid fa-circle-chevron-right text-xl opacity-80"></i>
        </button>
      </div>

      <!-- Controls & Filters for Access Logs -->
      <div class="bg-slate-800/80 border border-slate-700/60 rounded-2xl p-4 space-y-3">
        <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-3">
          <h3 class="font-bold text-white text-base flex items-center gap-2">
            <i class="fa-solid fa-clock-rotate-left text-blue-400"></i>
            進出動態即時紀錄
          </h3>
          <div class="flex items-center gap-2 w-full sm:w-auto">
            <button onclick="exportAccessCSV()" class="flex-1 sm:flex-none px-3 py-1.5 bg-slate-700 hover:bg-slate-600 text-slate-200 text-xs rounded-lg font-medium transition-all flex items-center justify-center gap-1.5">
              <i class="fa-solid fa-file-csv text-emerald-400"></i>
              匯出 CSV 報表
            </button>
          </div>
        </div>

        <div class="flex flex-col sm:flex-row gap-2.5">
          <div class="relative flex-1">
            <i class="fa-solid fa-magnifying-glass absolute left-3 top-1/2 -translate-y-1/2 text-slate-400 text-xs"></i>
            <input type="text" id="access-search" oninput="renderAccessLogs()" placeholder="搜尋姓名、車號、單位、事由..." class="w-full bg-slate-900/90 border border-slate-700 rounded-xl pl-9 pr-3 py-2 text-xs text-white placeholder-slate-400 focus:outline-none focus:border-blue-500">
          </div>
          <div class="flex gap-2">
            <select id="access-filter-category" onchange="renderAccessLogs()" class="bg-slate-900 border border-slate-700 text-slate-200 text-xs rounded-xl px-3 py-2 focus:outline-none focus:border-blue-500">
              <option value="all">全部類型 (人員/車輛)</option>
              <option value="人員">人員</option>
              <option value="車輛">車輛</option>
            </select>
            <select id="access-filter-status" onchange="renderAccessLogs()" class="bg-slate-900 border border-slate-700 text-slate-200 text-xs rounded-xl px-3 py-2 focus:outline-none focus:border-blue-500">
              <option value="all">全部狀態</option>
              <option value="在營">目前在營</option>
              <option value="已離營">已離營</option>
            </select>
          </div>
        </div>
      </div>

      <!-- Log Table / Stream -->
      <div id="access-log-container" class="space-y-2.5">
        <!-- Dynamic content rendered by JS -->
      </div>
    </section>
  </main>

  <!-- MODAL 1: Add/Edit Handover Item -->
  <div id="handover-modal" class="fixed inset-0 z-50 bg-slate-950/80 backdrop-blur-sm hidden flex items-center justify-center p-4">
    <div class="bg-slate-800 border border-slate-700 rounded-2xl w-full max-w-lg overflow-hidden shadow-2xl animate-in fade-in zoom-in duration-200">
      <div class="px-5 py-4 bg-slate-900 border-b border-slate-700 flex justify-between items-center">
        <h3 id="handover-modal-title" class="text-base font-bold text-white flex items-center gap-2">
          <i class="fa-solid fa-pen-to-square text-blue-400"></i>
          新增交接與列管事項
        </h3>
        <button onclick="closeHandoverModal()" class="text-slate-400 hover:text-white text-lg w-8 h-8 rounded-lg flex items-center justify-center hover:bg-slate-800">
          <i class="fa-solid fa-xmark"></i>
        </button>
      </div>

      <form id="handover-form" onsubmit="saveHandoverItem(event)" class="p-5 space-y-4">
        <input type="hidden" id="handover-id">
        
        <div>
          <label class="block text-xs font-semibold text-slate-300 mb-1">事項標題 / 案由 <span class="text-rose-400">*</span></label>
          <input type="text" id="handover-title" required placeholder="例：營區主機定期維護、115年第3季裝備盤點..." class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2.5 text-sm text-white focus:border-blue-500 focus:outline-none">
        </div>

        <div class="grid grid-cols-2 gap-3">
          <div>
            <label class="block text-xs font-semibold text-slate-300 mb-1">負責幹部 / 人員 <span class="text-rose-400">*</span></label>
            <input type="text" id="handover-assignee" required placeholder="例：張連長、陳士官長" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2.5 text-sm text-white focus:border-blue-500 focus:outline-none">
          </div>
          <div>
            <label class="block text-xs font-semibold text-slate-300 mb-1">優先程度</label>
            <select id="handover-priority" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3 py-2.5 text-sm text-white focus:border-blue-500 focus:outline-none">
              <option value="一般">一般 (Normal)</option>
              <option value="重要">重要 (Important)</option>
              <option value="緊急">緊急 (Urgent)</option>
            </select>
          </div>
        </div>

        <div class="grid grid-cols-2 gap-3">
          <div>
            <label class="block text-xs font-semibold text-slate-300 mb-1">預計完成日期</label>
            <input type="date" id="handover-date" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2 text-sm text-white focus:border-blue-500 focus:outline-none">
          </div>
          <div>
            <label class="block text-xs font-semibold text-slate-300 mb-1">目前進度狀態</label>
            <select id="handover-status" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2.5 text-sm text-white focus:border-blue-500 focus:outline-none">
              <option value="待辦">待辦 (Pending)</option>
              <option value="處理中">處理中 (In Progress)</option>
              <option value="已完成">已完成 (Completed)</option>
            </select>
          </div>
        </div>

        <div>
          <label class="block text-xs font-semibold text-slate-300 mb-1">詳細備忘與指示說明</label>
          <textarea id="handover-notes" rows="3" placeholder="請填寫交接細節、辦理要求或關聯文件字號..." class="w-full bg-slate-900 border border-slate-700 rounded-xl p-3 text-sm text-white focus:border-blue-500 focus:outline-none resize-none"></textarea>
        </div>

        <div class="pt-2 flex justify-end gap-2.5">
          <button type="button" onclick="closeHandoverModal()" class="px-4 py-2 bg-slate-700 hover:bg-slate-600 text-slate-200 text-sm font-medium rounded-xl">取消</button>
          <button type="submit" class="px-5 py-2 bg-blue-600 hover:bg-blue-500 text-white text-sm font-semibold rounded-xl shadow-lg shadow-blue-600/30">儲存事項</button>
        </div>
      </form>
    </div>
  </div>

  <!-- MODAL 2: Add Entry/Exit Access Record -->
  <div id="access-modal" class="fixed inset-0 z-50 bg-slate-950/80 backdrop-blur-sm hidden flex items-center justify-center p-4">
    <div class="bg-slate-800 border border-slate-700 rounded-2xl w-full max-w-lg overflow-hidden shadow-2xl animate-in fade-in zoom-in duration-200">
      <div id="access-modal-header" class="px-5 py-4 bg-emerald-950 border-b border-emerald-800/60 flex justify-between items-center">
        <h3 id="access-modal-title" class="text-base font-bold text-emerald-300 flex items-center gap-2">
          <i class="fa-solid fa-right-to-bracket"></i>
          登記進營
        </h3>
        <button onclick="closeAccessModal()" class="text-slate-400 hover:text-white text-lg w-8 h-8 rounded-lg flex items-center justify-center hover:bg-slate-800">
          <i class="fa-solid fa-xmark"></i>
        </button>
      </div>

      <form id="access-form" onsubmit="saveAccessRecord(event)" class="p-5 space-y-4">
        <input type="hidden" id="access-direction" value="進營">

        <div class="grid grid-cols-2 gap-3">
          <div>
            <label class="block text-xs font-semibold text-slate-300 mb-1">管制對象類別</label>
            <select id="access-category" onchange="toggleCategoryFields()" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3 py-2.5 text-sm text-white focus:border-blue-500 focus:outline-none">
              <option value="人員">人員 (官兵/廠商/訪客)</option>
              <option value="車輛">車輛 (軍車/民車/工程車)</option>
            </select>
          </div>
          <div>
            <label class="block text-xs font-semibold text-slate-300 mb-1">登記時間</label>
            <input type="text" id="access-time" readonly class="w-full bg-slate-950 border border-slate-800 text-slate-400 font-mono rounded-xl px-3 py-2.5 text-xs">
          </div>
        </div>

        <div class="grid grid-cols-2 gap-3">
          <div>
            <label id="access-name-label" class="block text-xs font-semibold text-slate-300 mb-1">姓名 / 駕駛人 <span class="text-rose-400">*</span></label>
            <input type="text" id="access-name" required placeholder="例：王小明" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2.5 text-sm text-white focus:border-blue-500 focus:outline-none">
          </div>
          <div>
            <label class="block text-xs font-semibold text-slate-300 mb-1">所屬單位 / 公司</label>
            <input type="text" id="access-unit" placeholder="例：本部連、維修廠商" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2.5 text-sm text-white focus:border-blue-500 focus:outline-none">
          </div>
        </div>

        <div id="field-vehicle-group" class="grid grid-cols-2 gap-3">
          <div>
            <label class="block text-xs font-semibold text-slate-300 mb-1">車牌號碼 / 軍車號</label>
            <input type="text" id="access-vehicle-no" placeholder="例：ABC-1234 或 軍-54321" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2.5 text-sm text-white focus:border-blue-500 focus:outline-none">
          </div>
          <div>
            <label class="block text-xs font-semibold text-slate-300 mb-1">隨車人數/同行人數</label>
            <input type="number" id="access-passengers" value="1" min="1" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2.5 text-sm text-white focus:border-blue-500 focus:outline-none">
          </div>
        </div>

        <div class="grid grid-cols-2 gap-3">
          <div>
            <label class="block text-xs font-semibold text-slate-300 mb-1">事由 / 任務名稱 <span class="text-rose-400">*</span></label>
            <input type="text" id="access-purpose" required placeholder="例：收假歸營、營區維修、公差" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2.5 text-sm text-white focus:border-blue-500 focus:outline-none">
          </div>
          <div>
            <label class="block text-xs font-semibold text-slate-300 mb-1">體溫 / 備註資訊</label>
            <input type="text" id="access-temp" placeholder="例：36.5℃, 帶通行證" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2.5 text-sm text-white focus:border-blue-500 focus:outline-none">
          </div>
        </div>

        <div class="pt-2 flex justify-end gap-2.5">
          <button type="button" onclick="closeAccessModal()" class="px-4 py-2 bg-slate-700 hover:bg-slate-600 text-slate-200 text-sm font-medium rounded-xl">取消</button>
          <button type="submit" id="access-submit-btn" class="px-5 py-2 bg-emerald-600 hover:bg-emerald-500 text-white text-sm font-semibold rounded-xl shadow-lg shadow-emerald-600/30">確認登記進營</button>
        </div>
      </form>
    </div>
  </div>

  <!-- Custom Alert/Toast Notification Component -->
  <div id="toast" class="fixed bottom-5 right-5 z-50 transform translate-y-20 opacity-0 transition-all duration-300 pointer-events-none">
    <div class="bg-slate-800 border border-slate-700 text-slate-100 px-4 py-3 rounded-xl shadow-2xl flex items-center gap-2.5 text-sm">
      <i id="toast-icon" class="fa-solid fa-circle-check text-emerald-400 text-base"></i>
      <span id="toast-message">操作成功</span>
    </div>
  </div>

  <script>
    /* Global Application State */
    const STORAGE_KEY_HANDOVER = 'mil_handover_items_v1';
    const STORAGE_KEY_ACCESS = 'mil_access_records_v1';

    let handoverItems = [];
    let accessRecords = [];

    // Initialize Application
    window.addEventListener('DOMContentLoaded', () => {
      loadDataFromStorage();
      renderHandoverList();
      renderAccessLogs();
      updateAccessStats();
    });

    /* Data Persistence Helpers */
    function loadDataFromStorage() {
      try {
        const savedHandovers = localStorage.getItem(STORAGE_KEY_HANDOVER);
        if (savedHandovers) {
          handoverItems = JSON.parse(savedHandovers);
        } else {
          handoverItems = [];
          saveHandoverToStorage();
        }

        const savedAccess = localStorage.getItem(STORAGE_KEY_ACCESS);
        if (savedAccess) {
          accessRecords = JSON.parse(savedAccess);
        } else {
          accessRecords = [];
          saveAccessToStorage();
        }
      } catch (e) {
        console.error('Failed to parse local storage data:', e);
      }
    }

    function saveHandoverToStorage() {
      localStorage.setItem(STORAGE_KEY_HANDOVER, JSON.stringify(handoverItems));
      updateHandoverStats();
    }

    function saveAccessToStorage() {
      localStorage.setItem(STORAGE_KEY_ACCESS, JSON.stringify(accessRecords));
      updateAccessStats();
    }

    function getFormattedNow() {
      const now = new Date();
      const year = now.getFullYear();
      const month = String(now.getMonth() + 1).padStart(2, '0');
      const day = String(now.getDate()).padStart(2, '0');
      const hours = String(now.getHours()).padStart(2, '0');
      const minutes = String(now.getMinutes()).padStart(2, '0');
      return `${year}-${month}-${day} ${hours}:${minutes}`;
    }

    /* UI Navigation Tab Switching */
    function switchTab(tab) {
      const handoverSec = document.getElementById('tool-handover');
      const accessSec = document.getElementById('tool-access');
      const handoverBtn = document.getElementById('tab-handover-btn');
      const accessBtn = document.getElementById('tab-access-btn');

      if (tab === 'handover') {
        handoverSec.classList.remove('hidden');
        accessSec.classList.add('hidden');
        
        handoverBtn.className = "flex-1 py-3 px-2 text-center text-sm font-semibold border-b-2 border-blue-500 text-blue-400 flex items-center justify-center gap-2 transition-all";
        accessBtn.className = "flex-1 py-3 px-2 text-center text-sm font-semibold border-b-2 border-transparent text-slate-400 hover:text-slate-200 flex items-center justify-center gap-2 transition-all";
      } else {
        handoverSec.classList.add('hidden');
        accessSec.classList.remove('hidden');

        accessBtn.className = "flex-1 py-3 px-2 text-center text-sm font-semibold border-b-2 border-blue-500 text-blue-400 flex items-center justify-center gap-2 transition-all";
        handoverBtn.className = "flex-1 py-3 px-2 text-center text-sm font-semibold border-b-2 border-transparent text-slate-400 hover:text-slate-200 flex items-center justify-center gap-2 transition-all";
      }
    }

    /* Toast Notification */
    function showToast(msg, isSuccess = true) {
      const toast = document.getElementById('toast');
      const toastMsg = document.getElementById('toast-message');
      const toastIcon = document.getElementById('toast-icon');

      toastMsg.textContent = msg;
      toastIcon.className = isSuccess 
        ? "fa-solid fa-circle-check text-emerald-400 text-base" 
        : "fa-solid fa-triangle-exclamation text-amber-400 text-base";

      toast.classList.remove('translate-y-20', 'opacity-0');
      setTimeout(() => {
        toast.classList.add('translate-y-20', 'opacity-0');
      }, 2500);
    }

    /* ================= TOOL 1: HANDOVER LOGIC ================= */
    function updateHandoverStats() {
      const total = handoverItems.length;
      const completed = handoverItems.filter(i => i.status === '已完成').length;
      const pending = total - completed;
      const percent = total === 0 ? 0 : Math.round((completed / total) * 100);

      document.getElementById('stat-total-handover').textContent = total;
      document.getElementById('stat-pending-handover').textContent = pending;
      document.getElementById('stat-completed-handover').textContent = completed;
      document.getElementById('handover-progress-percent').textContent = `${percent}%`;
      document.getElementById('handover-progress-bar').style.width = `${percent}%`;
    }

    function renderHandoverList() {
      const container = document.getElementById('handover-list-container');
      const query = document.getElementById('handover-search').value.toLowerCase().trim();
      const filterStatus = document.getElementById('handover-filter-status').value;
      const filterPriority = document.getElementById('handover-filter-priority').value;

      let filtered = handoverItems.filter(item => {
        const matchesQuery = item.title.toLowerCase().includes(query) ||
                             item.assignee.toLowerCase().includes(query) ||
                             (item.notes && item.notes.toLowerCase().includes(query));
        const matchesStatus = filterStatus === 'all' || item.status === filterStatus;
        const matchesPriority = filterPriority === 'all' || item.priority === filterPriority;
        return matchesQuery && matchesStatus && matchesPriority;
      });

      updateHandoverStats();

      if (filtered.length === 0) {
        container.innerHTML = `
          <div class="col-span-full bg-slate-800/40 border border-slate-800 rounded-2xl p-8 text-center text-slate-500">
            <i class="fa-solid fa-folder-open text-3xl mb-2 opacity-50"></i>
            <p class="text-sm">尚無相關交接事項或符合條件的列管案</p>
          </div>
        `;
        return;
      }

      container.innerHTML = filtered.map(item => {
        let priorityBadge = '';
        if (item.priority === '緊急') {
          priorityBadge = `<span class="px-2 py-0.5 text-[11px] font-bold rounded-md bg-rose-950/80 text-rose-400 border border-rose-800/60">緊急</span>`;
        } else if (item.priority === '重要') {
          priorityBadge = `<span class="px-2 py-0.5 text-[11px] font-bold rounded-md bg-amber-950/80 text-amber-400 border border-amber-800/60">重要</span>`;
        } else {
          priorityBadge = `<span class="px-2 py-0.5 text-[11px] font-medium rounded-md bg-slate-800 text-slate-400 border border-slate-700">一般</span>`;
        }

        let statusClass = '';
        let statusIcon = '';
        if (item.status === '已完成') {
          statusClass = 'bg-emerald-950/40 border-emerald-800/50 text-emerald-300';
          statusIcon = '<i class="fa-solid fa-circle-check"></i>';
        } else if (item.status === '處理中') {
          statusClass = 'bg-blue-950/40 border-blue-800/50 text-blue-300';
          statusIcon = '<i class="fa-solid fa-spinner animate-spin"></i>';
        } else {
          statusClass = 'bg-slate-900/60 border-slate-700 text-slate-300';
          statusIcon = '<i class="fa-regular fa-circle"></i>';
        }

        return `
          <div class="bg-slate-800/90 border border-slate-700/80 rounded-2xl p-4 flex flex-col justify-between hover:border-slate-600 transition-all shadow-md">
            <div>
              <div class="flex items-start justify-between gap-2 mb-2">
                <div class="flex items-center gap-2 flex-wrap">
                  ${priorityBadge}
                  <h4 class="font-bold text-white text-base leading-snug">${escapeHtml(item.title)}</h4>
                </div>
                <button onclick="toggleHandoverStatus('${item.id}')" class="px-2.5 py-1 text-xs rounded-lg border font-medium flex items-center gap-1.5 transition-all ${statusClass}">
                  ${statusIcon}
                  <span>${item.status}</span>
                </button>
              </div>

              <div class="text-xs text-slate-400 space-y-1 mb-3">
                <div class="flex items-center gap-4">
                  <span><i class="fa-solid fa-user-gear text-slate-500 mr-1"></i>負責人: <strong class="text-slate-200">${escapeHtml(item.assignee)}</strong></span>
                  ${item.date ? `<span><i class="fa-regular fa-calendar-check text-slate-500 mr-1"></i>期限: ${item.date}</span>` : ''}
                </div>
              </div>

              ${item.notes ? `
                <div class="bg-slate-900/70 border border-slate-800 rounded-xl p-2.5 text-xs text-slate-300 leading-relaxed mb-3 font-mono">
                  ${escapeHtml(item.notes)}
                </div>
              ` : ''}
            </div>

            <div class="flex items-center justify-between pt-2 border-t border-slate-700/50 text-xs">
              <span class="text-slate-500 text-[11px]">建立: ${item.createdAt ? item.createdAt.substring(0,10) : '近期'}</span>
              <div class="flex items-center gap-2">
                <button onclick="openHandoverModal('${item.id}')" class="text-slate-400 hover:text-blue-400 p-1">
                  <i class="fa-solid fa-pen-to-square"></i> 編輯
                </button>
                <button onclick="deleteHandoverItem('${item.id}')" class="text-slate-400 hover:text-rose-400 p-1">
                  <i class="fa-solid fa-trash-can"></i> 刪除
                </button>
              </div>
            </div>
          </div>
        `;
      }).join('');
    }

    function openHandoverModal(id = null) {
      const modal = document.getElementById('handover-modal');
      const modalTitle = document.getElementById('handover-modal-title');
      const form = document.getElementById('handover-form');

      form.reset();
      document.getElementById('handover-id').value = '';

      if (id) {
        const item = handoverItems.find(i => i.id === id);
        if (item) {
          modalTitle.innerHTML = `<i class="fa-solid fa-pen-to-square text-blue-400"></i> 編輯事項`;
          document.getElementById('handover-id').value = item.id;
          document.getElementById('handover-title').value = item.title;
          document.getElementById('handover-assignee').value = item.assignee;
          document.getElementById('handover-priority').value = item.priority;
          document.getElementById('handover-date').value = item.date || '';
          document.getElementById('handover-status').value = item.status;
          document.getElementById('handover-notes').value = item.notes || '';
        }
      } else {
        modalTitle.innerHTML = `<i class="fa-solid fa-plus text-blue-400"></i> 新增交接與列管事項`;
        document.getElementById('handover-date').value = new Date().toISOString().split('T')[0];
      }

      modal.classList.remove('hidden');
    }

    function closeHandoverModal() {
      document.getElementById('handover-modal').classList.add('hidden');
    }

    function saveHandoverItem(e) {
      e.preventDefault();
      const id = document.getElementById('handover-id').value;
      const title = document.getElementById('handover-title').value.trim();
      const assignee = document.getElementById('handover-assignee').value.trim();
      const priority = document.getElementById('handover-priority').value;
      const date = document.getElementById('handover-date').value;
      const status = document.getElementById('handover-status').value;
      const notes = document.getElementById('handover-notes').value.trim();

      if (!title || !assignee) return;

      if (id) {
        const index = handoverItems.findIndex(i => i.id === id);
        if (index !== -1) {
          handoverItems[index] = { ...handoverItems[index], title, assignee, priority, date, status, notes };
          showToast('事項已順利更新');
        }
      } else {
        const newItem = {
          id: 'ho_' + Date.now(),
          title,
          assignee,
          priority,
          date,
          status,
          notes,
          createdAt: new Date().toISOString()
        };
        handoverItems.unshift(newItem);
        showToast('已新增交接事項');
      }

      saveHandoverToStorage();
      renderHandoverList();
      closeHandoverModal();
    }

    function toggleHandoverStatus(id) {
      const item = handoverItems.find(i => i.id === id);
      if (item) {
        if (item.status === '待辦') item.status = '處理中';
        else if (item.status === '處理中') item.status = '已完成';
        else item.status = '待辦';

        saveHandoverToStorage();
        renderHandoverList();
        showToast(`狀態變更為：${item.status}`);
      }
    }

    function deleteHandoverItem(id) {
      if (confirm('確定要刪除此筆交接事項嗎？')) {
        handoverItems = handoverItems.filter(i => i.id !== id);
        saveHandoverToStorage();
        renderHandoverList();
        showToast('已刪除該事項', false);
      }
    }

    /* ================= TOOL 2: ACCESS CONTROL LOGIC ================= */
    function updateAccessStats() {
      const inCampPersonnel = accessRecords.filter(r => r.status === '在營' && r.category === '人員')
        .reduce((sum, r) => sum + (Number(r.passengers) || 1), 0);
        
      const inCampVehicles = accessRecords.filter(r => r.status === '在營' && r.category === '車輛').length;

      const todayStr = new Date().toISOString().substring(0, 10);
      const todayEntries = accessRecords.filter(r => r.direction === '進營' && r.entryTime && r.entryTime.startsWith(todayStr)).length;
      const todayExits = accessRecords.filter(r => r.exitTime && r.exitTime.startsWith(todayStr)).length;

      document.getElementById('stat-in-camp-personnel').textContent = inCampPersonnel;
      document.getElementById('stat-in-camp-vehicles').textContent = inCampVehicles;
      document.getElementById('stat-today-entry').textContent = todayEntries;
      document.getElementById('stat-today-exit').textContent = todayExits;
    }

    function renderAccessLogs() {
      const container = document.getElementById('access-log-container');
      const query = document.getElementById('access-search').value.toLowerCase().trim();
      const filterCat = document.getElementById('access-filter-category').value;
      const filterStatus = document.getElementById('access-filter-status').value;

      let filtered = accessRecords.filter(r => {
        const matchesQuery = r.name.toLowerCase().includes(query) ||
                             (r.unit && r.unit.toLowerCase().includes(query)) ||
                             (r.vehicleNo && r.vehicleNo.toLowerCase().includes(query)) ||
                             (r.purpose && r.purpose.toLowerCase().includes(query));
        const matchesCat = filterCat === 'all' || r.category === filterCat;
        const matchesStatus = filterStatus === 'all' || r.status === filterStatus;
        return matchesQuery && matchesCat && matchesStatus;
      });

      updateAccessStats();

      if (filtered.length === 0) {
        container.innerHTML = `
          <div class="bg-slate-800/40 border border-slate-800 rounded-2xl p-8 text-center text-slate-500">
            <i class="fa-solid fa-address-card text-3xl mb-2 opacity-50"></i>
            <p class="text-sm">尚無任何門禁進出登記資料</p>
          </div>
        `;
        return;
      }

      container.innerHTML = filtered.map(r => {
        const isInCamp = r.status === '在營';
        const isVehicle = r.category === '車輛';

        return `
          <div class="bg-slate-800/90 border border-slate-700/80 rounded-xl p-3.5 flex flex-col sm:flex-row sm:items-center justify-between gap-3 shadow-md hover:border-slate-600 transition-all">
            <div class="flex items-start gap-3">
              <div class="w-10 h-10 rounded-xl ${isVehicle ? 'bg-indigo-950 text-indigo-400 border-indigo-800' : 'bg-teal-950 text-teal-400 border-teal-800'} border flex items-center justify-center text-base shrink-0 mt-0.5">
                <i class="${isVehicle ? 'fa-solid fa-car' : 'fa-solid fa-user-shield'}"></i>
              </div>

              <div class="space-y-1">
                <div class="flex items-center gap-2 flex-wrap">
                  <span class="font-bold text-white text-base">${escapeHtml(r.name)}</span>
                  ${r.unit ? `<span class="text-xs px-2 py-0.5 rounded bg-slate-700 text-slate-300">${escapeHtml(r.unit)}</span>` : ''}
                  ${r.vehicleNo ? `<span class="text-xs font-mono font-bold px-2 py-0.5 rounded bg-amber-950 text-amber-300 border border-amber-800"><i class="fa-solid fa-car-side mr-1"></i>${escapeHtml(r.vehicleNo)}</span>` : ''}
                  <span class="text-xs font-semibold px-2 py-0.5 rounded ${isInCamp ? 'bg-emerald-950 text-emerald-400 border border-emerald-800' : 'bg-slate-700 text-slate-400'}">
                    ${r.status}
                  </span>
                </div>

                <div class="text-xs text-slate-400 flex flex-wrap gap-x-4 gap-y-1">
                  <span>事由: <strong class="text-slate-300">${escapeHtml(r.purpose)}</strong></span>
                  ${r.passengers > 1 ? `<span>隨行人數: <strong class="text-slate-300">${r.passengers} 人</strong></span>` : ''}
                  ${r.temp ? `<span>備註/體溫: <strong class="text-slate-300">${escapeHtml(r.temp)}</strong></span>` : ''}
                </div>

                <div class="text-[11px] text-slate-500 font-mono flex items-center gap-3 pt-0.5">
                  <span><i class="fa-solid fa-right-to-bracket text-emerald-500 mr-1"></i>進營: ${r.entryTime || '無紀錄'}</span>
                  ${r.exitTime ? `<span><i class="fa-solid fa-right-from-bracket text-amber-500 mr-1"></i>離營: ${r.exitTime}</span>` : ''}
                </div>
              </div>
            </div>

            <div class="flex items-center gap-2 self-end sm:self-center shrink-0">
              ${isInCamp ? `
                <button onclick="checkoutRecord('${r.id}')" class="px-3 py-1.5 bg-amber-600 hover:bg-amber-500 text-white text-xs font-bold rounded-lg transition-all flex items-center gap-1 shadow-md shadow-amber-900/20">
                  <i class="fa-solid fa-person-walking-arrow-right"></i>
                  登記離營
                </button>
              ` : `
                <button onclick="reEntryRecord('${r.id}')" class="px-3 py-1.5 bg-slate-700 hover:bg-slate-600 text-slate-200 text-xs font-medium rounded-lg transition-all flex items-center gap-1">
                  <i class="fa-solid fa-rotate-left"></i>
                  再次進營
                </button>
              `}
              <button onclick="deleteAccessRecord('${r.id}')" class="p-1.5 text-slate-500 hover:text-rose-400 text-xs">
                <i class="fa-solid fa-trash-can"></i>
              </button>
            </div>
          </div>
        `;
      }).join('');
    }

    function openAccessModal(direction = '進營') {
      const modal = document.getElementById('access-modal');
      const header = document.getElementById('access-modal-header');
      const title = document.getElementById('access-modal-title');
      const btn = document.getElementById('access-submit-btn');

      document.getElementById('access-form').reset();
      document.getElementById('access-direction').value = direction;
      document.getElementById('access-time').value = getFormattedNow();

      if (direction === '進營') {
        header.className = "px-5 py-4 bg-emerald-950 border-b border-emerald-800/60 flex justify-between items-center";
        title.className = "text-base font-bold text-emerald-300 flex items-center gap-2";
        title.innerHTML = `<i class="fa-solid fa-right-to-bracket"></i> 登記人員/車輛進營`;
        btn.className = "px-5 py-2 bg-emerald-600 hover:bg-emerald-500 text-white text-sm font-semibold rounded-xl shadow-lg shadow-emerald-600/30";
        btn.textContent = "確認登記進營";
      } else {
        header.className = "px-5 py-4 bg-amber-950 border-b border-amber-800/60 flex justify-between items-center";
        title.className = "text-base font-bold text-amber-300 flex items-center gap-2";
        title.innerHTML = `<i class="fa-solid fa-right-from-bracket"></i> 登記人員/車輛出營`;
        btn.className = "px-5 py-2 bg-amber-600 hover:bg-amber-500 text-white text-sm font-semibold rounded-xl shadow-lg shadow-amber-600/30";
        btn.textContent = "確認登記離營";
      }

      toggleCategoryFields();
      modal.classList.remove('hidden');
    }

    function closeAccessModal() {
      document.getElementById('access-modal').classList.add('hidden');
    }

    function toggleCategoryFields() {
      const cat = document.getElementById('access-category').value;
      const nameLabel = document.getElementById('access-name-label');

      if (cat === '車輛') {
        nameLabel.innerHTML = `駕駛人姓名 <span class="text-rose-400">*</span>`;
      } else {
        nameLabel.innerHTML = `進出人姓名 <span class="text-rose-400">*</span>`;
      }
    }

    function saveAccessRecord(e) {
      e.preventDefault();
      const direction = document.getElementById('access-direction').value;
      const category = document.getElementById('access-category').value;
      const name = document.getElementById('access-name').value.trim();
      const unit = document.getElementById('access-unit').value.trim();
      const vehicleNo = document.getElementById('access-vehicle-no').value.trim();
      const passengers = document.getElementById('access-passengers').value || 1;
      const purpose = document.getElementById('access-purpose').value.trim();
      const temp = document.getElementById('access-temp').value.trim();
      const currentTime = getFormattedNow();

      if (!name || !purpose) return;

      const newRecord = {
        id: 'ac_' + Date.now(),
        direction,
        category,
        name,
        unit,
        vehicleNo,
        passengers: Number(passengers),
        purpose,
        temp,
        status: direction === '進營' ? '在營' : '已離營',
        entryTime: direction === '進營' ? currentTime : null,
        exitTime: direction === '出營' ? currentTime : null
      };

      accessRecords.unshift(newRecord);
      saveAccessToStorage();
      renderAccessLogs();
      closeAccessModal();
      showToast(`已完成${direction}登記`);
    }

    function checkoutRecord(id) {
      const record = accessRecords.find(r => r.id === id);
      if (record) {
        record.status = '已離營';
        record.exitTime = getFormattedNow();
        saveAccessToStorage();
        renderAccessLogs();
        showToast(`${record.name} 已登記離營`);
      }
    }

    function reEntryRecord(id) {
      const record = accessRecords.find(r => r.id === id);
      if (record) {
        record.status = '在營';
        record.entryTime = getFormattedNow();
        record.exitTime = null;
        saveAccessToStorage();
        renderAccessLogs();
        showToast(`${record.name} 已重新登記進營`);
      }
    }

    function deleteAccessRecord(id) {
      if (confirm('確定要刪除這筆門禁紀錄嗎？')) {
        accessRecords = accessRecords.filter(r => r.id !== id);
        saveAccessToStorage();
        renderAccessLogs();
        showToast('紀錄已刪除', false);
      }
    }

    /* CSV Export Utility for Access Logs */
    function exportAccessCSV() {
      if (accessRecords.length === 0) {
        showToast('尚無紀錄可供匯出', false);
        return;
      }

      let csvContent = "\uFEFF類別,狀態,姓名,單位,車牌號碼,人數,事由,體溫備註,進營時間,離營時間\n";
      accessRecords.forEach(r => {
        csvContent += `"${r.category}","${r.status}","${r.name}","${r.unit || ''}","${r.vehicleNo || ''}","${r.passengers}","${r.purpose}","${r.temp || ''}","${r.entryTime || ''}","${r.exitTime || ''}"\n`;
      });

      const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
      const url = URL.createObjectURL(blob);
      const link = document.createElement("a");
      link.setAttribute("href", url);
      link.setAttribute("download", `營區門禁管制紀錄_${new Date().toISOString().substring(0,10)}.csv`);
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
      showToast('已匯出 CSV 報表');
    }

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