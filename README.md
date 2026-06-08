<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <title>Omni</title>
    
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        ios: {
                            bg: '#000000',
                            card: '#1C1C1E',
                            cardHover: '#2C2C2E',
                            blue: '#0A84FF',
                            purple: '#BF5AF2',
                            green: '#30D158',
                            red: '#FF453A',
                            orange: '#FF9F0A',
                            gray: '#8E8E93',
                            border: '#38383A'
                        }
                    },
                    fontFamily: {
                        sans: ['-apple-system', 'BlinkMacSystemFont', '"Segoe UI"', 'Roboto', 'Helvetica', 'Arial', 'sans-serif'],
                    }
                }
            }
        }
    </script>

    <!-- Phosphor Icons -->
    <script src="https://unpkg.com/@phosphor-icons/web"></script>
    
    <!-- SortableJS for Drag & Drop -->
    <script src="https://cdn.jsdelivr.net/npm/sortablejs@latest/Sortable.min.js"></script>
    
    <!-- Marked.js for Markdown Notes -->
    <script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>

    <style>
        :root {
            --sat: env(safe-area-inset-top);
            --sab: env(safe-area-inset-bottom);
        }
        body {
            background-color: #000000;
            color: #ffffff;
            -webkit-tap-highlight-color: transparent;
            -webkit-touch-callout: none;
            overflow: hidden; /* Prevent body scroll, handle in containers */
        }
        .hide-scroll::-webkit-scrollbar {
            display: none;
        }
        .hide-scroll {
            -ms-overflow-style: none;
            scrollbar-width: none;
        }
        .app-container {
            height: 100vh;
            height: -webkit-fill-available;
            display: flex;
            flex-direction: column;
            padding-top: var(--sat);
        }
        .main-content {
            flex: 1;
            overflow-y: auto;
            padding-bottom: calc(80px + var(--sab)); /* Space for bottom nav */
        }
        .bottom-nav {
            padding-bottom: var(--sab);
        }
        
        /* Modals & Animations */
        .modal-enter {
            animation: slideUp 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
        }
        .modal-exit {
            animation: slideDown 0.2s ease-in forwards;
        }
        @keyframes slideUp {
            from { transform: translateY(100%); opacity: 0; }
            to { transform: translateY(0); opacity: 1; }
        }
        @keyframes slideDown {
            from { transform: translateY(0); opacity: 1; }
            to { transform: translateY(100%); opacity: 0; }
        }

        /* Custom Checkbox */
        .task-checkbox {
            width: 22px;
            height: 22px;
            border-radius: 50%;
            border: 2px solid #8E8E93;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: all 0.2s;
        }
        .task-checkbox.completed {
            background-color: #0A84FF;
            border-color: #0A84FF;
        }
        .task-checkbox.completed::after {
            content: '✓';
            color: white;
            font-size: 14px;
            font-weight: bold;
        }

        /* Kanban Ghost */
        .sortable-ghost {
            opacity: 0.4;
            background-color: #2C2C2E;
        }
        
        /* Markdown Styles */
        .prose h1 { font-size: 1.5rem; font-weight: bold; margin-bottom: 0.5rem; }
        .prose h2 { font-size: 1.25rem; font-weight: bold; margin-bottom: 0.5rem; mt-2 }
        .prose p { margin-bottom: 1rem; color: #D1D1D6; }
        .prose ul { list-style-type: disc; padding-left: 1.5rem; margin-bottom: 1rem; }
        .prose ol { list-style-type: decimal; padding-left: 1.5rem; margin-bottom: 1rem; }
        .prose a { color: #0A84FF; }
    </style>
</head>
<body class="antialiased text-white selection:bg-ios-blue selection:bg-opacity-30">

    <div class="app-container">
        
        <!-- Header -->
        <header class="px-5 py-4 flex justify-between items-center bg-ios-bg z-10 sticky top-0 bg-opacity-90 backdrop-blur-md border-b border-ios-border">
            <h1 id="header-title" class="text-2xl font-bold tracking-tight">Today</h1>
            <div id="header-actions" class="flex gap-4">
                <!-- Dynamic actions injected here -->
            </div>
        </header>

        <!-- Main Scrollable Area -->
        <main id="main-content" class="main-content px-5 pt-2 hide-scroll">
            <!-- Content injected via JS -->
        </main>

        <!-- Bottom Navigation -->
        <nav class="bottom-nav fixed bottom-0 left-0 right-0 bg-ios-card bg-opacity-90 backdrop-blur-lg border-t border-ios-border flex justify-around items-center pt-3 pb-2 z-40">
            <button onclick="App.navigate('today')" class="nav-btn flex flex-col items-center text-ios-blue w-16" data-target="today">
                <i class="ph-fill ph-calendar-star text-2xl mb-1"></i>
                <span class="text-[10px] font-medium">Today</span>
            </button>
            <button onclick="App.navigate('projects')" class="nav-btn flex flex-col items-center text-ios-gray w-16" data-target="projects">
                <i class="ph ph-folder-open text-2xl mb-1"></i>
                <span class="text-[10px] font-medium">Projects</span>
            </button>
            
            <!-- FAB Container -->
            <div class="relative -top-6 w-16 flex justify-center">
                <button onclick="App.openOmni()" class="bg-ios-blue text-white rounded-full w-14 h-14 flex items-center justify-center shadow-[0_4px_14px_rgba(10,132,255,0.4)] transform active:scale-95 transition-transform">
                    <i class="ph ph-plus text-3xl"></i>
                </button>
            </div>
            
            <button onclick="App.navigate('habits')" class="nav-btn flex flex-col items-center text-ios-gray w-16" data-target="habits">
                <i class="ph ph-check-circle text-2xl mb-1"></i>
                <span class="text-[10px] font-medium">Habits</span>
            </button>
            <button onclick="App.navigate('notes')" class="nav-btn flex flex-col items-center text-ios-gray w-16" data-target="notes">
                <i class="ph ph-notebook text-2xl mb-1"></i>
                <span class="text-[10px] font-medium">Notes</span>
            </button>
        </nav>

        <!-- Omni Input Modal (Slide Up) -->
        <div id="omni-modal" class="fixed inset-0 z-50 hidden">
            <div class="absolute inset-0 bg-black bg-opacity-50" onclick="App.closeOmni()"></div>
            <div class="absolute bottom-0 left-0 right-0 bg-ios-card rounded-t-2xl pb-[calc(1rem+var(--sab))] pt-5 px-5 modal-enter shadow-2xl border-t border-ios-border">
                <div class="flex justify-between items-center mb-4">
                    <h3 class="text-sm font-semibold text-ios-gray uppercase tracking-wider">Quick Add</h3>
                    <button onclick="App.closeOmni()" class="text-ios-gray hover:text-white"><i class="ph ph-x text-xl"></i></button>
                </div>
                
                <input type="text" id="omni-input" placeholder="e.g., Read book #Personal" 
                    class="w-full bg-ios-bg border border-ios-border rounded-xl px-4 py-4 text-white text-lg focus:outline-none focus:border-ios-blue focus:ring-1 focus:ring-ios-blue transition-all"
                    onkeypress="if(event.key === 'Enter') App.handleOmniSubmit()">
                
                <div class="flex gap-2 mt-4 overflow-x-auto hide-scroll pb-2">
                    <button class="bg-ios-bg px-3 py-1.5 rounded-lg text-xs font-medium text-ios-gray whitespace-nowrap border border-ios-border" onclick="document.getElementById('omni-input').value += ' #'">+ Project</button>
                    <button class="bg-ios-bg px-3 py-1.5 rounded-lg text-xs font-medium text-ios-gray whitespace-nowrap border border-ios-border" onclick="App.openNoteEditor()">📝 New Note</button>
                    <button class="bg-ios-bg px-3 py-1.5 rounded-lg text-xs font-medium text-ios-gray whitespace-nowrap border border-ios-border" onclick="App.createHabitPrompt()">🔄 New Habit</button>
                </div>
            </div>
        </div>

        <!-- Note Editor Modal (Full Screen) -->
        <div id="note-modal" class="fixed inset-0 z-50 bg-ios-bg hidden flex-col pb-[var(--sab)] pt-[var(--sat)]">
            <div class="flex justify-between items-center px-5 py-4 border-b border-ios-border">
                <button onclick="App.closeNoteEditor()" class="text-ios-blue text-lg">Cancel</button>
                <span class="font-semibold text-sm text-ios-gray">Note Editor</span>
                <button onclick="App.saveNote()" class="text-ios-blue font-semibold text-lg">Save</button>
            </div>
            <input type="text" id="note-title" placeholder="Note Title" class="w-full bg-transparent px-5 py-4 text-2xl font-bold focus:outline-none placeholder-ios-border border-b border-ios-border">
            <textarea id="note-content" placeholder="Write something in Markdown..." class="flex-1 w-full bg-transparent px-5 py-4 text-lg focus:outline-none resize-none hide-scroll placeholder-ios-border font-mono text-gray-300"></textarea>
        </div>

        <!-- Note Viewer Modal -->
        <div id="note-viewer" class="fixed inset-0 z-50 bg-ios-bg hidden flex-col pb-[var(--sab)] pt-[var(--sat)]">
            <div class="flex justify-between items-center px-5 py-4 border-b border-ios-border bg-ios-bg bg-opacity-90 backdrop-blur sticky top-0">
                <button onclick="App.closeNoteViewer()" class="text-ios-blue flex items-center"><i class="ph ph-caret-left text-2xl mr-1"></i> Back</button>
                <div class="flex gap-4">
                    <button onclick="App.editCurrentNote()" class="text-ios-blue"><i class="ph ph-pencil-simple text-xl"></i></button>
                    <button onclick="App.deleteCurrentNote()" class="text-ios-red"><i class="ph ph-trash text-xl"></i></button>
                </div>
            </div>
            <div class="px-5 py-6 overflow-y-auto hide-scroll flex-1">
                <h1 id="viewer-title" class="text-3xl font-bold mb-6"></h1>
                <div id="viewer-content" class="prose prose-invert"></div>
            </div>
        </div>

    </div>

    <script>
        // --- State Management ---
        const getTodayDate = () => new Date().toISOString().split('T')[0];
        
        const defaultState = {
            tasks: [
                { id: 't1', title: 'Welcome to Omni!', projectId: 'inbox', status: 'todo', date: getTodayDate(), completed: false },
                { id: 't2', title: 'Try dragging this task', projectId: 'p1', status: 'todo', date: null, completed: false },
                { id: 't3', title: 'Review mockups', projectId: 'p1', status: 'in_progress', date: null, completed: false }
            ],
            projects: [
                { id: 'inbox', name: 'Inbox', view: 'list', isSystem: true },
                { id: 'p1', name: 'Work', view: 'board', isSystem: false }
            ],
            notes: [
                { id: 'n1', title: 'Meeting Notes', content: '## Q3 Planning\n- Discuss marketing budget.\n- **Launch date:** Oct 15th.', date: getTodayDate() }
            ],
            habits: [
                { id: 'h1', name: 'Read 10 pages', streak: 2, logs: { [getTodayDate()]: false } }
            ],
            view: { current: 'today', payload: null } // payload for specific project ID
        };

        const App = {
            state: JSON.parse(localStorage.getItem('omni_data')) || defaultState,
            sortableInstances: [],
            currentNoteId: null,

            save() {
                localStorage.setItem('omni_data', JSON.stringify(this.state));
            },

            // --- Navigation & Rendering ---
            navigate(view, payload = null) {
                this.state.view = { current: view, payload };
                this.save();
                this.updateNavUI();
                this.render();
            },

            updateNavUI() {
                const view = this.state.view.current;
                document.querySelectorAll('.nav-btn').forEach(btn => {
                    const target = btn.dataset.target;
                    const icon = btn.querySelector('i');
                    if (target === view || (target === 'projects' && view === 'project')) {
                        btn.classList.replace('text-ios-gray', 'text-ios-blue');
                        icon.classList.replace('ph', 'ph-fill');
                    } else {
                        btn.classList.replace('text-ios-blue', 'text-ios-gray');
                        icon.classList.replace('ph-fill', 'ph');
                    }
                });
            },

            render() {
                const { current, payload } = this.state.view;
                const main = document.getElementById('main-content');
                const title = document.getElementById('header-title');
                const actions = document.getElementById('header-actions');
                
                // Cleanup sortables
                this.sortableInstances.forEach(s => s.destroy());
                this.sortableInstances = [];
                main.innerHTML = '';
                actions.innerHTML = '';

                switch (current) {
                    case 'today':
                        title.innerText = 'Today';
                        this.renderTaskList(main, this.getTasksForToday());
                        break;
                    case 'projects':
                        title.innerText = 'Projects';
                        actions.innerHTML = `<button onclick="App.createProject()" class="text-ios-blue"><i class="ph ph-plus text-xl"></i></button>`;
                        this.renderProjectList(main);
                        break;
                    case 'project':
                        const project = this.state.projects.find(p => p.id === payload);
                        if (!project) return this.navigate('projects');
                        title.innerText = project.name;
                        
                        if (!project.isSystem) {
                            actions.innerHTML = `
                                <button onclick="App.toggleProjectView('${project.id}')" class="text-ios-blue text-xl"><i class="ph ${project.view === 'board' ? 'ph-kanban' : 'ph-list'}"></i></button>
                                <button onclick="App.deleteProject('${project.id}')" class="text-ios-red text-xl"><i class="ph ph-trash"></i></button>
                            `;
                        }
                        
                        if (project.view === 'board' && !project.isSystem) {
                            this.renderBoard(main, project.id);
                        } else {
                            this.renderTaskList(main, this.getTasksForProject(project.id));
                        }
                        break;
                    case 'habits':
                        title.innerText = 'Habits';
                        actions.innerHTML = `<button onclick="App.createHabitPrompt()" class="text-ios-blue"><i class="ph ph-plus text-xl"></i></button>`;
                        this.renderHabits(main);
                        break;
                    case 'notes':
                        title.innerText = 'Notes';
                        actions.innerHTML = `<button onclick="App.openNoteEditor()" class="text-ios-blue"><i class="ph ph-plus text-xl"></i></button>`;
                        this.renderNotes(main);
                        break;
                }
            },

            // --- Renderers ---
            renderTaskList(container, tasks) {
                if (tasks.length === 0) {
                    container.innerHTML = `<div class="text-center text-ios-gray mt-20"><i class="ph ph-check-circle text-6xl mb-4 opacity-50"></i><p>All clear!</p></div>`;
                    return;
                }

                const list = document.createElement('div');
                list.className = 'space-y-2 pb-6';
                list.id = 'task-list';

                tasks.forEach(task => {
                    const item = document.createElement('div');
                    item.className = `flex items-center bg-ios-card p-4 rounded-xl active:bg-ios-cardHover transition-colors ${task.completed ? 'opacity-50' : ''}`;
                    item.dataset.id = task.id;
                    
                    const proj = this.state.projects.find(p => p.id === task.projectId);
                    const projTag = proj && proj.id !== 'inbox' ? `<span class="text-[10px] bg-ios-border text-ios-gray px-2 py-0.5 rounded-md ml-2">${proj.name}</span>` : '';

                    item.innerHTML = `
                        <div class="task-checkbox mr-4 ${task.completed ? 'completed' : ''}" onclick="App.toggleTask('${task.id}')"></div>
                        <div class="flex-1 min-w-0" onclick="App.editTaskPrompt('${task.id}')">
                            <p class="text-white truncate ${task.completed ? 'line-through text-ios-gray' : ''}">${task.title}</p>
                            ${projTag}
                        </div>
                        <button onclick="App.deleteTask('${task.id}')" class="text-ios-gray hover:text-ios-red p-2 -mr-2"><i class="ph ph-trash"></i></button>
                    `;
                    list.appendChild(item);
                });

                container.appendChild(list);
            },

            renderProjectList(container) {
                const list = document.createElement('div');
                list.className = 'space-y-3';
                
                this.state.projects.forEach(p => {
                    const taskCount = this.getTasksForProject(p.id).filter(t => !t.completed).length;
                    const el = document.createElement('div');
                    el.className = 'flex items-center justify-between bg-ios-card p-4 rounded-xl active:bg-ios-cardHover';
                    el.onclick = () => this.navigate('project', p.id);
                    el.innerHTML = `
                        <div class="flex items-center gap-3">
                            <i class="ph ${p.id === 'inbox' ? 'ph-tray text-ios-blue' : 'ph-folder-notch text-ios-orange'} text-2xl"></i>
                            <span class="font-medium text-lg">${p.name}</span>
                        </div>
                        <span class="bg-ios-bg text-ios-gray text-xs px-2 py-1 rounded-full">${taskCount}</span>
                    `;
                    list.appendChild(el);
                });
                container.appendChild(list);
            },

            renderBoard(container, projectId) {
                const board = document.createElement('div');
                board.className = 'flex gap-4 h-full overflow-x-auto hide-scroll pb-4 -mx-5 px-5 snap-x';
                
                const columns = [
                    { id: 'todo', title: 'To Do', color: 'ios-gray' },
                    { id: 'in_progress', title: 'In Progress', color: 'ios-blue' },
                    { id: 'done', title: 'Done', color: 'ios-green' }
                ];

                const tasks = this.getTasksForProject(projectId);

                columns.forEach(col => {
                    const colTasks = tasks.filter(t => (t.status || 'todo') === col.id);
                    const colEl = document.createElement('div');
                    colEl.className = 'flex-shrink-0 w-72 flex flex-col bg-ios-card bg-opacity-50 rounded-2xl p-3 snap-center';
                    colEl.innerHTML = `
                        <h3 class="font-bold text-sm text-${col.color} mb-3 flex justify-between">
                            ${col.title} <span class="bg-ios-bg px-2 rounded-full text-xs text-white">${colTasks.length}</span>
                        </h3>
                        <div class="flex-1 overflow-y-auto hide-scroll space-y-2 min-h-[100px]" id="board-${col.id}" data-status="${col.id}">
                            ${colTasks.map(task => `
                                <div class="bg-ios-bg p-3 rounded-xl border border-ios-border cursor-grab active:cursor-grabbing shadow-sm" data-id="${task.id}">
                                    <p class="text-sm ${task.completed ? 'line-through text-ios-gray' : 'text-white'}">${task.title}</p>
                                </div>
                            `).join('')}
                        </div>
                    `;
                    board.appendChild(colEl);
                });

                container.appendChild(board);

                // Initialize SortableJS for Kanban
                setTimeout(() => {
                    columns.forEach(col => {
                        const el = document.getElementById(`board-${col.id}`);
                        if (el) {
                            const sortable = new Sortable(el, {
                                group: 'kanban',
                                animation: 150,
                                delay: 100, // Touch friendly
                                delayOnTouchOnly: true,
                                ghostClass: 'sortable-ghost',
                                onEnd: (evt) => {
                                    const taskId = evt.item.dataset.id;
                                    const newStatus = evt.to.dataset.status;
                                    const task = this.state.tasks.find(t => t.id === taskId);
                                    if (task && task.status !== newStatus) {
                                        task.status = newStatus;
                                        if (newStatus === 'done') task.completed = true;
                                        else if (task.completed) task.completed = false;
                                        this.save();
                                        this.render(); // Re-render to update counts
                                    }
                                }
                            });
                            this.sortableInstances.push(sortable);
                        }
                    });
                }, 0);
            },

            renderHabits(container) {
                const today = getTodayDate();
                const list = document.createElement('div');
                list.className = 'space-y-3';

                this.state.habits.forEach(habit => {
                    const isDoneToday = habit.logs[today] === true;
                    
                    const el = document.createElement('div');
                    el.className = 'flex items-center justify-between bg-ios-card p-4 rounded-xl';
                    el.innerHTML = `
                        <div class="flex items-center gap-4 flex-1">
                            <button onclick="App.toggleHabit('${habit.id}')" class="w-12 h-12 rounded-full flex items-center justify-center transition-all ${isDoneToday ? 'bg-ios-green text-white shadow-[0_0_15px_rgba(48,209,88,0.5)]' : 'bg-ios-bg border-2 border-ios-border text-ios-gray'}">
                                <i class="ph-bold ph-${isDoneToday ? 'check' : 'plus'} text-xl"></i>
                            </button>
                            <div>
                                <h3 class="font-semibold text-lg">${habit.name}</h3>
                                <p class="text-xs text-ios-gray flex items-center gap-1"><i class="ph-fill ph-fire text-ios-orange"></i> ${habit.streak} day streak</p>
                            </div>
                        </div>
                        <button onclick="App.deleteHabit('${habit.id}')" class="text-ios-gray hover:text-ios-red p-2"><i class="ph ph-trash"></i></button>
                    `;
                    list.appendChild(el);
                });
                
                if(this.state.habits.length === 0) {
                     container.innerHTML = `<div class="text-center text-ios-gray mt-20"><i class="ph ph-plant text-6xl mb-4 opacity-50"></i><p>Build good habits</p></div>`;
                     return;
                }

                container.appendChild(list);
            },

            renderNotes(container) {
                const grid = document.createElement('div');
                grid.className = 'grid grid-cols-2 gap-4 pb-6';

                this.state.notes.forEach(note => {
                    const el = document.createElement('div');
                    el.className = 'bg-ios-card p-4 rounded-2xl aspect-square flex flex-col active:scale-95 transition-transform border border-ios-border shadow-sm';
                    el.onclick = () => this.openNoteViewer(note.id);
                    
                    // Simple preview extraction
                    const preview = note.content.replace(/[#*_>]/g, '').substring(0, 50) + '...';
                    
                    el.innerHTML = `
                        <h3 class="font-bold text-lg mb-2 line-clamp-2">${note.title || 'Untitled'}</h3>
                        <p class="text-xs text-ios-gray flex-1 overflow-hidden line-clamp-4 leading-relaxed">${preview}</p>
                        <p class="text-[10px] text-ios-gray mt-2 opacity-50">${note.date}</p>
                    `;
                    grid.appendChild(el);
                });
                
                if(this.state.notes.length === 0) {
                     container.innerHTML = `<div class="text-center text-ios-gray mt-20"><i class="ph ph-notebook text-6xl mb-4 opacity-50"></i><p>No notes yet</p></div>`;
                     return;
                }

                container.appendChild(grid);
            },

            // --- Logic & Actions ---
            getTasksForToday() {
                const today = getTodayDate();
                return this.state.tasks.filter(t => t.date === today || t.date === null); 
            },
            
            getTasksForProject(projectId) {
                return this.state.tasks.filter(t => t.projectId === projectId);
            },

            toggleTask(id) {
                const task = this.state.tasks.find(t => t.id === id);
                if (task) {
                    task.completed = !task.completed;
                    if(task.completed && task.status) task.status = 'done';
                    if(!task.completed && task.status === 'done') task.status = 'todo';
                    this.save();
                    this.render();
                }
            },

            deleteTask(id) {
                this.state.tasks = this.state.tasks.filter(t => t.id !== id);
                this.save();
                this.render();
            },

            editTaskPrompt(id) {
                const task = this.state.tasks.find(t => t.id === id);
                if(!task) return;
                const newTitle = prompt("Edit task:", task.title);
                if(newTitle && newTitle.trim()) {
                    task.title = newTitle.trim();
                    this.save();
                    this.render();
                }
            },

            toggleProjectView(id) {
                const proj = this.state.projects.find(p => p.id === id);
                if (proj) {
                    proj.view = proj.view === 'list' ? 'board' : 'list';
                    this.save();
                    this.render();
                }
            },

            createProject() {
                const name = prompt("Project Name:");
                if (name && name.trim()) {
                    const newProj = { id: 'p_' + Date.now(), name: name.trim(), view: 'list', isSystem: false };
                    this.state.projects.push(newProj);
                    this.save();
                    this.navigate('project', newProj.id);
                }
            },
            
            deleteProject(id) {
                if(confirm("Delete project and all its tasks?")) {
                    this.state.projects = this.state.projects.filter(p => p.id !== id);
                    this.state.tasks = this.state.tasks.filter(t => t.projectId !== id);
                    this.save();
                    this.navigate('projects');
                }
            },

            toggleHabit(id) {
                const habit = this.state.habits.find(h => h.id === id);
                const today = getTodayDate();
                if (habit) {
                    if (habit.logs[today]) {
                        habit.logs[today] = false;
                        habit.streak = Math.max(0, habit.streak - 1);
                    } else {
                        habit.logs[today] = true;
                        habit.streak += 1;
                    }
                    this.save();
                    this.render();
                }
            },

            createHabitPrompt() {
                this.closeOmni();
                const name = prompt("What habit do you want to build?");
                if (name && name.trim()) {
                    this.state.habits.push({ id: 'h_' + Date.now(), name: name.trim(), streak: 0, logs: {} });
                    this.save();
                    if(this.state.view.current !== 'habits') this.navigate('habits');
                    else this.render();
                }
            },
            
            deleteHabit(id) {
                if(confirm("Delete this habit?")) {
                    this.state.habits = this.state.habits.filter(h => h.id !== id);
                    this.save();
                    this.render();
                }
            },

            // --- Omni Input (Natural Language) ---
            openOmni() {
                const modal = document.getElementById('omni-modal');
                modal.classList.remove('hidden');
                modal.classList.remove('modal-exit');
                setTimeout(() => document.getElementById('omni-input').focus(), 100);
            },
            closeOmni() {
                const modal = document.getElementById('omni-modal');
                const content = modal.querySelector('.modal-enter');
                content.classList.replace('modal-enter', 'modal-exit');
                setTimeout(() => {
                    modal.classList.add('hidden');
                    content.classList.replace('modal-exit', 'modal-enter');
                    document.getElementById('omni-input').value = '';
                }, 200);
            },
            handleOmniSubmit() {
                const input = document.getElementById('omni-input');
                let text = input.value.trim();
                if (!text) return;

                // Natural Language Parsing
                let targetProjectId = 'inbox';
                let targetDate = getTodayDate();

                // Extract Project via #
                const projectMatch = text.match(/#(\w+)/);
                if (projectMatch) {
                    const pName = projectMatch[1].toLowerCase();
                    const p = this.state.projects.find(proj => proj.name.toLowerCase() === pName);
                    if (p) {
                        targetProjectId = p.id;
                        text = text.replace(projectMatch[0], '').trim();
                    } else {
                        // Create project on the fly if it doesn't exist
                        const newPId = 'p_' + Date.now();
                        this.state.projects.push({ id: newPId, name: projectMatch[1], view: 'list', isSystem: false });
                        targetProjectId = newPId;
                        text = text.replace(projectMatch[0], '').trim();
                    }
                }

                this.state.tasks.push({
                    id: 't_' + Date.now(),
                    title: text,
                    projectId: targetProjectId,
                    status: 'todo',
                    date: targetDate,
                    completed: false
                });

                this.save();
                this.closeOmni();
                
                // Navigate to where it was added if not there
                if (this.state.view.current !== 'today' && targetProjectId === 'inbox') {
                    this.navigate('today');
                } else if (targetProjectId !== 'inbox') {
                    this.navigate('project', targetProjectId);
                } else {
                    this.render();
                }
            },

            // --- Notes Management ---
            openNoteEditor(noteId = null) {
                this.closeOmni();
                const modal = document.getElementById('note-modal');
                const titleInput = document.getElementById('note-title');
                const contentInput = document.getElementById('note-content');
                
                if (noteId) {
                    const note = this.state.notes.find(n => n.id === noteId);
                    this.currentNoteId = noteId;
                    titleInput.value = note.title;
                    contentInput.value = note.content;
                } else {
                    this.currentNoteId = null;
                    titleInput.value = '';
                    contentInput.value = '';
                }
                
                modal.classList.remove('hidden');
                modal.classList.add('flex');
                if(!noteId) setTimeout(() => titleInput.focus(), 100);
            },
            
            closeNoteEditor() {
                const modal = document.getElementById('note-modal');
                modal.classList.add('hidden');
                modal.classList.remove('flex');
            },

            saveNote() {
                const title = document.getElementById('note-title').value.trim() || 'Untitled';
                const content = document.getElementById('note-content').value.trim();
                
                if (this.currentNoteId) {
                    const note = this.state.notes.find(n => n.id === this.currentNoteId);
                    note.title = title;
                    note.content = content;
                } else {
                    if (content || title !== 'Untitled') {
                        this.state.notes.unshift({
                            id: 'n_' + Date.now(),
                            title,
                            content,
                            date: getTodayDate()
                        });
                    }
                }
                
                this.save();
                this.closeNoteEditor();
                
                // Re-render notes view or update viewer
                if (this.state.view.current === 'notes') {
                     this.render();
                }
                if (this.currentNoteId && !document.getElementById('note-viewer').classList.contains('hidden')) {
                     this.openNoteViewer(this.currentNoteId); // refresh viewer
                }
            },

            openNoteViewer(id) {
                const note = this.state.notes.find(n => n.id === id);
                if (!note) return;
                
                this.currentNoteId = id;
                document.getElementById('viewer-title').innerText = note.title;
                document.getElementById('viewer-content').innerHTML = marked.parse(note.content || '*Empty note*');
                
                const viewer = document.getElementById('note-viewer');
                viewer.classList.remove('hidden');
                viewer.classList.add('flex');
            },
            
            closeNoteViewer() {
                const viewer = document.getElementById('note-viewer');
                viewer.classList.add('hidden');
                viewer.classList.remove('flex');
                this.currentNoteId = null;
            },
            
            editCurrentNote() {
                if(this.currentNoteId) {
                    this.openNoteEditor(this.currentNoteId);
                }
            },
            
            deleteCurrentNote() {
                if(confirm("Delete this note?")) {
                    this.state.notes = this.state.notes.filter(n => n.id !== this.currentNoteId);
                    this.save();
                    this.closeNoteViewer();
                    if (this.state.view.current === 'notes') this.render();
                }
            }
        };

        // Initialize App
        window.onload = () => {
            App.updateNavUI();
            App.render();
            
            // Handle iOS safe area issues with inputs
            document.querySelectorAll('input, textarea').forEach(el => {
                el.addEventListener('focus', () => {
                    document.body.style.position = 'fixed';
                    document.body.style.width = '100%';
                });
                el.addEventListener('blur', () => {
                    document.body.style.position = '';
                    document.body.style.width = '';
                });
            });
        };
    </script>
</body>
</html>
