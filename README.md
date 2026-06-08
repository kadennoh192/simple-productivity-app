<!DOCTYPE html>
<html lang="en" class="light">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="theme-color" content="#0f172a">
    <title>Omni Productivity</title>
    
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: { darkbg: '#0f172a', darkcard: '#1e293b' },
                    fontFamily: { sans: ['Inter', 'sans-serif'] },
                    animation: {
                        'fade-in': 'fadeIn 0.3s ease-out forwards',
                        'slide-up': 'slideUp 0.4s ease-out forwards',
                        'scale-in': 'scaleIn 0.2s ease-out forwards'
                    },
                    keyframes: {
                        fadeIn: { '0%': { opacity: '0' }, '100%': { opacity: '1' } },
                        slideUp: { '0%': { opacity: '0', transform: 'translateY(15px)' }, '100%': { opacity: '1', transform: 'translateY(0)' } },
                        scaleIn: { '0%': { opacity: '0', transform: 'scale(0.95)' }, '100%': { opacity: '1', transform: 'scale(1)' } }
                    }
                }
            }
        }
    </script>
    
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">

    <style>
        body { font-family: 'Inter', sans-serif; -webkit-tap-highlight-color: transparent; }
        .hide-scrollbar::-webkit-scrollbar { display: none; }
        .hide-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
        
        /* Custom Checkboxes */
        .task-checkbox { appearance: none; width: 1.25rem; height: 1.25rem; border: 2px solid #cbd5e1; border-radius: 50%; cursor: pointer; position: relative; transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1); }
        .dark .task-checkbox { border-color: #475569; }
        .task-checkbox:checked { background-color: var(--theme-color, #6366f1); border-color: var(--theme-color, #6366f1); transform: scale(1.1); }
        .task-checkbox:checked::after { content: ''; position: absolute; left: 5px; top: 2px; width: 5px; height: 10px; border: solid white; border-width: 0 2px 2px 0; transform: rotate(45deg); }
        
        .subtask-checkbox { width: 1rem; height: 1rem; border-radius: 4px; }
        
        /* Priorities & Themes */
        .p1 { color: #ef4444; } .p2 { color: #f59e0b; } .p3 { color: #3b82f6; } .p4 { color: #9ca3af; }
        .theme-text { color: var(--theme-color, #6366f1) !important; }
        .theme-bg { background-color: var(--theme-color, #6366f1) !important; }
        .theme-border { border-color: var(--theme-color, #6366f1) !important; }
        .theme-ring { --tw-ring-color: var(--theme-color, #6366f1) !important; }

        /* Timeline */
        .timeline-container { position: relative; padding-left: 2rem; border-left: 2px solid #e2e8f0; }
        .dark .timeline-container { border-color: #334155; }
        .timeline-dot { position: absolute; left: -0.4rem; top: 1.5rem; width: 0.75rem; height: 0.75rem; border-radius: 50%; background: var(--theme-color, #6366f1); border: 2px solid white; box-shadow: 0 0 0 2px var(--theme-color, #6366f1); }
        .dark .timeline-dot { border-color: #0f172a; }

        /* Animations */
        .stagger-1 { animation-delay: 50ms; }
        .stagger-2 { animation-delay: 100ms; }
        .stagger-3 { animation-delay: 150ms; }
        .stagger-4 { animation-delay: 200ms; }
        .stagger-5 { animation-delay: 250ms; }
        
        .glass-panel { background: rgba(255, 255, 255, 0.85); backdrop-filter: blur(12px); -webkit-backdrop-filter: blur(12px); }
        .dark .glass-panel { background: rgba(30, 41, 59, 0.85); }
    </style>
</head>
<body class="bg-gray-50 text-gray-900 dark:bg-darkbg dark:text-gray-100 transition-colors duration-300 overflow-hidden h-screen flex flex-col md:flex-row">

    <!-- Desktop Sidebar -->
    <aside class="hidden md:flex flex-col w-64 glass-panel border-r border-gray-200 dark:border-gray-800 h-full shadow-sm z-20 shrink-0 transition-colors duration-300">
        <div class="p-5 flex items-center justify-between border-b border-gray-200 dark:border-gray-800">
            <h1 class="text-xl font-bold flex items-center gap-2 tracking-tight"><i class="fa-solid fa-layer-group theme-text"></i> Omni</h1>
            <button onclick="toggleTheme()" class="text-gray-400 hover:text-gray-800 dark:hover:text-white transition-colors"><i class="fa-solid fa-moon dark:fa-sun"></i></button>
        </div>
        <nav class="flex-1 overflow-y-auto py-4 space-y-1 px-3 hide-scrollbar" id="desktop-nav">
            <!-- Nav Injected by JS -->
        </nav>
        <div class="p-4 border-t border-gray-200 dark:border-gray-800">
            <button onclick="openSettings()" class="w-full flex items-center text-gray-600 dark:text-gray-400 hover:bg-gray-100 dark:hover:bg-gray-800 px-3 py-2.5 rounded-xl transition-all duration-200 hover:pl-4 font-medium">
                <i class="fa-solid fa-gear w-6"></i> Settings
            </button>
        </div>
    </aside>

    <!-- Main Content Area -->
    <main class="flex-1 flex flex-col h-full relative overflow-hidden bg-cover bg-center transition-all duration-500" id="main-content">
        <div class="absolute inset-0 bg-gray-50 dark:bg-darkbg transition-opacity duration-300 z-0" id="main-overlay"></div>
        
        <!-- Header Mobile -->
        <header class="flex md:hidden items-center justify-between p-4 glass-panel border-b border-gray-200 dark:border-gray-800 z-10 relative">
            <h2 id="mobile-header-title" class="text-lg font-bold tracking-tight">Inbox</h2>
            <div class="flex gap-4">
                <button onclick="toggleTheme()" class="text-gray-500 dark:text-gray-400"><i class="fa-solid fa-moon dark:fa-sun"></i></button>
                <button onclick="openSettings()" class="text-gray-500 dark:text-gray-400"><i class="fa-solid fa-gear"></i></button>
            </div>
        </header>

        <!-- Dynamic View Container -->
        <div id="view-container" class="flex-1 overflow-y-auto p-4 md:p-8 pb-24 md:pb-8 hide-scrollbar z-10 relative">
            <!-- Content Injected via JS -->
        </div>

        <!-- Global Quick Add FAB -->
        <button onclick="openTaskModal()" class="fixed right-6 bottom-20 md:bottom-8 w-14 h-14 theme-bg text-white rounded-full shadow-[0_8px_30px_rgb(0,0,0,0.12)] flex items-center justify-center text-2xl hover:scale-110 transition-transform duration-300 z-40 active:scale-95">
            <i class="fa-solid fa-plus"></i>
        </button>
    </main>

    <!-- Mobile Bottom Nav -->
    <nav class="md:hidden fixed bottom-0 w-full glass-panel border-t border-gray-200 dark:border-gray-800 pb-safe z-30 shadow-[0_-10px_40px_rgba(0,0,0,0.05)]">
        <div class="flex justify-around items-center h-16 overflow-x-auto hide-scrollbar px-2" id="mobile-nav">
            <!-- Mobile Nav Injected by JS -->
        </div>
    </nav>

    <!-- Modals & Overlays -->
    
    <!-- Task Edit/Add Modal -->
    <div id="task-modal" class="fixed inset-0 bg-black/40 backdrop-blur-sm z-50 hidden flex-col items-center justify-end md:justify-center p-0 md:p-4 opacity-0 transition-opacity duration-300">
        <div class="bg-white dark:bg-darkcard w-full md:max-w-2xl rounded-t-3xl md:rounded-2xl shadow-2xl overflow-hidden flex flex-col max-h-[90vh] transform translate-y-full md:translate-y-8 transition-transform duration-300" id="task-modal-content">
            <div class="p-5 border-b border-gray-100 dark:border-gray-800 flex justify-between items-center shrink-0">
                <h3 class="font-bold text-lg text-gray-800 dark:text-gray-100 tracking-tight" id="task-modal-title">New Task</h3>
                <button onclick="closeTaskModal()" class="text-gray-400 hover:text-gray-600 bg-gray-100 dark:bg-gray-800 w-8 h-8 rounded-full flex items-center justify-center transition-colors"><i class="fa-solid fa-times"></i></button>
            </div>
            <div class="p-5 overflow-y-auto flex-1 space-y-5 hide-scrollbar">
                <input type="hidden" id="tm-id">
                <input type="text" id="tm-title" placeholder="What needs to be done?" class="w-full bg-transparent border-none p-0 text-2xl font-bold focus:ring-0 dark:text-white outline-none placeholder-gray-300 dark:placeholder-gray-600 transition-colors">
                
                <div class="grid grid-cols-2 gap-4">
                    <div class="bg-gray-50 dark:bg-gray-800/50 p-3 rounded-xl border border-gray-100 dark:border-gray-700/50">
                        <label class="text-[10px] font-bold uppercase tracking-wider text-gray-400 mb-1 block">Due Date & Time</label>
                        <input type="datetime-local" id="tm-due" class="w-full bg-transparent text-sm dark:text-white outline-none font-medium">
                    </div>
                    <div class="bg-gray-50 dark:bg-gray-800/50 p-3 rounded-xl border border-gray-100 dark:border-gray-700/50">
                        <label class="text-[10px] font-bold uppercase tracking-wider text-gray-400 mb-1 block">List</label>
                        <select id="tm-list" class="w-full bg-transparent text-sm dark:text-white outline-none font-medium cursor-pointer"></select>
                    </div>
                    <div class="bg-gray-50 dark:bg-gray-800/50 p-3 rounded-xl border border-gray-100 dark:border-gray-700/50">
                        <label class="text-[10px] font-bold uppercase tracking-wider text-gray-400 mb-1 block">Priority</label>
                        <select id="tm-priority" class="w-full bg-transparent text-sm dark:text-white outline-none font-medium cursor-pointer">
                            <option value="4">None</option>
                            <option value="3">Low</option>
                            <option value="2">Medium</option>
                            <option value="1">High 🔴</option>
                        </select>
                    </div>
                    <div class="bg-gray-50 dark:bg-gray-800/50 p-3 rounded-xl border border-gray-100 dark:border-gray-700/50">
                        <label class="text-[10px] font-bold uppercase tracking-wider text-gray-400 mb-1 block">Repeat</label>
                        <select id="tm-repeat" class="w-full bg-transparent text-sm dark:text-white outline-none font-medium cursor-pointer">
                            <option value="none">Does not repeat</option>
                            <option value="daily">Daily</option>
                            <option value="weekly">Weekly</option>
                            <option value="monthly">Monthly</option>
                        </select>
                    </div>
                </div>
                
                <!-- Subtasks -->
                <div>
                    <label class="text-xs font-bold text-gray-500 mb-2 block">Subtasks</label>
                    <div id="tm-subtasks-list" class="space-y-2 mb-2"></div>
                    <div class="flex gap-2">
                        <input type="text" id="tm-new-subtask" placeholder="Add a subtask..." class="flex-1 bg-gray-50 dark:bg-gray-800 rounded-lg p-2.5 text-sm dark:text-white outline-none focus:ring-2 theme-ring transition-shadow" onkeypress="if(event.key === 'Enter') addSubtask()">
                        <button onclick="addSubtask()" class="px-4 bg-gray-100 dark:bg-gray-800 text-gray-600 dark:text-gray-300 rounded-lg hover:bg-gray-200 transition-colors"><i class="fa-solid fa-plus"></i></button>
                    </div>
                </div>

                <textarea id="tm-desc" placeholder="Add notes, descriptions, or links..." class="w-full bg-gray-50 dark:bg-gray-800 rounded-xl p-4 h-28 focus:ring-2 theme-ring dark:text-white outline-none resize-none transition-shadow text-sm"></textarea>
                
                <!-- File Upload -->
                <div>
                    <div class="flex items-center justify-between mb-2">
                        <label class="text-xs font-bold text-gray-500">Attachments</label>
                        <label class="cursor-pointer text-xs theme-text hover:opacity-80 transition-opacity font-medium">
                            <i class="fa-solid fa-paperclip"></i> Add File
                            <input type="file" id="tm-file-input" class="hidden" onchange="handleFileUpload(event)">
                        </label>
                    </div>
                    <span id="tm-file-status" class="text-xs text-gray-400 block mb-2"></span>
                    <div id="tm-attachments" class="flex flex-wrap gap-2"></div>
                </div>
            </div>
            <div class="p-5 border-t border-gray-100 dark:border-gray-800 flex justify-end gap-3 shrink-0 bg-gray-50 dark:bg-gray-900/50">
                <button onclick="closeTaskModal()" class="px-6 py-2.5 text-gray-600 dark:text-gray-300 font-medium hover:bg-gray-200 dark:hover:bg-gray-800 rounded-xl transition-colors">Cancel</button>
                <button onclick="saveTask()" class="px-8 py-2.5 theme-bg text-white font-bold rounded-xl shadow-lg hover:shadow-xl hover:opacity-90 transition-all transform active:scale-95">Save Task</button>
            </div>
        </div>
    </div>

    <!-- Habit Create/Edit Modal -->
    <div id="habit-modal" class="fixed inset-0 bg-black/40 backdrop-blur-sm z-[55] hidden items-center justify-center p-4 opacity-0 transition-opacity duration-300">
        <div class="bg-white dark:bg-darkcard w-full max-w-sm rounded-2xl shadow-2xl p-6 transform scale-95 transition-transform duration-300" id="habit-modal-content">
            <h3 class="font-bold text-xl mb-4 dark:text-white">New Habit</h3>
            <input type="text" id="hm-title" placeholder="Habit Name (e.g. Read 10 Pages)" class="w-full bg-gray-50 dark:bg-gray-800 rounded-xl p-3 mb-4 outline-none focus:ring-2 theme-ring dark:text-white">
            <div class="mb-4">
                <label class="text-xs text-gray-500 font-bold mb-2 block">Color</label>
                <div class="flex gap-2">
                    <button onclick="selectHabitColor('text-blue-500')" class="w-8 h-8 rounded-full bg-blue-500 ring-2 ring-offset-2 ring-transparent focus:ring-blue-500 habit-color-btn"></button>
                    <button onclick="selectHabitColor('text-green-500')" class="w-8 h-8 rounded-full bg-green-500 ring-2 ring-offset-2 ring-transparent focus:ring-green-500 habit-color-btn"></button>
                    <button onclick="selectHabitColor('text-purple-500')" class="w-8 h-8 rounded-full bg-purple-500 ring-2 ring-offset-2 ring-transparent focus:ring-purple-500 habit-color-btn"></button>
                    <button onclick="selectHabitColor('text-red-500')" class="w-8 h-8 rounded-full bg-red-500 ring-2 ring-offset-2 ring-transparent focus:ring-red-500 habit-color-btn"></button>
                    <button onclick="selectHabitColor('text-yellow-500')" class="w-8 h-8 rounded-full bg-yellow-500 ring-2 ring-offset-2 ring-transparent focus:ring-yellow-500 habit-color-btn"></button>
                </div>
            </div>
            <div class="flex justify-end gap-2 mt-6">
                <button onclick="closeHabitModal()" class="px-4 py-2 text-gray-500 hover:bg-gray-100 dark:hover:bg-gray-800 rounded-lg">Cancel</button>
                <button onclick="saveHabit()" class="px-4 py-2 theme-bg text-white rounded-lg font-medium">Create Habit</button>
            </div>
        </div>
    </div>

    <!-- Settings Modal -->
    <div id="settings-modal" class="fixed inset-0 bg-black/40 backdrop-blur-sm z-50 hidden items-center justify-center p-4 opacity-0 transition-opacity duration-300">
        <div class="bg-white dark:bg-darkcard w-full max-w-lg rounded-2xl shadow-2xl overflow-hidden flex flex-col max-h-[85vh] transform scale-95 transition-transform duration-300" id="settings-modal-content">
            <div class="p-5 border-b border-gray-100 dark:border-gray-800 flex justify-between items-center shrink-0">
                <h3 class="font-bold text-xl dark:text-white tracking-tight"><i class="fa-solid fa-gear text-gray-400 mr-2"></i>Settings</h3>
                <button onclick="closeSettings()" class="text-gray-400 hover:text-gray-600 bg-gray-100 dark:bg-gray-800 w-8 h-8 rounded-full flex items-center justify-center transition-colors"><i class="fa-solid fa-times"></i></button>
            </div>
            <div class="p-6 overflow-y-auto space-y-8 flex-1 hide-scrollbar">
                
                <!-- Appearance -->
                <div class="bg-gray-50 dark:bg-gray-800/30 p-4 rounded-xl">
                    <h4 class="font-bold text-gray-800 dark:text-white mb-4 flex items-center gap-2"><i class="fa-solid fa-palette text-indigo-400"></i> Appearance</h4>
                    <div class="flex items-center justify-between mb-5">
                        <span class="text-sm font-medium text-gray-600 dark:text-gray-300">Dark Mode</span>
                        <button onclick="toggleTheme(); renderSettings();" class="text-2xl text-gray-500 dark:text-yellow-400 transition-transform active:scale-90"><i class="fa-solid fa-moon dark:fa-sun"></i></button>
                    </div>
                    <span class="text-sm font-medium text-gray-600 dark:text-gray-300 block mb-3">Accent Color</span>
                    <div class="flex gap-4 mb-5">
                        <button onclick="setAccent('#6366f1')" class="w-8 h-8 rounded-full bg-[#6366f1] ring-2 ring-offset-2 ring-transparent focus:ring-[#6366f1] transition-transform hover:scale-110"></button>
                        <button onclick="setAccent('#10b981')" class="w-8 h-8 rounded-full bg-[#10b981] ring-2 ring-offset-2 ring-transparent focus:ring-[#10b981] transition-transform hover:scale-110"></button>
                        <button onclick="setAccent('#f43f5e')" class="w-8 h-8 rounded-full bg-[#f43f5e] ring-2 ring-offset-2 ring-transparent focus:ring-[#f43f5e] transition-transform hover:scale-110"></button>
                        <button onclick="setAccent('#8b5cf6')" class="w-8 h-8 rounded-full bg-[#8b5cf6] ring-2 ring-offset-2 ring-transparent focus:ring-[#8b5cf6] transition-transform hover:scale-110"></button>
                    </div>
                    <div>
                        <span class="text-sm font-medium text-gray-600 dark:text-gray-300 block mb-2">App Background Image URL</span>
                        <input type="text" id="bg-url-input" placeholder="https://..." class="w-full bg-white dark:bg-gray-800 rounded-lg p-2.5 text-sm outline-none border border-gray-200 dark:border-gray-700 dark:text-white focus:ring-2 theme-ring" onchange="setBackground(this.value)">
                    </div>
                    <div class="mt-4">
                        <span class="text-sm font-medium text-gray-600 dark:text-gray-300 flex justify-between">Background Overlay Opacity <span id="opacity-val">90%</span></span>
                        <input type="range" id="bg-opacity" min="0" max="1" step="0.05" class="w-full mt-2 accent-indigo-500" oninput="updateOpacity(this.value)">
                        <p class="text-[10px] text-gray-400 mt-1">Increase to make text more readable over custom backgrounds.</p>
                    </div>
                </div>

                <!-- Custom Lists Management -->
                <div class="bg-gray-50 dark:bg-gray-800/30 p-4 rounded-xl">
                    <h4 class="font-bold text-gray-800 dark:text-white mb-4 flex items-center gap-2"><i class="fa-solid fa-list-check text-green-400"></i> Manage Lists</h4>
                    <div id="settings-lists" class="space-y-2 mb-4"></div>
                    <div class="flex gap-2">
                        <input type="text" id="new-list-name" placeholder="Create a new list..." class="flex-1 bg-white dark:bg-gray-800 border border-gray-200 dark:border-gray-700 rounded-lg p-2.5 text-sm outline-none dark:text-white focus:ring-2 theme-ring">
                        <button onclick="addCustomList()" class="px-4 py-2 bg-green-500 text-white rounded-lg text-sm hover:bg-green-600 font-medium transition-colors"><i class="fa-solid fa-plus mr-1"></i> Add</button>
                    </div>
                </div>

                <!-- Manage Habits -->
                <div class="bg-gray-50 dark:bg-gray-800/30 p-4 rounded-xl">
                    <h4 class="font-bold text-gray-800 dark:text-white mb-4 flex items-center gap-2"><i class="fa-solid fa-fire text-orange-400"></i> Manage Habits</h4>
                    <div id="settings-habits" class="space-y-2"></div>
                </div>

                <!-- Pomodoro & Notifications Config -->
                <div class="bg-gray-50 dark:bg-gray-800/30 p-4 rounded-xl">
                    <h4 class="font-bold text-gray-800 dark:text-white mb-4 flex items-center gap-2"><i class="fa-solid fa-stopwatch text-red-400"></i> Focus & Alerts</h4>
                    <div class="grid grid-cols-3 gap-3 text-sm mb-4">
                        <div>
                            <label class="text-gray-500 font-medium block mb-1">Work (min)</label>
                            <input type="number" id="cfg-pomo-work" class="w-full bg-white dark:bg-gray-800 border border-gray-200 dark:border-gray-700 rounded-lg p-2.5 dark:text-white outline-none focus:ring-2 theme-ring">
                        </div>
                        <div>
                            <label class="text-gray-500 font-medium block mb-1">Short (min)</label>
                            <input type="number" id="cfg-pomo-short" class="w-full bg-white dark:bg-gray-800 border border-gray-200 dark:border-gray-700 rounded-lg p-2.5 dark:text-white outline-none focus:ring-2 theme-ring">
                        </div>
                        <div>
                            <label class="text-gray-500 font-medium block mb-1">Long (min)</label>
                            <input type="number" id="cfg-pomo-long" class="w-full bg-white dark:bg-gray-800 border border-gray-200 dark:border-gray-700 rounded-lg p-2.5 dark:text-white outline-none focus:ring-2 theme-ring">
                        </div>
                    </div>
                    <button onclick="requestNotifications()" id="notif-btn" class="w-full py-2.5 bg-white dark:bg-gray-800 border border-gray-200 dark:border-gray-700 rounded-lg hover:bg-gray-100 dark:hover:bg-gray-700 text-sm flex items-center justify-center gap-2 dark:text-white font-medium transition-colors">
                        <i class="fa-solid fa-bell"></i> Enable Browser Reminders
                    </button>
                </div>

                <!-- Data Management -->
                <div class="bg-red-50 dark:bg-red-900/10 p-4 rounded-xl border border-red-100 dark:border-red-900/30">
                    <h4 class="font-bold text-red-600 dark:text-red-400 mb-2">Danger Zone</h4>
                    <p class="text-xs text-red-500/70 mb-3">Permanently delete all tasks, lists, and habits from this browser.</p>
                    <button onclick="clearData()" class="px-4 py-2 bg-red-500 text-white rounded-lg hover:bg-red-600 text-sm font-medium transition-colors"><i class="fa-solid fa-trash-can mr-2"></i>Reset All Data</button>
                </div>
            </div>
            <div class="p-5 border-t border-gray-100 dark:border-gray-800 flex justify-end shrink-0 bg-gray-50 dark:bg-gray-900/50">
                <button onclick="saveSettings()" class="px-8 py-2.5 theme-bg text-white font-bold rounded-xl shadow-md hover:shadow-lg transition-all active:scale-95">Done</button>
            </div>
        </div>
    </div>

    <!-- Focus Mode Overlay -->
    <div id="focus-mode-overlay" class="fixed inset-0 bg-white dark:bg-darkbg z-[60] hidden flex-col items-center justify-center p-6 text-center transition-opacity duration-500 opacity-0">
        <button onclick="exitFocusMode()" class="absolute top-6 left-6 text-gray-400 hover:text-gray-900 dark:hover:text-white text-xl transition-colors"><i class="fa-solid fa-compress mr-2"></i>Exit Focus</button>
        <div class="max-w-xl w-full animate-slide-up">
            <h2 id="focus-task-title" class="text-3xl md:text-5xl font-bold mb-12 text-gray-800 dark:text-gray-100 tracking-tight">Task Title</h2>
            <div class="relative w-64 h-64 md:w-80 md:h-80 mx-auto mb-12 flex items-center justify-center rounded-full border-8 border-gray-50 dark:border-gray-800/50 shadow-inner">
                <svg class="absolute inset-0 w-full h-full transform -rotate-90">
                    <circle id="timer-progress" cx="50%" cy="50%" r="48%" fill="none" stroke="var(--theme-color, #6366f1)" stroke-width="8" stroke-dasharray="100" stroke-dashoffset="0" class="transition-all duration-1000 ease-linear drop-shadow-md"></circle>
                </svg>
                <div class="text-center relative z-10">
                    <div id="timer-display" class="text-6xl md:text-7xl font-black tabular-nums text-gray-800 dark:text-white tracking-tighter">25:00</div>
                    <div id="timer-mode-label" class="theme-text font-bold mt-2 uppercase tracking-[0.2em] text-sm">Work Session</div>
                </div>
            </div>
            <div class="flex justify-center gap-6">
                <button onclick="toggleTimer()" id="timer-toggle-btn" class="w-16 h-16 theme-bg text-white rounded-full text-2xl shadow-lg flex items-center justify-center active:scale-90 transition-transform duration-200"><i class="fa-solid fa-play"></i></button>
                <button onclick="resetTimer()" class="w-16 h-16 bg-gray-100 dark:bg-gray-800 hover:bg-gray-200 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-200 rounded-full text-xl shadow flex items-center justify-center active:scale-90 transition-all duration-200"><i class="fa-solid fa-rotate-right"></i></button>
                <button onclick="completeFocusTask()" class="w-16 h-16 bg-green-500 hover:bg-green-600 text-white rounded-full text-xl shadow flex items-center justify-center active:scale-90 transition-all duration-200"><i class="fa-solid fa-check"></i></button>
            </div>
        </div>
    </div>

<script>
/**
 * OMNI PRODUCTIVITY SUITE - V3.0
 * Animations, Subtasks, Dynamic Calendar, UI Polish, Settings Fixes.
 */

const generateId = () => crypto.randomUUID();
const escapeHTML = (str) => str ? str.replace(/[&<>'"]/g, tag => ({'&': '&amp;', '<': '&lt;', '>': '&gt;', "'": '&#39;', '"': '&quot;'}[tag])) : '';

// --- STATE MANAGEMENT ---
const STORAGE_KEY = 'omni_suite_v3_state';

const defaultState = {
    theme: 'light',
    accentColor: '#6366f1',
    bgUrl: '',
    bgOpacity: 0.9,
    currentView: 'inbox',
    lists: [
        { id: 'groceries', name: 'Groceries', icon: 'fa-basket-shopping' },
        { id: 'work', name: 'Work', icon: 'fa-briefcase' }
    ],
    tasks: [],
    habits: [
        { id: generateId(), title: 'Drink Water', color: 'text-blue-500', logs: {} },
        { id: generateId(), title: 'Read 10 Pages', color: 'text-purple-500', logs: {} }
    ],
    pomodoro: { work: 25, shortBreak: 5, longBreak: 15, stats: { totalWorkMins: 0, completed: 0 } },
    stats: { tasksCompleted: 0 }
};

let state = JSON.parse(localStorage.getItem(STORAGE_KEY));
if (!state || !state.lists) state = defaultState;

// V3 Migration Hooks
if(state.bgOpacity === undefined) state.bgOpacity = 0.9;
// Remove 'inbox' from state.lists if it exists from V2
state.lists = state.lists.filter(l => l.id !== 'inbox');
state.tasks.forEach(t => { if(!t.subtasks) t.subtasks = []; });

const saveState = () => {
    try { localStorage.setItem(STORAGE_KEY, JSON.stringify(state)); } 
    catch (e) { alert("Storage Full! Please delete some file attachments."); }
};

// --- INITIALIZATION ---
let notificationInterval;
let currentCalDate = new Date(); // Track calendar month separately

function init() {
    applyTheme();
    setBackground(state.bgUrl, state.bgOpacity);
    
    // Welcome data
    if (state.tasks.length === 0) {
        let tmrw = new Date(); tmrw.setDate(tmrw.getDate() + 1);
        state.tasks.push(
            { id: generateId(), title: "Welcome to Omni v3! 👋", listId: 'inbox', priority: 1, due: new Date().toISOString().slice(0,16), status: 'todo', recurring: 'none', pomodoros: 0, attachments: [], subtasks: [{id: generateId(), title: 'Try checking off this subtask', done: false}] },
            { id: generateId(), title: "Explore Custom Lists in the Sidebar", listId: 'work', priority: 3, due: tmrw.toISOString().slice(0,16), status: 'todo', recurring: 'none', pomodoros: 0, attachments: [], subtasks: [] }
        );
        saveState();
    }

    renderNav();
    changeView(state.currentView);
    setupNotifications();
}

function applyTheme() {
    if (state.theme === 'dark' || (!('theme' in state) && window.matchMedia('(prefers-color-scheme: dark)').matches)) {
        document.documentElement.classList.add('dark');
        state.theme = 'dark';
    } else {
        document.documentElement.classList.remove('dark');
        state.theme = 'light';
    }
    document.documentElement.style.setProperty('--theme-color', state.accentColor);
}

function toggleTheme() {
    state.theme = state.theme === 'dark' ? 'light' : 'dark';
    applyTheme(); saveState();
}

function setAccent(color) {
    state.accentColor = color;
    applyTheme(); saveState(); renderNav(); changeView(state.currentView);
}

function setBackground(url, opacity) {
    state.bgUrl = url;
    state.bgOpacity = opacity !== undefined ? opacity : state.bgOpacity;
    const main = document.getElementById('main-content');
    const overlay = document.getElementById('main-overlay');
    
    if(url) {
        main.style.backgroundImage = `url(${url})`;
        overlay.style.backgroundColor = state.theme === 'dark' ? `rgba(15, 23, 42, ${state.bgOpacity})` : `rgba(249, 250, 251, ${state.bgOpacity})`;
    } else {
        main.style.backgroundImage = 'none';
        overlay.style.backgroundColor = ''; // Revert to class-based bg
    }
    saveState();
}

// --- NAVIGATION ENGINE ---
const coreNavItems = [
    { id: 'inbox', label: 'Inbox', icon: 'fa-inbox' },
    { id: 'today', label: 'Today', icon: 'fa-star' },
    { id: 'upcoming', label: 'Upcoming', icon: 'fa-calendar-days' }
];
const viewNavItems = [
    { id: 'calendar', label: 'Calendar', icon: 'fa-calendar-alt' },
    { id: 'timeline', label: 'Timeline', icon: 'fa-stream' },
    { id: 'matrix', label: 'Matrix', icon: 'fa-table-cells' },
    { id: 'countdown', label: 'Countdown', icon: 'fa-hourglass-half' }
];
const trackNavItems = [
    { id: 'habits', label: 'Habits', icon: 'fa-fire' },
    { id: 'stats', label: 'Stats', icon: 'fa-chart-pie' }
];

function renderNav() {
    const dNav = document.getElementById('desktop-nav');
    const mNav = document.getElementById('mobile-nav');
    dNav.innerHTML = ''; mNav.innerHTML = '';

    const createBtn = (id, label, icon, isList=false) => {
        const isActive = state.currentView === id;
        const dClass = isActive ? 'theme-bg text-white shadow-md font-bold' : 'text-gray-600 dark:text-gray-400 hover:bg-gray-100 dark:hover:bg-gray-800 font-medium hover:pl-4';
        const mClass = isActive ? 'theme-text font-bold scale-110' : 'text-gray-500 dark:text-gray-400 hover:text-gray-800 dark:hover:text-gray-200';
        
        dNav.innerHTML += `<button onclick="changeView('${id}')" class="w-full flex items-center px-3 py-2.5 mb-1 rounded-xl transition-all duration-200 ${dClass}">
            <i class="fa-solid ${icon} w-6 text-center mr-2 ${isActive ? 'text-white' : ''}"></i> <span class="truncate">${label}</span>
        </button>`;

        if(['inbox', 'today', 'calendar', 'habits'].includes(id) || (isList && id==='groceries')) {
            mNav.innerHTML += `<button onclick="changeView('${id}')" class="flex flex-col items-center justify-center min-w-[4rem] h-full transition-all duration-200 ${mClass}">
                <i class="fa-solid ${icon} text-xl mb-1"></i><span class="text-[10px] truncate w-full text-center">${label}</span>
            </button>`;
        }
    };

    dNav.innerHTML += `<div class="text-[10px] font-black text-gray-400 uppercase tracking-widest mb-2 mt-2 px-3">Core</div>`;
    coreNavItems.forEach(i => createBtn(i.id, i.label, i.icon));
    
    dNav.innerHTML += `<div class="text-[10px] font-black text-gray-400 uppercase tracking-widest mb-2 mt-6 px-3 flex justify-between items-center">Views <button onclick="openSettings()" class="hover:text-gray-600"><i class="fa-solid fa-plus"></i></button></div>`;
    viewNavItems.forEach(i => createBtn(i.id, i.label, i.icon));
    // Inject Custom Lists under Views
    state.lists.forEach(l => createBtn(l.id, l.name, l.icon || 'fa-list-ul', true));
    
    dNav.innerHTML += `<div class="text-[10px] font-black text-gray-400 uppercase tracking-widest mb-2 mt-6 px-3">Track</div>`;
    trackNavItems.forEach(i => createBtn(i.id, i.label, i.icon));
}

function changeView(id) {
    // Add fade out animation to view container
    const container = document.getElementById('view-container');
    container.classList.remove('animate-fade-in');
    void container.offsetWidth; // trigger reflow
    container.classList.add('animate-fade-in');

    state.currentView = id;
    saveState();
    renderNav();
    
    const titleMap = [...coreNavItems, ...viewNavItems, ...trackNavItems, ...state.lists.map(l=>({id:l.id, label:l.name}))].find(i=>i.id===id)?.label || 'Omni';
    document.getElementById('mobile-header-title').innerText = titleMap;

    // Routing
    if (id === 'inbox') renderListView(container, 'Inbox', t => t.listId === 'inbox' && t.status !== 'done');
    else if (state.lists.some(l=>l.id===id)) renderListView(container, titleMap, t => t.listId === id && t.status !== 'done');
    else if (id === 'today') renderListView(container, 'Today', t => t.status !== 'done' && t.due && t.due.startsWith(getTodayStr()));
    else if (id === 'upcoming') renderListView(container, 'Upcoming', t => t.status !== 'done' && t.due && t.due >= getTodayStr());
    else if (id === 'calendar') renderCalendarView(container);
    else if (id === 'timeline') renderTimelineView(container);
    else if (id === 'matrix') renderMatrixView(container);
    else if (id === 'countdown') renderCountdownView(container);
    else if (id === 'habits') renderHabitsView(container);
    else if (id === 'stats') renderStatsView(container);
}

// --- VIEW GENERATORS ---

function renderListView(container, title, filterFn) {
    let html = `<h2 class="text-3xl md:text-4xl font-black mb-6 hidden md:block text-gray-800 dark:text-white tracking-tight animate-slide-up">${title}</h2>`;
    let tasks = state.tasks.filter(filterFn).sort((a,b) => new Date(a.due||'9999') - new Date(b.due||'9999') || a.priority - b.priority);
    
    if (!tasks.length) {
        html += `<div class="flex flex-col items-center justify-center py-20 text-gray-400 animate-scale-in">
            <i class="fa-solid fa-leaf text-6xl mb-4 opacity-50"></i>
            <p class="text-lg font-medium">All clear here!</p></div>`;
    } else {
        html += `<div class="space-y-3">` + tasks.map((t, i) => generateTaskHTML(t, i)).join('') + `</div>`;
    }
    
    // Show recently completed
    let done = state.tasks.filter(t => t.status === 'done' && (t.listId === state.currentView || state.currentView === 'today' || state.currentView === 'upcoming')).slice(0,5);
    if(done.length && !['today','upcoming'].includes(state.currentView)) {
        html += `<h3 class="mt-10 mb-3 text-xs font-bold text-gray-400 uppercase tracking-widest">Recently Completed</h3><div class="space-y-2 opacity-60">` + done.map((t, i) => generateTaskHTML(t, i)).join('') + `</div>`;
    }

    container.innerHTML = html;
}

function generateTaskHTML(task, index) {
    const isDone = task.status === 'done';
    let dueHtml = '';
    if (task.due) {
        const d = new Date(task.due);
        const isPast = d < new Date() && !isDone;
        dueHtml = `<span class="text-xs ${isPast?'text-red-500':'text-gray-500'} font-medium"><i class="fa-regular fa-clock mr-1"></i>${d.toLocaleString([], {month:'short', day:'numeric', hour:'2-digit', minute:'2-digit'})}</span>`;
    }
    
    let attachHtml = task.attachments && task.attachments.length ? `<span class="text-xs text-blue-500"><i class="fa-solid fa-paperclip mr-1"></i>${task.attachments.length}</span>` : '';
    let recurHtml = task.recurring && task.recurring !== 'none' ? `<span class="text-xs text-indigo-500"><i class="fa-solid fa-repeat mr-1"></i></span>` : '';
    
    // Subtask Progress
    let subHtml = '';
    if(task.subtasks && task.subtasks.length > 0) {
        const completedSubs = task.subtasks.filter(s => s.done).length;
        const totalSubs = task.subtasks.length;
        const isAllDone = completedSubs === totalSubs;
        subHtml = `<span class="text-xs ${isAllDone ? 'text-green-500' : 'text-gray-500'}"><i class="fa-solid fa-list-check mr-1"></i>${completedSubs}/${totalSubs}</span>`;
    }

    const staggerClass = index < 5 ? `stagger-${index+1}` : '';

    return `
        <div class="group flex items-start gap-3 p-4 bg-white/80 dark:bg-darkcard/80 backdrop-blur-md rounded-2xl shadow-sm hover:shadow-md border border-gray-100/50 dark:border-gray-700/50 transition-all duration-300 transform hover:-translate-y-0.5 animate-slide-up opacity-0 ${staggerClass} ${isDone ? 'opacity-50 grayscale' : ''}" draggable="true" ondragstart="matrixDragStart(event, '${task.id}')">
            <div class="mt-1 shrink-0">
                <input type="checkbox" class="task-checkbox" ${isDone ? 'checked' : ''} onchange="toggleTaskStatus('${task.id}')">
            </div>
            <div class="flex-1 min-w-0 cursor-pointer" onclick="editTask('${task.id}')">
                <div class="flex items-center gap-2">
                    <h4 class="text-base font-bold text-gray-800 dark:text-gray-100 ${isDone ? 'line-through text-gray-400' : ''} truncate transition-colors">${escapeHTML(task.title)}</h4>
                    ${task.priority < 4 ? `<i class="fa-solid fa-flag p${task.priority} text-xs"></i>` : ''}
                </div>
                ${task.desc ? `<p class="text-sm text-gray-500 dark:text-gray-400 mt-1 line-clamp-1">${escapeHTML(task.desc)}</p>` : ''}
                <div class="flex flex-wrap gap-3 mt-2.5 items-center">
                    ${dueHtml} ${recurHtml} ${attachHtml} ${subHtml}
                    ${task.pomodoros > 0 ? `<span class="text-xs text-red-400"><i class="fa-solid fa-stopwatch mr-1"></i>${task.pomodoros}</span>` : ''}
                </div>
            </div>
            <div class="flex flex-col items-end gap-2 shrink-0 opacity-100 md:opacity-0 group-hover:opacity-100 transition-opacity">
                <button onclick="startFocusMode('${task.id}')" class="text-gray-400 hover:theme-text transition tooltip w-8 h-8 rounded-full hover:bg-gray-100 dark:hover:bg-gray-800 flex items-center justify-center"><i class="fa-solid fa-crosshairs"></i></button>
                <button onclick="deleteTask('${task.id}')" class="text-gray-300 hover:text-red-500 transition tooltip w-8 h-8 rounded-full hover:bg-gray-100 dark:hover:bg-gray-800 flex items-center justify-center"><i class="fa-solid fa-trash"></i></button>
            </div>
        </div>
    `;
}

// --- CALENDAR VIEW (Upgraded with Navigation) ---
function renderCalendarView(container) {
    let y = currentCalDate.getFullYear();
    let m = currentCalDate.getMonth();
    
    let firstDay = new Date(y, m, 1).getDay();
    let daysInMonth = new Date(y, m + 1, 0).getDate();
    let monthName = currentCalDate.toLocaleString('default', { month: 'long' });
    
    let html = `
    <div class="flex justify-between items-center mb-8 animate-slide-up">
        <h2 class="text-3xl font-black text-gray-800 dark:text-white tracking-tight">${monthName} ${y}</h2>
        <div class="flex gap-2">
            <button onclick="changeCalMonth(-1)" class="w-10 h-10 rounded-full bg-white dark:bg-gray-800 shadow-sm border border-gray-100 dark:border-gray-700 hover:theme-text flex items-center justify-center transition-colors"><i class="fa-solid fa-chevron-left"></i></button>
            <button onclick="changeCalMonth(0)" class="px-4 h-10 rounded-full bg-white dark:bg-gray-800 shadow-sm border border-gray-100 dark:border-gray-700 hover:theme-text font-bold text-sm transition-colors">Today</button>
            <button onclick="changeCalMonth(1)" class="w-10 h-10 rounded-full bg-white dark:bg-gray-800 shadow-sm border border-gray-100 dark:border-gray-700 hover:theme-text flex items-center justify-center transition-colors"><i class="fa-solid fa-chevron-right"></i></button>
        </div>
    </div>
    <div class="grid grid-cols-7 gap-2 text-center text-xs font-black text-gray-400 mb-2 uppercase tracking-widest animate-fade-in">
        <div>Sun</div><div>Mon</div><div>Tue</div><div>Wed</div><div>Thu</div><div>Fri</div><div>Sat</div>
    </div>
    <div class="grid grid-cols-7 gap-1 md:gap-2 animate-scale-in">`;
    
    for(let i=0; i<firstDay; i++) html += `<div class="p-2 rounded-xl bg-gray-50/30 dark:bg-gray-900/10"></div>`;
    
    for(let i=1; i<=daysInMonth; i++) {
        let dateStr = `${y}-${String(m+1).padStart(2,'0')}-${String(i).padStart(2,'0')}`;
        let dayTasks = state.tasks.filter(t => t.due && t.due.startsWith(dateStr) && t.status !== 'done');
        let isToday = dateStr === getTodayStr();
        
        let tDots = dayTasks.slice(0,3).map(t => `<div class="w-2 h-2 rounded-full ${t.priority===1?'bg-red-500': t.priority===2?'bg-yellow-500':'theme-bg'} shadow-sm"></div>`).join('');
        let extra = dayTasks.length > 3 ? `<div class="text-[9px] font-bold text-gray-500">+${dayTasks.length-3}</div>` : '';

        html += `<div class="min-h-[5rem] md:min-h-[6rem] p-1.5 md:p-2 border rounded-xl glass-panel flex flex-col items-center transition-all hover:shadow-md cursor-pointer ${isToday ? 'border-[var(--theme-color)] ring-2 ring-[var(--theme-color)] ring-opacity-50' : 'border-gray-200/50 dark:border-gray-700/50'}">
            <span class="${isToday ? 'theme-bg text-white w-7 h-7 rounded-full shadow-md flex items-center justify-center font-bold' : 'text-gray-700 dark:text-gray-300 font-medium'} text-sm mt-1">${i}</span>
            <div class="flex flex-wrap justify-center items-center gap-1 mt-auto pb-1.5">${tDots}${extra}</div>
        </div>`;
    }
    
    html += `</div>`;
    container.innerHTML = html;
}

function changeCalMonth(delta) {
    if(delta === 0) currentCalDate = new Date();
    else currentCalDate.setMonth(currentCalDate.getMonth() + delta);
    renderCalendarView(document.getElementById('view-container'));
}

// --- TIMELINE VIEW ---
function renderTimelineView(container) {
    let tasks = state.tasks.filter(t => t.status !== 'done' && t.due).sort((a,b) => new Date(a.due) - new Date(b.due));
    
    let html = `<h2 class="text-3xl font-black mb-8 text-gray-800 dark:text-white tracking-tight animate-slide-up">Timeline</h2><div class="timeline-container animate-fade-in">`;
    
    let currentDay = '';
    tasks.forEach((t, i) => {
        let d = new Date(t.due);
        let dayStr = d.toLocaleDateString(undefined, {weekday:'long', month:'short', day:'numeric'});
        let timeStr = d.toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'});
        
        if (dayStr !== currentDay) {
            html += `<h3 class="mt-6 mb-4 font-black text-gray-600 dark:text-gray-300 bg-white dark:bg-gray-800 inline-block px-4 py-1.5 rounded-full text-xs uppercase tracking-widest shadow-sm border border-gray-100 dark:border-gray-700">${dayStr}</h3>`;
            currentDay = dayStr;
        }
        
        const staggerClass = i < 5 ? `stagger-${i+1}` : '';
        html += `
            <div class="relative pl-6 pb-6 cursor-pointer group animate-slide-up opacity-0 ${staggerClass}" onclick="editTask('${t.id}')">
                <div class="timeline-dot group-hover:scale-125 transition-transform duration-300"></div>
                <div class="glass-panel border border-gray-100/50 dark:border-gray-700/50 p-4 rounded-2xl shadow-sm group-hover:shadow-md transition-all flex justify-between items-center transform group-hover:translate-x-1">
                    <div>
                        <div class="text-xs text-theme-text font-bold mb-1 opacity-80">${timeStr}</div>
                        <div class="font-bold text-gray-800 dark:text-gray-100">${escapeHTML(t.title)}</div>
                    </div>
                    ${t.priority < 4 ? `<i class="fa-solid fa-flag p${t.priority} text-sm"></i>` : ''}
                </div>
            </div>
        `;
    });

    if(!tasks.length) html += `<p class="text-gray-500 italic mt-4 font-medium">No upcoming scheduled tasks.</p>`;
    html += `</div>`;
    container.innerHTML = html;
}

// --- MATRIX VIEW ---
function renderMatrixView(container) {
    let html = `
        <div class="animate-slide-up">
            <h2 class="text-3xl font-black mb-2 text-gray-800 dark:text-white tracking-tight">Eisenhower Matrix</h2>
            <p class="text-sm text-gray-500 mb-6 font-medium">Drag and drop tasks to prioritize instantly.</p>
        </div>`;
    
    const isUrgent = (t) => { if(!t.due) return false; return (new Date(t.due).getTime() - Date.now()) <= 172800000; }; // 48h
    const isImportant = (t) => t.priority === 1 || t.priority === 2;
    const getQ = (u, i) => state.tasks.filter(t => t.status !== 'done' && isUrgent(t) === u && isImportant(t) === i);

    const quadClass = "p-4 border border-gray-200/50 dark:border-gray-700/50 rounded-2xl glass-panel h-64 overflow-y-auto flex flex-col transition-all duration-300 hover:shadow-lg";

    html += `
        <div class="grid grid-cols-1 md:grid-cols-2 gap-4 animate-scale-in">
            <div class="${quadClass} ring-2 ring-red-500/20 hover:ring-red-500/40" ondragover="matrixDragOver(event)" ondrop="matrixDrop(event, true, true)">
                <h3 class="font-black text-red-500 mb-3 sticky top-0 backdrop-blur-md z-10 pb-2 flex items-center gap-2"><i class="fa-solid fa-fire"></i> Do First</h3>
                <div class="space-y-2 flex-1">${getQ(true,true).map(generateMatrixCard).join('')}</div>
            </div>
            <div class="${quadClass} ring-2 ring-blue-500/20 hover:ring-blue-500/40" ondragover="matrixDragOver(event)" ondrop="matrixDrop(event, false, true)">
                <h3 class="font-black text-blue-500 mb-3 sticky top-0 backdrop-blur-md z-10 pb-2 flex items-center gap-2"><i class="fa-solid fa-calendar-check"></i> Schedule</h3>
                <div class="space-y-2 flex-1">${getQ(false,true).map(generateMatrixCard).join('')}</div>
            </div>
            <div class="${quadClass} ring-2 ring-yellow-500/20 hover:ring-yellow-500/40" ondragover="matrixDragOver(event)" ondrop="matrixDrop(event, true, false)">
                <h3 class="font-black text-yellow-500 mb-3 sticky top-0 backdrop-blur-md z-10 pb-2 flex items-center gap-2"><i class="fa-solid fa-people-arrows"></i> Delegate</h3>
                <div class="space-y-2 flex-1">${getQ(true,false).map(generateMatrixCard).join('')}</div>
            </div>
            <div class="${quadClass} ring-2 ring-gray-500/20 hover:ring-gray-500/40" ondragover="matrixDragOver(event)" ondrop="matrixDrop(event, false, false)">
                <h3 class="font-black text-gray-500 mb-3 sticky top-0 backdrop-blur-md z-10 pb-2 flex items-center gap-2"><i class="fa-solid fa-trash-can"></i> Eliminate</h3>
                <div class="space-y-2 flex-1">${getQ(false,false).map(generateMatrixCard).join('')}</div>
            </div>
        </div>
    `;
    container.innerHTML = html;
}

function generateMatrixCard(task) {
    return `<div class="bg-white/80 dark:bg-gray-800/80 p-3 rounded-xl text-sm border border-gray-100 dark:border-gray-700 flex justify-between items-center shadow-sm cursor-grab active:cursor-grabbing hover:border-[var(--theme-color)] transition-colors" draggable="true" ondragstart="matrixDragStart(event, '${task.id}')">
        <span class="truncate font-bold">${escapeHTML(task.title)}</span>
        <button onclick="toggleTaskStatus('${task.id}')" class="text-gray-400 hover:text-green-500 ml-2 shrink-0 transition-colors"><i class="fa-regular fa-circle-check text-lg"></i></button>
    </div>`;
}

function matrixDragStart(e, id) { e.dataTransfer.setData('taskId', id); e.target.classList.add('opacity-50'); }
function matrixDragOver(e) { e.preventDefault(); }
function matrixDrop(e, urgent, important) {
    e.preventDefault();
    const id = e.dataTransfer.getData('taskId');
    const t = state.tasks.find(x => x.id === id);
    if(t) {
        t.priority = important ? 2 : 4;
        if(urgent) { let d = new Date(); d.setHours(d.getHours()+24); t.due = d.toISOString().slice(0,16); }
        else if(t.due && new Date(t.due) < new Date(Date.now() + 172800000)) t.due = null; 
        saveState(); changeView('matrix');
    }
}

// --- COUNTDOWN VIEW ---
function renderCountdownView(container) {
    let tasks = state.tasks.filter(t => t.status !== 'done' && t.due && new Date(t.due) > new Date()).sort((a,b) => new Date(a.due) - new Date(b.due));
    
    let html = `<h2 class="text-3xl font-black mb-8 text-gray-800 dark:text-white tracking-tight animate-slide-up">Countdowns</h2><div class="grid grid-cols-1 md:grid-cols-2 gap-4 animate-scale-in">`;
    
    const now = new Date();
    tasks.forEach(t => {
        let d = new Date(t.due);
        let diff = d - now;
        let days = Math.floor(diff / (1000 * 60 * 60 * 24));
        let hours = Math.floor((diff / (1000 * 60 * 60)) % 24);
        
        html += `
        <div class="glass-panel border border-gray-200/50 dark:border-gray-700/50 p-6 rounded-3xl shadow-sm text-center relative overflow-hidden transition-transform hover:scale-[1.02]">
            <div class="absolute top-0 left-0 w-full h-1.5 theme-bg"></div>
            <h3 class="text-xl font-bold text-gray-800 dark:text-white mb-5 line-clamp-1">${escapeHTML(t.title)}</h3>
            <div class="flex justify-center gap-6 text-gray-700 dark:text-gray-200">
                <div class="flex flex-col items-center"><span class="text-5xl font-black theme-text tracking-tighter">${days}</span><span class="text-[10px] uppercase font-black text-gray-400 tracking-widest mt-1">Days</span></div>
                <div class="text-4xl font-light text-gray-300 mt-1">:</div>
                <div class="flex flex-col items-center"><span class="text-5xl font-black theme-text tracking-tighter">${hours}</span><span class="text-[10px] uppercase font-black text-gray-400 tracking-widest mt-1">Hours</span></div>
            </div>
            <div class="mt-6 inline-block bg-gray-100 dark:bg-gray-800 px-3 py-1 rounded-full text-xs font-bold text-gray-500"><i class="fa-regular fa-calendar mr-1"></i> ${d.toLocaleDateString()}</div>
        </div>`;
    });

    if(!tasks.length) html += `<p class="text-gray-500 italic font-medium">No future deadlines to count down.</p>`;
    html += `</div>`;
    container.innerHTML = html;
}

// --- HABITS VIEW (Upgraded Add Button) ---
function renderHabitsView(container) {
    let html = `<h2 class="text-3xl font-black mb-6 text-gray-800 dark:text-white tracking-tight animate-slide-up">Habits</h2><div class="space-y-4 animate-scale-in">`;
    const today = getTodayStr();

    state.habits.forEach(habit => {
        const isDone = habit.logs[today];
        let streak=0, d=new Date();
        if(habit.logs[today]) streak++; else d.setDate(d.getDate()-1);
        while(habit.logs[d.toISOString().split('T')[0]]) { streak++; d.setDate(d.getDate()-1); }

        html += `
        <div class="glass-panel p-5 rounded-3xl border border-gray-100/50 dark:border-gray-700/50 flex justify-between items-center shadow-sm hover:shadow-md transition-all">
            <div class="flex items-center gap-4">
                <div class="w-14 h-14 rounded-2xl bg-white dark:bg-gray-800 shadow-sm flex items-center justify-center text-2xl ${habit.color}"><i class="fa-solid fa-star"></i></div>
                <div>
                    <h3 class="font-bold text-lg dark:text-white">${escapeHTML(habit.title)}</h3>
                    <p class="text-sm font-medium text-gray-500">${streak} Day Streak 🔥</p>
                </div>
            </div>
            <button onclick="toggleHabit('${habit.id}', '${today}')" class="w-16 h-16 rounded-2xl flex items-center justify-center text-2xl transition-all shadow-md active:scale-90 ${isDone ? 'bg-green-500 text-white shadow-green-500/30' : 'bg-gray-100 dark:bg-gray-800 text-gray-400 hover:bg-gray-200'}">
                <i class="fa-solid ${isDone ? 'fa-check' : 'fa-plus'}"></i>
            </button>
        </div>`;
    });
    
    // Add New Habit Button inline
    html += `
        <button onclick="openHabitModal()" class="w-full border-2 border-dashed border-gray-300 dark:border-gray-700 rounded-3xl p-5 text-gray-500 dark:text-gray-400 font-bold hover:bg-gray-50 dark:hover:bg-gray-800/50 transition-colors flex items-center justify-center gap-2">
            <i class="fa-solid fa-plus"></i> Create New Habit
        </button>
    </div>`;
    container.innerHTML = html;
}

function toggleHabit(id, date) {
    const h = state.habits.find(x => x.id === id);
    if(h) { h.logs[date] = !h.logs[date]; saveState(); changeView('habits'); }
}

// --- STATS VIEW ---
function renderStatsView(container) {
    const total = state.tasks.length;
    const done = state.tasks.filter(t => t.status === 'done').length;
    const pct = total ? Math.round((done/total)*100) : 0;
    
    container.innerHTML = `
        <h2 class="text-3xl font-black mb-6 text-gray-800 dark:text-white animate-slide-up">Insights</h2>
        <div class="grid grid-cols-1 md:grid-cols-2 gap-4 animate-scale-in">
            <div class="glass-panel p-8 rounded-3xl border border-gray-100/50 dark:border-gray-700/50 text-center shadow-sm">
                <h4 class="text-gray-400 font-bold uppercase tracking-widest text-xs mb-4">Completion Rate</h4>
                <div class="text-6xl font-black theme-text mb-4 tracking-tighter">${pct}%</div>
                <div class="w-full bg-gray-200 dark:bg-gray-700 rounded-full h-4 mb-3 overflow-hidden shadow-inner"><div class="theme-bg h-full rounded-full transition-all duration-1000" style="width: ${pct}%"></div></div>
                <p class="text-sm font-medium text-gray-500">${done} of ${total} tasks completed</p>
            </div>
            <div class="glass-panel p-8 rounded-3xl border border-gray-100/50 dark:border-gray-700/50 text-center shadow-sm">
                <h4 class="text-gray-400 font-bold uppercase tracking-widest text-xs mb-4">Deep Focus Time</h4>
                <div class="text-6xl font-black text-orange-500 mb-4 tracking-tighter">${Math.floor(state.pomodoro.stats.totalWorkMins/60)}h ${state.pomodoro.stats.totalWorkMins%60}m</div>
                <div class="w-16 h-1 bg-orange-500/20 rounded-full mx-auto mb-3"></div>
                <p class="text-sm font-medium text-gray-500">${state.pomodoro.stats.completed} Successful Sessions</p>
            </div>
        </div>
    `;
}

// --- TASK CRUD, SUBTASKS & MODAL ---
const getTodayStr = () => new Date().toISOString().split('T')[0];
let currentTaskAttachments = [];
let currentSubtasks = [];

function openTaskModal(taskId = null) {
    const modal = document.getElementById('task-modal');
    const content = document.getElementById('task-modal-content');
    const titleObj = document.getElementById('tm-title');
    const dueObj = document.getElementById('tm-due');
    const listObj = document.getElementById('tm-list');
    const prioObj = document.getElementById('tm-priority');
    const repObj = document.getElementById('tm-repeat');
    const descObj = document.getElementById('tm-desc');
    const idObj = document.getElementById('tm-id');
    
    // Populate Lists (Only custom lists + Inbox)
    const options = [{id: 'inbox', name: 'Inbox'}, ...state.lists];
    listObj.innerHTML = options.map(l => `<option value="${l.id}">${l.name}</option>`).join('');
    
    if (taskId) {
        const t = state.tasks.find(x => x.id === taskId);
        idObj.value = t.id; titleObj.value = t.title; dueObj.value = t.due || '';
        listObj.value = t.listId || 'inbox'; prioObj.value = t.priority || 4;
        repObj.value = t.recurring || 'none'; descObj.value = t.desc || '';
        currentTaskAttachments = t.attachments ? [...t.attachments] : [];
        currentSubtasks = t.subtasks ? [...t.subtasks] : [];
        document.getElementById('task-modal-title').innerText = "Edit Task";
    } else {
        idObj.value = ''; titleObj.value = ''; dueObj.value = '';
        listObj.value = state.lists.some(l=>l.id===state.currentView) ? state.currentView : 'inbox';
        prioObj.value = 4; repObj.value = 'none'; descObj.value = '';
        currentTaskAttachments = []; currentSubtasks = [];
        document.getElementById('task-modal-title').innerText = "New Task";
    }
    
    renderAttachments();
    renderSubtasks();
    
    modal.classList.remove('hidden'); modal.classList.add('flex');
    // Trigger animation
    setTimeout(() => {
        modal.classList.remove('opacity-0');
        content.classList.remove('translate-y-full', 'md:translate-y-8');
        titleObj.focus();
    }, 10);
}

function closeTaskModal() {
    const modal = document.getElementById('task-modal');
    const content = document.getElementById('task-modal-content');
    modal.classList.add('opacity-0');
    content.classList.add('translate-y-full', 'md:translate-y-8');
    setTimeout(() => {
        modal.classList.add('hidden'); modal.classList.remove('flex');
    }, 300);
}

// Subtask Engine
function renderSubtasks() {
    const container = document.getElementById('tm-subtasks-list');
    container.innerHTML = currentSubtasks.map((s, i) => `
        <div class="flex justify-between items-center bg-white dark:bg-gray-800 border border-gray-100 dark:border-gray-700 p-2 rounded-lg text-sm">
            <div class="flex items-center gap-2">
                <input type="checkbox" class="subtask-checkbox accent-indigo-500" ${s.done ? 'checked' : ''} onchange="toggleSubtask(${i})">
                <span class="${s.done ? 'line-through text-gray-400' : 'text-gray-700 dark:text-gray-200'} font-medium">${escapeHTML(s.title)}</span>
            </div>
            <button onclick="removeSubtask(${i})" class="text-gray-400 hover:text-red-500"><i class="fa-solid fa-times"></i></button>
        </div>
    `).join('');
}
function addSubtask() {
    const input = document.getElementById('tm-new-subtask');
    if(input.value.trim()) {
        currentSubtasks.push({ id: generateId(), title: input.value.trim(), done: false });
        input.value = ''; renderSubtasks();
    }
}
function toggleSubtask(index) { currentSubtasks[index].done = !currentSubtasks[index].done; renderSubtasks(); }
function removeSubtask(index) { currentSubtasks.splice(index, 1); renderSubtasks(); }

function saveTask() {
    const id = document.getElementById('tm-id').value || generateId();
    const tIndex = state.tasks.findIndex(t => t.id === id);
    const title = document.getElementById('tm-title').value.trim();
    if(!title) return alert('Title required!');

    const newTask = {
        id, title,
        due: document.getElementById('tm-due').value,
        listId: document.getElementById('tm-list').value,
        priority: parseInt(document.getElementById('tm-priority').value),
        recurring: document.getElementById('tm-repeat').value,
        desc: document.getElementById('tm-desc').value,
        attachments: currentTaskAttachments,
        subtasks: currentSubtasks,
        status: tIndex >= 0 ? state.tasks[tIndex].status : 'todo',
        pomodoros: tIndex >= 0 ? state.tasks[tIndex].pomodoros : 0
    };

    if (tIndex >= 0) state.tasks[tIndex] = newTask; else state.tasks.push(newTask);
    saveState(); closeTaskModal(); changeView(state.currentView);
}

function editTask(id) { openTaskModal(id); }

function toggleTaskStatus(id) {
    const t = state.tasks.find(x => x.id === id);
    if(t) {
        t.status = t.status === 'done' ? 'todo' : 'done';
        if(t.status === 'done') {
            state.stats.tasksCompleted++;
            if(t.recurring && t.recurring !== 'none') createRecurringClone(t);
        }
        saveState(); changeView(state.currentView);
    }
}

function createRecurringClone(task) {
    if(!task.due) return;
    let d = new Date(task.due);
    if(task.recurring === 'daily') d.setDate(d.getDate() + 1);
    else if(task.recurring === 'weekly') d.setDate(d.getDate() + 7);
    else if(task.recurring === 'monthly') d.setMonth(d.getMonth() + 1);
    
    // Reset subtasks for the new clone
    const clonedSubs = task.subtasks ? task.subtasks.map(s => ({...s, done: false})) : [];

    state.tasks.push({...task, id: generateId(), due: d.toISOString().slice(0,16), status: 'todo', pomodoros: 0, subtasks: clonedSubs});
}

function deleteTask(id) {
    if(confirm('Delete this task?')) {
        state.tasks = state.tasks.filter(t => t.id !== id);
        saveState(); changeView(state.currentView);
    }
}

// --- FILE UPLOADS ---
function handleFileUpload(e) {
    const file = e.target.files[0];
    if(!file) return;
    if(file.size > 2 * 1024 * 1024) return alert("File too large. Max 2MB allowed."); 
    
    document.getElementById('tm-file-status').innerText = "Uploading...";
    const reader = new FileReader();
    reader.onload = (ev) => {
        currentTaskAttachments.push({ name: file.name, data: ev.target.result });
        document.getElementById('tm-file-status').innerText = "";
        renderAttachments();
    };
    reader.readAsDataURL(file);
}

function renderAttachments() {
    const div = document.getElementById('tm-attachments');
    div.innerHTML = currentTaskAttachments.map((f, i) => `
        <div class="flex items-center gap-2 bg-gray-100 dark:bg-gray-800 px-3 py-1.5 rounded-lg text-xs dark:text-gray-300 font-medium">
            <a href="${f.data}" download="${f.name}" class="truncate max-w-[120px] hover:theme-text"><i class="fa-solid fa-file mr-1"></i>${escapeHTML(f.name)}</a>
            <button onclick="removeAttachment(${i})" class="text-gray-400 hover:text-red-500 ml-1"><i class="fa-solid fa-times"></i></button>
        </div>
    `).join('');
}
function removeAttachment(idx) { currentTaskAttachments.splice(idx,1); renderAttachments(); }

// --- SETTINGS & HABIT MODALS ---
function openSettings() {
    const modal = document.getElementById('settings-modal');
    const content = document.getElementById('settings-modal-content');
    renderSettings();
    modal.classList.remove('hidden'); modal.classList.add('flex');
    setTimeout(() => { modal.classList.remove('opacity-0'); content.classList.remove('scale-95'); }, 10);
}
function closeSettings() {
    const modal = document.getElementById('settings-modal');
    const content = document.getElementById('settings-modal-content');
    modal.classList.add('opacity-0'); content.classList.add('scale-95');
    setTimeout(() => { modal.classList.add('hidden'); modal.classList.remove('flex'); }, 300);
}

function renderSettings() {
    document.getElementById('cfg-pomo-work').value = state.pomodoro.work;
    document.getElementById('cfg-pomo-short').value = state.pomodoro.shortBreak;
    document.getElementById('cfg-pomo-long').value = state.pomodoro.longBreak;
    document.getElementById('bg-url-input').value = state.bgUrl || '';
    document.getElementById('bg-opacity').value = state.bgOpacity;
    document.getElementById('opacity-val').innerText = Math.round(state.bgOpacity * 100) + '%';
    
    // Lists Management
    const listDiv = document.getElementById('settings-lists');
    listDiv.innerHTML = state.lists.map(l => `
        <div class="flex justify-between items-center bg-white dark:bg-gray-800 border border-gray-200 dark:border-gray-700 p-2.5 rounded-lg text-sm dark:text-gray-200 font-bold">
            <span><i class="fa-solid ${l.icon} mr-2 theme-text"></i>${escapeHTML(l.name)}</span>
            <button onclick="deleteList('${l.id}')" class="text-gray-400 hover:text-red-500 transition-colors"><i class="fa-solid fa-trash"></i></button>
        </div>
    `).join('');

    // Habit Management
    const habitDiv = document.getElementById('settings-habits');
    habitDiv.innerHTML = state.habits.map(h => `
        <div class="flex justify-between items-center bg-white dark:bg-gray-800 border border-gray-200 dark:border-gray-700 p-2.5 rounded-lg text-sm dark:text-gray-200 font-bold">
            <span><i class="fa-solid fa-star mr-2 ${h.color}"></i>${escapeHTML(h.title)}</span>
            <button onclick="deleteHabit('${h.id}')" class="text-gray-400 hover:text-red-500 transition-colors"><i class="fa-solid fa-trash"></i></button>
        </div>
    `).join('');
}

function updateOpacity(val) {
    document.getElementById('opacity-val').innerText = Math.round(val * 100) + '%';
    setBackground(document.getElementById('bg-url-input').value, parseFloat(val));
}

// Habit Creation Engine
let pendingHabitColor = 'text-blue-500';
function openHabitModal() {
    const modal = document.getElementById('habit-modal');
    const content = document.getElementById('habit-modal-content');
    document.getElementById('hm-title').value = '';
    selectHabitColor('text-blue-500');
    modal.classList.remove('hidden'); modal.classList.add('flex');
    setTimeout(() => { modal.classList.remove('opacity-0'); content.classList.remove('scale-95'); document.getElementById('hm-title').focus(); }, 10);
}
function closeHabitModal() {
    const modal = document.getElementById('habit-modal');
    const content = document.getElementById('habit-modal-content');
    modal.classList.add('opacity-0'); content.classList.add('scale-95');
    setTimeout(() => { modal.classList.add('hidden'); modal.classList.remove('flex'); }, 300);
}
function selectHabitColor(colorClass) {
    pendingHabitColor = colorClass;
    document.querySelectorAll('.habit-color-btn').forEach(btn => {
        if(btn.classList.contains(colorClass.replace('text-','bg-'))) btn.classList.add('ring-gray-400');
        else btn.classList.remove('ring-gray-400');
    });
}
function saveHabit() {
    const title = document.getElementById('hm-title').value.trim();
    if(title) {
        state.habits.push({ id: generateId(), title, color: pendingHabitColor, logs: {} });
        saveState(); closeHabitModal(); changeView('habits');
    }
}
function deleteHabit(id) {
    if(confirm('Delete this habit and all its history?')) {
        state.habits = state.habits.filter(h => h.id !== id);
        saveState(); renderSettings();
        if(state.currentView === 'habits') changeView('habits');
    }
}

function addCustomList() {
    const input = document.getElementById('new-list-name');
    const name = input.value.trim();
    if(name) {
        state.lists.push({ id: 'list-'+generateId().slice(0,5), name, icon: 'fa-list-ul' });
        input.value = ''; saveState(); renderSettings(); renderNav();
    }
}
function deleteList(id) {
    if(confirm('Delete list? Tasks will be moved to Inbox.')) {
        state.tasks.forEach(t => { if(t.listId === id) t.listId = 'inbox'; });
        state.lists = state.lists.filter(l => l.id !== id);
        if(state.currentView === id) state.currentView = 'inbox';
        saveState(); renderSettings(); renderNav(); changeView(state.currentView);
    }
}
function saveSettings() {
    state.pomodoro.work = parseInt(document.getElementById('cfg-pomo-work').value) || 25;
    state.pomodoro.shortBreak = parseInt(document.getElementById('cfg-pomo-short').value) || 5;
    state.pomodoro.longBreak = parseInt(document.getElementById('cfg-pomo-long').value) || 15;
    saveState(); closeSettings();
}
function clearData() {
    if(confirm("DANGER: Wipe all data permanently?")) { localStorage.removeItem(STORAGE_KEY); location.reload(); }
}

// --- NOTIFICATIONS API ---
function requestNotifications() {
    if (!("Notification" in window)) alert("Browser does not support notifications");
    else if (Notification.permission === "granted") new Notification("Omni", { body: "Notifications working!" });
    else Notification.requestPermission().then(p => { if (p === "granted") new Notification("Omni", { body: "Enabled!" }); });
}

function setupNotifications() {
    if(notificationInterval) clearInterval(notificationInterval);
    notificationInterval = setInterval(() => {
        if(Notification.permission !== "granted") return;
        const nowStr = new Date().toISOString().slice(0,16);
        state.tasks.forEach(t => { if(t.status !== 'done' && t.due === nowStr) new Notification("Task Due Now!", { body: t.title }); });
    }, 60000);
}

// --- POMODORO ENGINE ---
let pomodoroTimer, timeLeft, isTimerRunning = false, currentPomodoroMode = 'work', activeFocusTaskId = null;

function startFocusMode(taskId) {
    activeFocusTaskId = taskId;
    const task = state.tasks.find(t => t.id === taskId);
    if(!task) return;
    document.getElementById('focus-task-title').innerText = task.title;
    const overlay = document.getElementById('focus-mode-overlay');
    overlay.classList.remove('hidden'); overlay.classList.add('flex');
    setTimeout(() => overlay.classList.remove('opacity-0'), 10);
    
    currentPomodoroMode = 'work'; timeLeft = state.pomodoro.work * 60; isTimerRunning = false;
    updateTimerDisplay();
}

function exitFocusMode() {
    clearInterval(pomodoroTimer); isTimerRunning = false;
    const overlay = document.getElementById('focus-mode-overlay');
    overlay.classList.add('opacity-0');
    setTimeout(() => { overlay.classList.add('hidden'); overlay.classList.remove('flex'); }, 500);
    changeView(state.currentView);
}

function toggleTimer() {
    const btnIcon = document.querySelector('#timer-toggle-btn i');
    if(isTimerRunning) { clearInterval(pomodoroTimer); btnIcon.className = 'fa-solid fa-play'; }
    else { pomodoroTimer = setInterval(timerTick, 1000); btnIcon.className = 'fa-solid fa-pause'; }
    isTimerRunning = !isTimerRunning;
}

function timerTick() {
    if(timeLeft > 0) { timeLeft--; updateTimerDisplay(); }
    else { clearInterval(pomodoroTimer); isTimerRunning = false; handleSessionEnd(); }
}

function updateTimerDisplay() {
    const mins = Math.floor(timeLeft / 60), secs = timeLeft % 60;
    document.getElementById('timer-display').innerText = `${mins.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`;
    const totalDuration = state.pomodoro[currentPomodoroMode] * 60;
    const offset = 100 - ((timeLeft / totalDuration) * 100);
    document.getElementById('timer-progress').style.strokeDashoffset = offset;
    
    let lbl = document.getElementById('timer-mode-label');
    if(currentPomodoroMode === 'work') { document.getElementById('timer-progress').style.stroke = state.accentColor; lbl.innerText = 'Work'; lbl.className = 'theme-text font-bold mt-2 uppercase tracking-[0.2em] text-sm'; }
    else { document.getElementById('timer-progress').style.stroke = '#22c55e'; lbl.innerText = 'Break'; lbl.className = 'text-green-500 font-bold mt-2 uppercase tracking-[0.2em] text-sm'; }
}

function resetTimer() {
    clearInterval(pomodoroTimer); isTimerRunning = false;
    timeLeft = state.pomodoro[currentPomodoroMode] * 60;
    document.querySelector('#timer-toggle-btn i').className = 'fa-solid fa-play';
    updateTimerDisplay();
}

function handleSessionEnd() {
    if(Notification.permission === "granted") new Notification("Timer Finished!", { body: `Your ${currentPomodoroMode} session is over.` });
    const task = state.tasks.find(t => t.id === activeFocusTaskId);
    document.querySelector('#timer-toggle-btn i').className = 'fa-solid fa-play';
    
    if(currentPomodoroMode === 'work') {
        if(task) task.pomodoros = (task.pomodoros || 0) + 1;
        state.pomodoro.stats.completed++; state.pomodoro.stats.totalWorkMins += state.pomodoro.work; saveState();
        currentPomodoroMode = (state.pomodoro.stats.completed % 4 === 0) ? 'longBreak' : 'shortBreak';
    } else {
        currentPomodoroMode = 'work';
    }
    timeLeft = state.pomodoro[currentPomodoroMode] * 60; updateTimerDisplay();
}

function completeFocusTask() {
    const task = state.tasks.find(t => t.id === activeFocusTaskId);
    if(task) { task.status = 'done'; state.stats.tasksCompleted++; saveState(); exitFocusMode(); }
}

window.onload = init;

</script>
</body>
</html>
