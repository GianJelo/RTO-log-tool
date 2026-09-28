<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Market Operations Shift Log & Event Tracker</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            darkMode: 'class',
        }
    </script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" rel="stylesheet">
</head>
<body class="bg-slate-900 text-slate-100 dark:bg-slate-900 dark:text-slate-100 bg-slate-50 text-slate-800 min-h-screen font-sans antialiased flex flex-col justify-between transition-colors duration-200">

    <!-- Header Navbar -->
    <header class="bg-slate-800 dark:bg-slate-800 bg-white border-b border-slate-700 dark:border-slate-700 border-slate-200 sticky top-0 z-30 shadow-sm">
        <div class="max-w-7xl mx-auto px-4 py-4 flex flex-col sm:flex-row justify-between items-center gap-4">
            <div class="flex items-center space-x-3">
                <div class="bg-blue-600 p-2.5 rounded-lg text-white shadow-lg">
                    <i class="fa-solid fa-chart-line text-xl"></i>
                </div>
                <div>
                    <h1 class="text-xl font-bold tracking-tight text-white dark:text-white text-slate-900">Market Operations Center</h1>
                    <p class="text-xs text-slate-400 dark:text-slate-400 text-slate-500">Shift Log, Participant Issues, and Event Tracker</p>
                </div>
            </div>
            <div class="flex items-center gap-3">
                <span id="connection-status" class="inline-flex items-center px-2.5 py-1 rounded-full text-xs font-medium bg-emerald-500/10 text-emerald-400 border border-emerald-500/20">
                    <span class="w-2 h-2 mr-1.5 bg-emerald-400 rounded-full"></span> Connected to Sheets
                </span>
                <button onclick="toggleDarkMode()" class="bg-slate-700 dark:bg-slate-700 bg-slate-100 hover:bg-slate-600 dark:hover:bg-slate-600 hover:bg-slate-200 text-slate-200 dark:text-slate-200 text-slate-700 px-3.5 py-2 rounded-lg text-sm font-medium transition flex items-center gap-2 border border-slate-600 dark:border-slate-600 border-slate-300" title="Toggle Dark/Light Mode">
                    <i id="theme-icon" class="fa-solid fa-moon"></i>
                </button>
                <button onclick="openSettings()" class="bg-slate-700 dark:bg-slate-700 bg-slate-100 hover:bg-slate-600 dark:hover:bg-slate-600 hover:bg-slate-200 text-slate-200 dark:text-slate-200 text-slate-700 px-3.5 py-2 rounded-lg text-sm font-medium transition flex items-center gap-2 border border-slate-600 dark:border-slate-600 border-slate-300">
                    <i class="fa-solid fa-gear"></i> Settings
                </button>
            </div>
        </div>
    </header>

    <!-- Main Container -->
    <main class="max-w-7xl mx-auto px-4 py-8 grid grid-cols-1 lg:grid-cols-3 gap-8 w-full mb-auto">
        
        <!-- Left Column: Turnover Backtracking & Log Entry Form -->
        <section class="lg:col-span-1 space-y-6">
            
            <!-- Previous Shift Turnover Panel -->
            <div class="bg-slate-800 dark:bg-slate-800 bg-white border border-slate-700 dark:border-slate-700 border-slate-200 rounded-xl p-5 shadow-xl">
                <div class="flex items-center justify-between border-b border-slate-700 dark:border-slate-700 border-slate-200 pb-3 mb-3">
                    <h3 class="text-sm font-semibold text-white dark:text-white text-slate-900 flex items-center gap-2">
                        <i class="fa-solid fa-clock-rotate-left text-amber-400"></i> Previous Shift Turnover
                    </h3>
                    <span id="turnover-shift-label" class="text-[10px] bg-slate-900 dark:bg-slate-900 bg-slate-100 text-amber-300 dark:text-amber-300 text-amber-600 px-2 py-0.5 rounded border border-slate-700 dark:border-slate-700 border-slate-300">Loading...</span>
                </div>
                <div id="turnoverContent" class="space-y-2.5 max-h-64 overflow-y-auto pr-1 text-xs">
                    <!-- Populated via JS -->
                </div>
            </div>

            <!-- Log Entry Form -->
            <div class="bg-slate-800 dark:bg-slate-800 bg-white border border-slate-700 dark:border-slate-700 border-slate-200 rounded-xl p-6 shadow-xl">
                <h2 class="text-lg font-semibold text-white dark:text-white text-slate-900 mb-4 flex items-center gap-2 border-b border-slate-700 dark:border-slate-700 border-slate-200 pb-3">
                    <i class="fa-solid fa-pen-to-square text-blue-500"></i> New Shift Log Entry
                </h2>
                
                <form id="shiftLogForm" onsubmit="handleFormSubmit(event)" class="space-y-4">
                    <div>
                        <label class="block text-xs font-semibold uppercase tracking-wider text-slate-400 dark:text-slate-400 text-slate-600 mb-1">Shift Period</label>
                        <select id="shift" onchange="updateTurnoverView()" required class="w-full bg-slate-900 dark:bg-slate-900 bg-slate-50 border border-slate-700 dark:border-slate-700 border-slate-300 rounded-lg p-2.5 text-sm text-slate-200 dark:text-slate-200 text-slate-800 focus:ring-2 focus:ring-blue-500 focus:outline-none">
                            <option value="Morning Shift (06:00 - 14:00)">Morning Shift (06:00 - 14:00)</option>
                            <option value="Evening Shift (14:00 - 22:00)">Evening Shift (14:00 - 22:00)</option>
                            <option value="Night Shift (22:00 - 06:00)">Night Shift (22:00 - 06:00)</option>
                        </select>
                    </div>

                    <div class="grid grid-cols-2 gap-3">
                        <div>
                            <label class="block text-xs font-semibold uppercase tracking-wider text-slate-400 dark:text-slate-400 text-slate-600 mb-1">Operator ID</label>
                            <input type="text" id="operatorId" required placeholder="e.g. OP-4021" class="w-full bg-slate-900 dark:bg-slate-900 bg-slate-50 border border-slate-700 dark:border-slate-700 border-slate-300 rounded-lg p-2.5 text-sm text-slate-200 dark:text-slate-200 text-slate-800 focus:ring-2 focus:ring-blue-500 focus:outline-none">
                        </div>
                        <div>
                            <label class="block text-xs font-semibold uppercase tracking-wider text-slate-400 dark:text-slate-400 text-slate-600 mb-1">Severity</label>
                            <select id="severity" required class="w-full bg-slate-900 dark:bg-slate-900 bg-slate-50 border border-slate-700 dark:border-slate-700 border-slate-300 rounded-lg p-2.5 text-sm text-slate-200 dark:text-slate-200 text-slate-800 focus:ring-2 focus:ring-blue-500 focus:outline-none">
                                <option value="Normal">Normal</option>
                                <option value="Advisory">Advisory</option>
                                <option value="Warning">Warning</option>
                                <option value="Critical">Critical</option>
                            </select>
                        </div>
                    </div>

                    <div class="grid grid-cols-2 gap-3">
                        <div>
                            <label class="block text-xs font-semibold uppercase tracking-wider text-slate-400 dark:text-slate-400 text-slate-600 mb-1">Category</label>
                            <select id="category" required class="w-full bg-slate-900 dark:bg-slate-900 bg-slate-50 border border-slate-700 dark:border-slate-700 border-slate-300 rounded-lg p-2.5 text-sm text-slate-200 dark:text-slate-200 text-slate-800 focus:ring-2 focus:ring-blue-500 focus:outline-none">
                                <option value="System Event">System Event</option>
                                <option value="Participant Concern">Participant Concern</option>
                                <option value="Market Incident">Market Incident</option>
                                <option value="Communication">Communication</option>
                            </select>
                        </div>
                        <div>
                            <label class="block text-xs font-semibold uppercase tracking-wider text-slate-400 dark:text-slate-400 text-slate-600 mb-1">Participant Ref</label>
                            <input type="text" id="participantId" placeholder="e.g. GEN-CO-02" class="w-full bg-slate-900 dark:bg-slate-900 bg-slate-50 border border-slate-700 dark:border-slate-700 border-slate-300 rounded-lg p-2.5 text-sm text-slate-200 dark:text-slate-200 text-slate-800 focus:ring-2 focus:ring-blue-500 focus:outline-none">
                        </div>
                    </div>

                    <div>
                        <label class="block text-xs font-semibold uppercase tracking-wider text-slate-400 dark:text-slate-400 text-slate-600 mb-1">Description of Event / Concern</label>
                        <textarea id="description" rows="4" required placeholder="Detailed operational summary, participant inquiries, or system remarks..." class="w-full bg-slate-900 dark:bg-slate-900 bg-slate-50 border border-slate-700 dark:border-slate-700 border-slate-300 rounded-lg p-2.5 text-sm text-slate-200 dark:text-slate-200 text-slate-800 focus:ring-2 focus:ring-blue-500 focus:outline-none"></textarea>
                    </div>

                    <button type="submit" class="w-full bg-blue-600 hover:bg-blue-500 text-white font-medium py-2.5 rounded-lg transition shadow-lg shadow-blue-600/20 flex items-center justify-center gap-2">
                        <i class="fa-solid fa-cloud-arrow-up"></i> Submit Log Entry
                    </button>
                </form>
            </div>
        </section>

        <!-- Right Column: Logs Viewer & Dashboard -->
        <section class="lg:col-span-2 space-y-6 flex flex-col">
            
            <!-- Quick Filter & Actions Bar -->
            <div class="bg-slate-800 dark:bg-slate-800 bg-white border border-slate-700 dark:border-slate-700 border-slate-200 rounded-xl p-4 shadow-xl flex flex-col sm:flex-row justify-between items-center gap-4">
                <div class="w-full sm:w-72 relative">
                    <span class="absolute inset-y-0 left-0 flex items-center pl-3 pointer-events-none text-slate-400">
                        <i class="fa-solid fa-magnifying-glass"></i>
                    </span>
                    <input type="text" id="searchInput" onkeyup="filterLogs()" placeholder="Search operational logs..." class="w-full bg-slate-900 dark:bg-slate-900 bg-slate-50 border border-slate-700 dark:border-slate-700 border-slate-300 rounded-lg pl-9 pr-4 py-2 text-sm text-slate-200 dark:text-slate-200 text-slate-800 focus:ring-2 focus:ring-blue-500 focus:outline-none">
                </div>
                
                <div class="flex items-center gap-2 w-full sm:w-auto justify-end">
                    <button onclick="syncPendingLogs()" class="bg-emerald-600/20 hover:bg-emerald-600/30 text-emerald-400 border border-emerald-500/30 px-3 py-2 rounded-lg text-sm font-medium transition flex items-center gap-2">
                        <i class="fa-solid fa-rotate"></i> Sync Cloud
                    </button>
                    <button onclick="exportCSV()" class="bg-slate-700 dark:bg-slate-700 bg-slate-100 hover:bg-slate-600 dark:hover:bg-slate-600 hover:bg-slate-200 text-slate-200 dark:text-slate-200 text-slate-700 border border-slate-600 dark:border-slate-600 border-slate-300 px-3 py-2 rounded-lg text-sm font-medium transition flex items-center gap-2">
                        <i class="fa-solid fa-download"></i> Export CSV
                    </button>
                </div>
            </div>

            <!-- Log Entries Display Table -->
            <div class="bg-slate-800 dark:bg-slate-800 bg-white border border-slate-700 dark:border-slate-700 border-slate-200 rounded-xl shadow-xl overflow-hidden flex-1 flex flex-col">
                <div class="px-6 py-4 border-b border-slate-700 dark:border-slate-700 border-slate-200 flex justify-between items-center">
                    <h2 class="font-semibold text-white dark:text-white text-slate-900 flex items-center gap-2">
                        <i class="fa-solid fa-list-ul text-blue-500"></i> Recorded Shift Logs
                    </h2>
                    <span id="log-count" class="text-xs bg-slate-700 dark:bg-slate-700 bg-slate-100 text-slate-300 dark:text-slate-300 text-slate-700 px-2.5 py-1 rounded-full font-mono">0 entries</span>
                </div>
                
                <div class="overflow-x-auto flex-1 max-h-[600px]">
                    <table class="w-full text-left border-collapse text-sm">
                        <thead class="bg-slate-900 dark:bg-slate-900 bg-slate-100 text-slate-400 dark:text-slate-400 text-slate-600 uppercase text-xs sticky top-0 z-10 border-b border-slate-700 dark:border-slate-700 border-slate-200">
                            <tr>
                                <th class="p-3.5">Timestamp / Shift</th>
                                <th class="p-3.5">Operator</th>
                                <th class="p-3.5">Category / Severity</th>
                                <th class="p-3.5">Participant</th>
                                <th class="p-3.5">Description</th>
                                <th class="p-3.5 text-center">Status</th>
                            </tr>
                        </thead>
                        <tbody id="logsTableBody" class="divide-y divide-slate-700/50 dark:divide-slate-700/50 divide-slate-200">
                            <!-- Populated dynamically via JS -->
                        </tbody>
                    </table>
                </div>
            </div>
        </section>
    </main>

    <!-- Footer -->
    <footer class="bg-slate-800 dark:bg-slate-800 bg-white border-t border-slate-700 dark:border-slate-700 border-slate-200 py-4 text-center text-xs text-slate-400 dark:text-slate-400 text-slate-500 flex flex-col sm:flex-row justify-center items-center gap-2">
        <span>Market Operations Shift Logging System &bull; Cloud Integrated via Google Sheets</span>
        <span class="hidden sm:inline">&bull;</span>
        <span class="font-semibold text-blue-400">Developed by Group E</span>
    </footer>

    <!-- Settings Modal -->
    <div id="settingsModal" class="fixed inset-0 bg-black/70 backdrop-blur-sm hidden z-50 flex items-center justify-center p-4">
        <div class="bg-slate-800 dark:bg-slate-800 bg-white border border-slate-700 dark:border-slate-700 border-slate-200 rounded-2xl w-full max-w-md p-6 shadow-2xl space-y-4">
            <div class="flex justify-between items-center border-b border-slate-700 dark:border-slate-700 border-slate-200 pb-3">
                <h3 class="text-lg font-semibold text-white dark:text-white text-slate-900 flex items-center gap-2">
                    <i class="fa-solid fa-link text-blue-500"></i> Google Apps Script Configuration
                </h3>
                <button onclick="closeSettings()" class="text-slate-400 hover:text-slate-200">
                    <i class="fa-solid fa-xmark text-lg"></i>
                </button>
            </div>
            
            <div>
                <label class="block text-xs font-semibold uppercase tracking-wider text-slate-400 dark:text-slate-400 text-slate-600 mb-1">Web App Deployment URL</label>
                <input type="text" id="scriptUrlInput" placeholder="https://script.google.com/macros/s/.../exec" class="w-full bg-slate-900 dark:bg-slate-900 bg-slate-50 border border-slate-700 dark:border-slate-700 border-slate-300 rounded-lg p-2.5 text-sm text-slate-200 dark:text-slate-200 text-slate-800 focus:ring-2 focus:ring-blue-500 focus:outline-none font-mono text-xs">
                <p class="text-xs text-slate-400 dark:text-slate-400 text-slate-500 mt-1">Paste the execution URL obtained after publishing your Google Apps Script backend.</p>
            </div>

            <div class="flex justify-end gap-3 pt-2">
                <button onclick="closeSettings()" class="bg-slate-700 dark:bg-slate-700 bg-slate-200 hover:bg-slate-600 dark:hover:bg-slate-600 hover:bg-slate-300 text-slate-300 dark:text-slate-300 text-slate-700 px-4 py-2 rounded-lg text-sm font-medium transition">Cancel</button>
                <button onclick="saveSettings()" class="bg-blue-600 hover:bg-blue-500 text-white px-4 py-2 rounded-lg text-sm font-medium transition shadow-lg shadow-blue-600/20">Save Configuration</button>
            </div>
        </div>
    </div>

    <!-- Application Script Logic -->
    <script>
        let logs = JSON.parse(localStorage.getItem('market_shift_logs') || '[]');
        let scriptUrl = localStorage.getItem('market_script_url') || 'https://script.google.com/macros/s/AKfycbxNxNJ6PXkc4BIAubJhSMlrYIj_MRSFGcGCpk1aWP5wfvOwJANS0lDA41pDa-fReIBW/exec';

        const shiftSequence = [
            "Morning Shift (06:00 - 14:00)",
            "Evening Shift (14:00 - 22:00)",
            "Night Shift (22:00 - 06:00)"
        ];

        document.addEventListener('DOMContentLoaded', () => {
            initTheme();
            updateConnectionStatus();
            renderLogs();
            updateTurnoverView();
        });

        function initTheme() {
            const savedTheme = localStorage.getItem('market_theme') || 'dark';
            if (savedTheme === 'light') {
                document.documentElement.classList.remove('dark');
                document.getElementById('theme-icon').className = "fa-solid fa-sun";
            } else {
                document.documentElement.classList.add('dark');
                document.getElementById('theme-icon').className = "fa-solid fa-moon";
            }
        }

        function toggleDarkMode() {
            const isDark = document.documentElement.classList.toggle('dark');
            const theme = isDark ? 'dark' : 'light';
            localStorage.setItem('market_theme', theme);
            document.getElementById('theme-icon').className = isDark ? "fa-solid fa-moon" : "fa-solid fa-sun";
        }

        function openSettings() {
            document.getElementById('scriptUrlInput').value = scriptUrl;
            document.getElementById('settingsModal').classList.remove('hidden');
        }

        function closeSettings() {
            document.getElementById('settingsModal').classList.add('hidden');
        }

        function saveSettings() {
            scriptUrl = document.getElementById('scriptUrlInput').value.trim();
            localStorage.setItem('market_script_url', scriptUrl);
            updateConnectionStatus();
            closeSettings();
            alert('Configuration saved successfully.');
        }

        function updateConnectionStatus() {
            const badge = document.getElementById('connection-status');
            if (scriptUrl) {
                badge.className = "inline-flex items-center px-2.5 py-1 rounded-full text-xs font-medium bg-emerald-500/10 text-emerald-400 border border-emerald-500/20";
                badge.innerHTML = `<span class="w-2 h-2 mr-1.5 bg-emerald-400 rounded-full"></span> Connected to Sheets`;
            } else {
                badge.className = "inline-flex items-center px-2.5 py-1 rounded-full text-xs font-medium bg-amber-500/10 text-amber-400 border border-amber-500/20";
                badge.innerHTML = `<span class="w-2 h-2 mr-1.5 bg-amber-400 rounded-full animate-pulse"></span> Config Required`;
            }
        }

        function updateTurnoverView() {
            const currentShift = document.getElementById('shift').value;
            const currentIndex = shiftSequence.indexOf(currentShift);
            const prevIndex = (currentIndex - 1 + shiftSequence.length) % shiftSequence.length;
            const previousShiftName = shiftSequence[prevIndex];

            document.getElementById('turnover-shift-label').innerText = previousShiftName.split(' ')[0] + ' Shift Turnover';

            const turnoverContainer = document.getElementById('turnoverContent');
            turnoverContainer.innerHTML = '';

            const turnoverLogs = logs.filter(l => l.shift === previousShiftName);

            if (turnoverLogs.length === 0) {
                turnoverContainer.innerHTML = `<div class="text-slate-500 italic text-center py-4">No records found for the preceding shift.</div>`;
                return;
            }

            turnoverLogs.forEach(log => {
                let sevColor = 'text-slate-500 dark:text-slate-400';
                if (log.severity === 'Advisory') sevColor = 'text-blue-500 dark:text-blue-400 font-bold';
                if (log.severity === 'Warning') sevColor = 'text-amber-500 dark:text-amber-400 font-bold';
                if (log.severity === 'Critical') sevColor = 'text-rose-500 dark:text-rose-400 font-bold';

                const card = document.createElement('div');
                card.className = "bg-slate-900/40 dark:bg-slate-900 bg-slate-50 border border-slate-700/70 dark:border-slate-700/70 border-slate-200 p-2.5 rounded-lg space-y-1";
                card.innerHTML = `
                    <div class="flex justify-between items-center text-[10px] text-slate-500 dark:text-slate-400">
                        <span class="font-mono text-blue-500 dark:text-blue-400 font-semibold">${log.operatorId}</span>
                        <span class="${sevColor}">${log.severity} - ${log.category}</span>
                    </div>
                    <p class="text-slate-800 dark:text-slate-200 leading-snug">${escapeHtml(log.description)}</p>
                    <div class="text-[10px] text-slate-500 dark:text-slate-500 text-right">${log.timestamp}</div>
                `;
                turnoverContainer.appendChild(card);
            });
        }

        async function handleFormSubmit(event) {
            event.preventDefault();
            
            const newLog = {
                id: 'LOG-' + Date.now().toString().slice(-6),
                timestamp: new Date().toLocaleString(),
                shift: document.getElementById('shift').value,
                operatorId: document.getElementById('operatorId').value.trim(),
                category: document.getElementById('category').value,
                severity: document.getElementById('severity').value,
                participantId: document.getElementById('participantId').value.trim() || 'N/A',
                description: document.getElementById('description').value.trim(),
                synced: false
            };

            logs.unshift(newLog);
            saveToLocalStorage();
            renderLogs();
            updateTurnoverView();
            event.target.reset();

            if (scriptUrl) {
                await pushLogToCloud(newLog);
            } else {
                alert('Log saved locally. Configure your Google Apps Script URL in settings to auto-sync to Google Sheets.');
            }
        }

        async function pushLogToCloud(logEntry) {
            try {
                await fetch(scriptUrl, {
                    method: 'POST',
                    mode: 'no-cors',
                    headers: { 'Content-Type': 'text/plain;charset=utf-8' },
                    body: JSON.stringify(logEntry)
                });
                
                logEntry.synced = true;
                saveToLocalStorage();
                renderLogs();
            } catch (error) {
                console.error('Cloud sync failed:', error);
            }
        }

        async function syncPendingLogs() {
            if (!scriptUrl) {
                alert('Please configure your Google Apps Script URL first via Settings.');
                openSettings();
                return;
            }

            const pending = logs.filter(l => !l.synced);
            if (pending.length === 0) {
                alert('All logs are already synchronized with Google Sheets.');
                return;
            }

            let successCount = 0;
            for (let log of pending) {
                try {
                    await fetch(scriptUrl, {
                        method: 'POST',
                        mode: 'no-cors',
                        headers: { 'Content-Type': 'text/plain;charset=utf-8' },
                        body: JSON.stringify(log)
                    });
                    log.synced = true;
                    successCount++;
                } catch (e) {
                    console.error('Failed to sync item', log.id);
                }
            }

            saveToLocalStorage();
            renderLogs();
            alert(`Sync complete. Successfully processed ${successCount} entries.`);
        }

        function saveToLocalStorage() {
            localStorage.setItem('market_shift_logs', JSON.stringify(logs));
        }

        function renderLogs(filterText = '') {
            const tbody = document.getElementById('logsTableBody');
            tbody.innerHTML = '';

            const filtered = logs.filter(l => {
                const query = filterText.toLowerCase();
                return l.operatorId.toLowerCase().includes(query) ||
                       l.description.toLowerCase().includes(query) ||
                       l.category.toLowerCase().includes(query) ||
                       l.participantId.toLowerCase().includes(query) ||
                       l.shift.toLowerCase().includes(query);
            });

            document.getElementById('log-count').innerText = `${filtered.length} entries`;

            if (filtered.length === 0) {
                tbody.innerHTML = `<tr><td colspan="6" class="text-center py-8 text-slate-500 italic">No shift logs found.</td></tr>`;
                return;
            }

            filtered.forEach(log => {
                let badgeColor = 'bg-slate-700 text-slate-300 border-slate-600';
                if (log.severity === 'Advisory') badgeColor = 'bg-blue-500/10 text-blue-500 dark:text-blue-400 border-blue-500/20';
                if (log.severity === 'Warning') badgeColor = 'bg-amber-500/10 text-amber-500 dark:text-amber-400 border-amber-500/20';
                if (log.severity === 'Critical') badgeColor = 'bg-rose-500/10 text-rose-500 dark:text-rose-400 border-rose-500/20';

                const tr = document.createElement('tr');
                tr.className = "hover:bg-slate-700/20 dark:hover:bg-slate-700/30 transition text-slate-700 dark:text-slate-300";
                tr.innerHTML = `
                    <td class="p-3.5">
                        <div class="font-medium text-slate-900 dark:text-white">${log.timestamp}</div>
                        <div class="text-xs text-slate-500 dark:text-slate-400">${log.shift}</div>
                    </td>
                    <td class="p-3.5 font-mono text-xs text-blue-600 dark:text-blue-400 font-semibold">${log.operatorId}</td>
                    <td class="p-3.5">
                        <div class="text-xs font-semibold text-slate-800 dark:text-slate-200">${log.category}</div>
                        <span class="inline-block mt-1 px-2 py-0.5 rounded text-[10px] font-bold border ${badgeColor}">${log.severity}</span>
                    </td>
                    <td class="p-3.5 font-mono text-xs">${log.participantId}</td>
                    <td class="p-3.5 text-slate-700 dark:text-slate-200 max-w-xs truncate" title="${escapeHtml(log.description)}">${escapeHtml(log.description)}</td>
                    <td class="p-3.5 text-center">
                        ${log.synced 
                            ? '<span class="text-emerald-600 dark:text-emerald-400 text-xs flex items-center justify-center gap-1"><i class="fa-solid fa-cloud-check"></i> Synced</span>' 
                            : '<span class="text-amber-600 dark:text-amber-400 text-xs flex items-center justify-center gap-1"><i class="fa-solid fa-clock"></i> Pending</span>'}
                    </td>
                `;
                tbody.appendChild(tr);
            });
        }

        function filterLogs() {
            const query = document.getElementById('searchInput').value;
            renderLogs(query);
        }

        function escapeHtml(text) {
            return text.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;").replace(/"/g, "&quot;").replace(/'/g, "&#039;");
        }

        function exportCSV() {
            if (logs.length === 0) {
                alert('No data available to export.');
                return;
            }
            let csv = 'Timestamp,Shift,OperatorID,Category,Severity,ParticipantID,Description\n';
            logs.forEach(l => {
                csv += `"${l.timestamp}","${l.shift}","${l.operatorId}","${l.category}","${l.severity}","${l.participantId}","${l.description.replace(/"/g, '""')}"\n`;
            });
            const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.setAttribute('href', url);
            a.setAttribute('download', `market_shift_logs_${new Date().toISOString().slice(0,10)}.csv`);
            document.body.appendChild(a);
            a.click();
            document.body.removeChild(a);
        }
    </script>
</body>
</html>
