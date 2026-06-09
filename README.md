<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>TickTick Clone</title>
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
    <style>
        body { -webkit-tap-highlight-color: transparent; }
    </style>
</head>
<body class="bg-gray-900 text-gray-100 font-sans flex flex-col h-screen overflow-hidden">

    <header class="bg-gray-800 border-b border-gray-700 p-4 flex justify-between items-center shrink-0">
        <h1 class="text-xl font-bold flex items-center gap-2 text-blue-400">
            <span class="material-icons">check_circle</span> TickClone
        </h1>
        <div class="flex gap-4">
            <button onclick="switchTab('tasks')" class="hover:text-blue-400 flex items-center"><span class="material-icons">list</span></button>
            <button onclick="switchTab('pomo')" class="hover:text-blue-400 flex items-center"><span class="material-icons">timer</span></button>
            <button onclick="switchTab('matrix')" class="hover:text-blue-400 flex items-center"><span class="material-icons">grid_view</span></button>
        </div>
    </header>

    <main class="flex-1 overflow-y-auto p-4 pb-24">
        
        <section id="tab-tasks" class="tab-content">
            <div class="flex gap-2 mb-4">
                <input type="text" id="task-input" placeholder="Add a task..." class="flex-1 bg-gray-800 border border-gray-700 rounded p-2 focus:outline-none focus:border-blue-500">
                <button onclick="addTask()" class="bg-blue-500 hover:bg-blue-600 text-white px-4 rounded flex items-center"><span class="material-icons">add</span></button>
            </div>
            <ul id="task-list" class="space-y-2"></ul>
        </section>

        <section id="tab-pomo" class="tab-content hidden text-center py-10">
            <div class="text-6xl font-mono mb-6" id="timer-display">25:00</div>
            <div class="flex justify-center gap-4">
                <button id="pomo-start" onclick="toggleTimer()" class="bg-green-600 hover:bg-green-700 px-6 py-2 rounded font-semibold">Start</button>
                <button onclick="resetTimer()" class="bg-gray-700 hover:bg-gray-600 px-6 py-2 rounded font-semibold">Reset</button>
            </div>
        </section>

        <section id="tab-matrix" class="tab-content hidden grid grid-cols-2 gap-2 h-full min-h-[400px]">
            <div class="bg-red-950/40 border border-red-900 p-2 rounded flex flex-col">
                <span class="text-xs font-bold text-red-400 uppercase mb-2">Urgent & Important</span>
                <ul id="matrix-q1" class="text-sm space-y-1 overflow-y-auto flex-1"></ul>
            </div>
            <div class="bg-blue-950/40 border border-blue-900 p-2 rounded flex flex-col">
                <span class="text-xs font-bold text-blue-400 uppercase mb-2">Important, Not Urgent</span>
                <ul id="matrix-q2" class="text-sm space-y-1 overflow-y-auto flex-1"></ul>
            </div>
            <div class="bg-orange-950/40 border border-orange-900 p-2 rounded flex flex-col">
                <span class="text-xs font-bold text-orange-400 uppercase mb-2">Urgent, Not Important</span>
                <ul id="matrix-q3" class="text-sm space-y-1 overflow-y-auto flex-1"></ul>
            </div>
            <div class="bg-gray-800/60 border border-gray-700 p-2 rounded flex flex-col">
                <span class="text-xs font-bold text-gray-400 uppercase mb-2">Neither</span>
                <ul id="matrix-q4" class="text-sm space-y-1 overflow-y-auto flex-1"></ul>
            </div>
        </section>

    </main>

    <script>
        let tasks = JSON.parse(localStorage.getItem('tasks')) || [];
        let timerInterval;
        let timeLeft = 1500; // 25 minutes
        let isRunning = false;

        // Core Task Functions
        function saveAndRender() {
            localStorage.setItem('tasks', JSON.stringify(tasks));
            renderTasks();
            renderMatrix();
        }

        function addTask() {
            const input = document.getElementById('task-input');
            if (!input.value.trim()) return;
            
            tasks.push({
                id: Date.now(),
                text: input.value,
                completed: false,
                matrix: 'q4' // Default quadrant
            });
            input.value = '';
            saveAndRender();
        }

        function toggleTask(id) {
            tasks = tasks.map(t => t.id === id ? {...t, completed: !t.completed} : t);
            saveAndRender();
        }

        function deleteTask(id) {
            tasks = tasks.filter(t => t.id !== id);
            saveAndRender();
        }

        function updateMatrix(id, quadrant) {
            tasks = tasks.map(t => t.id === id ? {...t, matrix: quadrant} : t);
            saveAndRender();
        }

        function renderTasks() {
            const list = document.getElementById('task-list');
            list.innerHTML = tasks.map(t => `
                <li class="bg-gray-800 border border-gray-700 p-3 rounded flex justify-between items-center gap-2">
                    <div class="flex items-center gap-3 flex-1">
                        <input type="checkbox" ${t.completed ? 'checked' : ''} onchange="toggleTask(${t.id})" class="w-5 h-5 accent-blue-500">
                        <span class="${t.completed ? 'line-through text-gray-500' : ''}">${t.text}</span>
                    </div>
                    <select onchange="updateMatrix(${t.id}, this.value)" class="bg-gray-700 text-xs rounded p-1 focus:outline-none">
                        <option value="q1" ${t.matrix === 'q1' ? 'selected' : ''}>Q1</option>
                        <option value="q2" ${t.matrix === 'q2' ? 'selected' : ''}>Q2</option>
                        <option value="q3" ${t.matrix === 'q3' ? 'selected' : ''}>Q3</option>
                        <option value="q4" ${t.matrix === 'q4' ? 'selected' : ''}>Q4</option>
                    </select>
                    <button onclick="deleteTask(${t.id})" class="text-gray-500 hover:text-red-400 flex items-center"><span class="material-icons text-sm">delete</span></button>
                </li>
            `).join('');
        }

        function renderMatrix() {
            ['q1', 'q2', 'q3', 'q4'].forEach(q => {
                const el = document.getElementById(`matrix-${q}`);
                el.innerHTML = tasks.filter(t => t.matrix === q).map(t => `
                    <li class="${t.completed ? 'line-through text-gray-500' : ''} bg-gray-900/50 p-1 rounded border border-gray-800/50">${t.text}</li>
                `).join('');
            });
        }

        // Navigation Tab Switcher
        function switchTab(tabName) {
            document.querySelectorAll('.tab-content').forEach(el => el.classList.add('hidden'));
            document.getElementById(`tab-${tabName}`).classList.remove('hidden');
        }

        // Pomodoro Timer Functions
        function updateTimerDisplay() {
            const mins = Math.floor(timeLeft / 60).toString().padStart(2, '0');
            const secs = (timeLeft % 60).toString().padStart(2, '0');
            document.getElementById('timer-display').innerText = `${mins}:${secs}`;
        }

        function toggleTimer() {
            const btn = document.getElementById('pomo-start');
            if (isRunning) {
                clearInterval(timerInterval);
                btn.innerText = "Start";
                btn.className = "bg-green-600 hover:bg-green-700 px-6 py-2 rounded font-semibold";
            } else {
                timerInterval = setInterval(() => {
                    if (timeLeft > 0) {
                        timeLeft--;
                        updateTimerDisplay();
                    } else {
                        clearInterval(timerInterval);
                        alert("Time's up!");
                        resetTimer();
                    }
                }, 1000);
                btn.innerText = "Pause";
                btn.className = "bg-yellow-600 hover:bg-yellow-700 px-6 py-2 rounded font-semibold";
            }
            isRunning = !isRunning;
        }

        function resetTimer() {
            clearInterval(timerInterval);
            isRunning = false;
            timeLeft = 1500;
            updateTimerDisplay();
            const btn = document.getElementById('pomo-start');
            btn.innerText = "Start";
            btn.className = "bg-green-600 hover:bg-green-700 px-6 py-2 rounded font-semibold";
        }

        // Initialize on load
        saveAndRender();
    </script>
</body>
</html>
