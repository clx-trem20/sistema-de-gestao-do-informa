<html lang="pt">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema de Demandas | Corporativo</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;700;900&display=swap');
        body { font-family: 'Inter', sans-serif; }
        .custom-scrollbar::-webkit-scrollbar { width: 6px; }
        .custom-scrollbar::-webkit-scrollbar-track { background: transparent; }
        .custom-scrollbar::-webkit-scrollbar-thumb { background: #e2e8f0; border-radius: 10px; }
        .modal-backdrop { backdrop-filter: blur(12px); background-color: rgba(15, 23, 42, 0.7); }
    </style>
</head>
<body class="bg-slate-50 text-slate-900 min-h-screen flex flex-col">

    <!-- Tela de Login -->
    <div id="login-screen" class="fixed inset-0 z-[100] bg-slate-950 flex flex-col items-center justify-center p-4">
        <div class="flex-grow flex items-center justify-center w-full">
            <div class="bg-slate-900 border border-slate-800 w-full max-w-md rounded-[2.5rem] p-10 shadow-2xl">
                <div class="flex flex-col items-center mb-8 text-center">
                    <div class="bg-indigo-600 p-5 rounded-3xl mb-4 shadow-lg shadow-indigo-500/20">
                        <i data-lucide="lock" class="text-white w-8 h-8"></i>
                    </div>
                    <h1 class="text-2xl font-black uppercase text-white tracking-tighter">Portal Interno</h1>
                    <p class="text-slate-500 text-sm font-medium">Gestão de Demandas</p>
                </div>
                <form id="login-form" class="space-y-4">
                    <input type="text" id="login-user" required placeholder="UTILIZADOR" class="w-full bg-slate-800 border border-slate-700 rounded-2xl px-6 py-4 outline-none focus:ring-2 focus:ring-indigo-500 font-bold uppercase text-white">
                    <input type="password" id="login-pass" required placeholder="PALAVRA-PASSE" class="w-full bg-slate-800 border border-slate-700 rounded-2xl px-6 py-4 outline-none focus:ring-2 focus:ring-indigo-500 font-bold text-white">
                    <div id="login-error" class="hidden p-3 bg-red-500/10 text-red-400 text-xs font-bold rounded-xl text-center border border-red-500/20"></div>
                    <button type="submit" class="w-full bg-indigo-600 hover:bg-indigo-500 text-white font-black py-5 rounded-2xl transition-all shadow-lg uppercase text-xs tracking-widest">Aceder à Plataforma</button>
                </form>
            </div>
        </div>
        <footer class="w-full py-8 text-center">
            <p class="text-slate-600 font-bold text-[10px] uppercase tracking-[0.2em]">
                © 2025 – Criado por <span class="text-indigo-500">CLX</span>
            </p>
        </footer>
    </div>

    <!-- Layout Principal -->
    <div id="app-screen" class="hidden flex-col min-h-screen">
        <header class="bg-white border-b border-slate-200 sticky top-0 z-30 shadow-sm">
            <div class="max-w-7xl mx-auto px-6 h-20 flex items-center justify-between">
                <div class="flex items-center gap-3 font-black text-2xl text-indigo-700">
                    <div class="bg-indigo-600 p-2 rounded-xl text-white shadow-md shadow-indigo-200">
                        <i data-lucide="layout" class="w-6 h-6"></i>
                    </div>
                    <span class="tracking-tighter uppercase">Demandas</span>
                </div>
                <div class="flex items-center gap-4">
                    <div class="text-right hidden sm:block">
                        <p id="user-info-role" class="text-[10px] uppercase font-black text-indigo-500 leading-none tracking-widest mb-1"></p>
                        <p id="user-info-name" class="text-sm font-bold text-slate-700 uppercase"></p>
                    </div>
                    <button id="btn-admin-panel" class="hidden p-3 bg-slate-100 hover:bg-indigo-600 text-slate-600 hover:text-white rounded-2xl transition-all shadow-sm">
                        <i data-lucide="settings" class="w-5 h-5"></i>
                    </button>
                    <button onclick="handleLogout()" class="p-3 bg-red-50 hover:bg-red-500 text-red-500 hover:text-white rounded-2xl transition-all shadow-sm">
                        <i data-lucide="log-out" class="w-5 h-5"></i>
                    </button>
                </div>
            </div>
        </header>

        <main class="max-w-7xl mx-auto px-6 py-10 flex-grow w-full">
            <div id="management-tools" class="hidden mb-10 p-6 bg-white border border-slate-100 rounded-[2rem] shadow-sm">
                <div class="flex flex-col md:flex-row gap-4 items-center">
                    <div class="flex-1 w-full relative">
                        <i data-lucide="search" class="absolute left-5 top-1/2 -translate-y-1/2 text-slate-400 w-5 h-5"></i>
                        <input type="text" id="search-user-input" placeholder="PESQUISAR COLABORADOR DO SETOR..." class="w-full pl-14 pr-6 py-4 bg-slate-50 border border-slate-100 rounded-2xl font-bold outline-none focus:ring-2 focus:ring-indigo-500 uppercase">
                        <div id="search-results" class="hidden absolute top-full left-0 right-0 mt-2 bg-white border border-slate-200 rounded-2xl shadow-2xl z-40 max-h-60 overflow-y-auto custom-scrollbar"></div>
                    </div>
                    <div id="selected-filter-badge" class="hidden flex items-center gap-3 bg-indigo-600 text-white px-6 py-4 rounded-2xl shadow-lg">
                        <p id="filter-user-name" class="font-black uppercase text-sm"></p>
                        <button onclick="clearUserFilter()" class="p-1 hover:bg-white/20 rounded-lg"><i data-lucide="x" class="w-4 h-4"></i></button>
                    </div>
                </div>
            </div>

            <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center mb-12 gap-6">
                <div>
                    <h2 id="view-title" class="text-4xl font-black text-slate-900 tracking-tight mb-2 uppercase">Painel de Controlo</h2>
                    <p id="view-subtitle" class="text-slate-500 font-medium"></p>
                </div>
                <button id="btn-new-task" class="hidden bg-indigo-600 hover:bg-indigo-700 text-white px-10 py-5 rounded-[2rem] font-black flex items-center gap-3 shadow-2xl w-full sm:w-auto justify-center transition-all text-xs tracking-widest uppercase">
                    <i data-lucide="plus" class="w-5 h-5"></i> Nova Demanda
                </button>
            </div>

            <div id="tasks-container" class="grid grid-cols-1 gap-6"></div>
        </main>

        <footer class="w-full py-8 mt-auto border-t border-slate-200">
            <div class="max-w-7xl mx-auto px-6 flex flex-col items-center justify-center text-center">
                <p class="text-slate-400 font-bold text-[10px] uppercase tracking-[0.2em]">
                    © 2025 – Criado por <span class="text-indigo-600">CLX</span>
                </p>
            </div>
        </footer>
    </div>

    <!-- Modal de Demanda (Nova e Edição) -->
    <div id="modal-task" class="hidden fixed inset-0 z-50 flex items-center justify-center p-6 modal-backdrop">
        <div class="bg-white rounded-[3.5rem] w-full max-w-xl p-12 shadow-2xl relative">
            <h2 id="modal-task-title" class="font-black text-slate-900 uppercase text-xs tracking-[0.3em] mb-10">Nova Demanda</h2>
            <form id="task-form" class="space-y-6">
                <input type="hidden" id="task-id">
                <input type="text" id="task-title" required placeholder="TÍTULO..." class="w-full p-5 border bg-slate-50 rounded-[1.5rem] font-bold uppercase outline-none focus:ring-2 focus:ring-indigo-500">
                <textarea id="task-desc" placeholder="DESCRIÇÃO..." class="w-full p-6 border bg-slate-50 rounded-[1.5rem] h-36 resize-none outline-none focus:ring-2 focus:ring-indigo-500"></textarea>
                <div class="grid grid-cols-2 gap-5">
                    <select id="task-category" class="w-full p-5 border bg-slate-50 rounded-[1.5rem] font-black text-xs uppercase"></select>
                    <select id="task-assigned" required class="w-full p-5 border bg-slate-50 rounded-[1.5rem] font-black text-xs uppercase">
                        <option value="">ATRIBUIR A...</option>
                    </select>
                </div>
                <div class="pt-8">
                    <button type="submit" class="w-full bg-indigo-600 text-white font-black py-6 rounded-[2rem] uppercase text-xs tracking-[0.2em]">Salvar</button>
                    <button type="button" onclick="closeModals()" class="w-full text-slate-300 font-black py-4 text-[10px] uppercase tracking-widest">Fechar</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Modal Admin -->
    <div id="modal-admin" class="hidden fixed inset-0 z-50 flex items-center justify-center p-4 modal-backdrop">
        <div class="bg-white rounded-[3rem] w-full max-w-6xl shadow-2xl overflow-hidden flex flex-col max-h-[90vh]">
            <div class="flex justify-between items-center p-8 bg-slate-50 border-b">
                <h2 class="text-2xl font-black uppercase tracking-tighter">Gestão Master</h2>
                <button onclick="closeModals()" class="bg-slate-200 p-3 rounded-full hover:bg-red-500 hover:text-white transition-all">
                    <i data-lucide="x" class="w-6 h-6"></i>
                </button>
            </div>
            <div class="p-8 overflow-y-auto flex-1 custom-scrollbar">
                <div class="grid grid-cols-1 lg:grid-cols-2 gap-10 mb-12">
                    <div class="p-8 bg-white border rounded-[2.5rem] shadow-sm">
                        <form id="admin-user-form" class="space-y-4">
                            <input type="text" id="new-user-login" required placeholder="NOME" class="w-full p-4 bg-slate-50 border rounded-2xl font-bold uppercase">
                            <input type="text" id="new-user-pass" required placeholder="SENHA" class="w-full p-4 bg-slate-50 border rounded-2xl font-bold">
                            <div class="grid grid-cols-2 gap-4">
                                <select id="new-user-category" class="p-4 bg-slate-50 border rounded-2xl font-bold text-xs uppercase"></select>
                                <select id="new-user-role" class="p-4 bg-slate-50 border rounded-2xl font-bold text-xs uppercase"></select>
                            </div>
                            <button type="submit" class="w-full bg-slate-900 text-white p-4 rounded-2xl font-black uppercase text-[10px]">Criar Utilizador</button>
                        </form>
                    </div>
                    <div class="p-8 bg-indigo-50/50 border border-indigo-100 rounded-[2.5rem]">
                        <textarea id="import-bulk-text" placeholder="IMPORTAR (NOME SENHA SETOR CARGO)" class="w-full h-32 p-4 bg-white border rounded-2xl font-mono text-[10px] mb-4"></textarea>
                        <button onclick="processBulkImport()" class="w-full bg-indigo-600 text-white p-4 rounded-2xl font-black uppercase text-[10px]">Importar em Lote</button>
                    </div>
                </div>
                <div id="admin-users-list" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-5"></div>
            </div>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.1.0/firebase-app.js";
        import { 
            getFirestore, collection, query, onSnapshot, addDoc, updateDoc, 
            deleteDoc, doc, serverTimestamp, setDoc, getDoc, writeBatch 
        } from "https://www.gstatic.com/firebasejs/11.1.0/firebase-firestore.js";
        import { 
            getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged 
        } from "https://www.gstatic.com/firebasejs/11.1.0/firebase-auth.js";

        // CONFIGURAÇÃO ATUALIZADA
        const firebaseConfig = {
            apiKey: "AIzaSyD29RmGmXbB8tcuZXN608eKjlXhr0LKBn0",
            authDomain: "gestor-do-informa.firebaseapp.com",
            projectId: "gestor-do-informa",
            storageBucket: "gestor-do-informa.firebasestorage.app",
            messagingSenderId: "2215345954",
            appId: "1:2215345954:web:3f201a2dda85b15100ef7b"
        };

        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const db = getFirestore(app);
        
        // Identificador único para isolar dados no Firestore (mantenha como desejar)
        const appId = "gestor-informa-demandas";

        const CATEGORIES = ["Meio Ambiente", "Linguagens", "Comunicações", "Edição de Vídeo", "Cultura", "Secretaria", "Esportes", "Presidência", "Informações", "Designer"];
        const ROLES = ["Colaborador", "Estagiário", "Diretor"];
        const MASTER_CONFIG = { login: "CLX", pass: "02072007" };

        let currentUser = null;
        let allTasks = [];
        let allUsers = [];
        let filteredUserLogin = null;

        window.addEventListener('load', () => { initAuth(); populateSelects(); });

        async function initAuth() {
            // Tenta usar token inicial se disponível no ambiente, senão anônimo
            if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
                await signInWithCustomToken(auth, __initial_auth_token);
            } else {
                await signInAnonymously(auth);
            }
        }

        function populateSelects() {
            const catSelects = [document.getElementById('task-category'), document.getElementById('new-user-category')];
            const roleSelects = [document.getElementById('new-user-role')];
            catSelects.forEach(s => {
                s.innerHTML = '';
                CATEGORIES.forEach(c => s.innerHTML += `<option value="${c}">${c.toUpperCase()}</option>`);
            });
            roleSelects.forEach(s => {
                s.innerHTML = '';
                ROLES.forEach(r => s.innerHTML += `<option value="${r}">${r.toUpperCase()}</option>`);
            });
        }

        document.getElementById('login-form').onsubmit = async (e) => {
            e.preventDefault();
            const loginInput = document.getElementById('login-user').value.trim();
            const passInput = document.getElementById('login-pass').value;
            const errBox = document.getElementById('login-error');
            errBox.classList.add('hidden');

            if (loginInput.toUpperCase() === MASTER_CONFIG.login.toUpperCase() && passInput === MASTER_CONFIG.pass) {
                loginSuccess({ login: MASTER_CONFIG.login.toUpperCase(), category: 'MASTER', role: 'Administrador', isMaster: true });
                return;
            }

            try {
                const userRef = doc(db, 'artifacts', appId, 'public', 'data', 'users', loginInput.toLowerCase());
                const userDoc = await getDoc(userRef);
                if (userDoc.exists()) {
                    const data = userDoc.data();
                    if (data.pass === passInput) loginSuccess({ ...data, isMaster: false });
                    else showLoginError('Senha incorreta.');
                } else showLoginError('Utilizador não registado.');
            } catch (err) { showLoginError('Erro de ligação ao servidor.'); }
        };

        function showLoginError(msg) {
            const eb = document.getElementById('login-error');
            eb.textContent = msg; eb.classList.remove('hidden');
        }

        function loginSuccess(userData) {
            currentUser = userData;
            document.getElementById('login-screen').classList.add('hidden');
            document.getElementById('app-screen').classList.remove('hidden');
            document.getElementById('app-screen').classList.add('flex');
            document.getElementById('user-info-role').textContent = `${userData.role} • ${userData.category}`;
            document.getElementById('user-info-name').textContent = userData.login;
            
            const isManager = userData.isMaster || userData.category === 'Presidência' || userData.role === 'Diretor';
            if (isManager) {
                document.getElementById('management-tools').classList.remove('hidden');
                document.getElementById('btn-new-task').classList.remove('hidden');
            }
            if (userData.isMaster) {
                document.getElementById('btn-admin-panel').classList.remove('hidden');
            }

            document.getElementById('view-subtitle').textContent = `Setor: ${userData.category}`;
            startListeners();
        }

        window.handleLogout = () => location.reload();

        function startListeners() {
            onSnapshot(collection(db, 'artifacts', appId, 'public', 'data', 'tasks'), (snap) => {
                allTasks = snap.docs.map(d => ({ id: d.id, ...d.data() }))
                    .sort((a, b) => (b.createdAt?.seconds || 0) - (a.createdAt?.seconds || 0));
                renderTasks();
            });

            onSnapshot(collection(db, 'artifacts', appId, 'public', 'data', 'users'), (snap) => {
                allUsers = snap.docs.map(d => ({ id: d.id, ...d.data() }));
                renderAdminUsers();
                updateTaskAssignedSelect();
            });
        }

        function renderTasks() {
            const container = document.getElementById('tasks-container');
            if (!currentUser) return;
            let filtered = [...allTasks];
            if (filteredUserLogin) {
                filtered = filtered.filter(t => t.assignedTo.toLowerCase() === filteredUserLogin.toLowerCase());
            } else {
                const userCat = currentUser.category.toLowerCase();
                const userLogin = currentUser.login.toLowerCase();
                if (currentUser.isMaster || userCat === 'presidência') { } 
                else if (currentUser.role === 'Diretor') { filtered = filtered.filter(t => t.category.toLowerCase() === userCat); } 
                else { filtered = filtered.filter(t => t.assignedTo.toLowerCase() === userLogin); }
            }
            if (filtered.length === 0) {
                container.innerHTML = `<div class="p-20 text-center opacity-30 font-black uppercase tracking-widest text-xs">Nenhuma demanda encontrada</div>`;
                return;
            }
            container.innerHTML = filtered.map(t => `
                <div class="bg-white border rounded-[2.5rem] p-8 shadow-sm transition-all hover:shadow-md ${t.status === 'concluída' ? 'opacity-60' : ''}">
                    <div class="flex gap-6">
                        <button onclick="toggleStatus('${t.id}', '${t.status}')" class="shrink-0 w-12 h-12 border-2 rounded-2xl flex items-center justify-center transition-all ${t.status === 'concluída' ? 'bg-emerald-500 border-emerald-500 text-white' : 'border-slate-200 hover:border-indigo-400'}">
                            <i data-lucide="check" class="w-6 h-6"></i>
                        </button>
                        <div class="flex-1">
                            <div class="flex justify-between items-start mb-3">
                                <span class="text-[9px] font-black bg-indigo-50 text-indigo-600 px-4 py-1.5 rounded-full uppercase tracking-wider">${t.category} • PARA: ${t.assignedTo}</span>
                                <div class="flex gap-2">
                                    ${canEdit(t) ? `
                                        <button onclick="editTask('${t.id}')" class="text-slate-300 hover:text-indigo-500 transition-colors p-1"><i data-lucide="pencil" class="w-5 h-5"></i></button>
                                        <button onclick="deleteT('${t.id}')" class="text-slate-300 hover:text-red-500 transition-colors p-1"><i data-lucide="trash-2" class="w-5 h-5"></i></button>
                                    ` : ''}
                                </div>
                            </div>
                            <h3 class="font-black text-xl uppercase tracking-tight mb-1 ${t.status === 'concluída' ? 'line-through text-slate-400' : 'text-slate-900'}">${t.title}</h3>
                            <p class="text-slate-500 text-sm mb-6 leading-relaxed">${t.description || 'Sem descrição.'}</p>
                            <div class="bg-slate-50/50 p-5 rounded-[1.5rem] border border-slate-100">
                                <div class="space-y-3 mb-4 max-h-40 overflow-y-auto custom-scrollbar">
                                    ${(t.comments || []).map(c => `
                                        <div class="text-[11px] bg-white p-3 rounded-xl border border-slate-100 shadow-sm">
                                            <div class="flex justify-between mb-1"><b class="text-indigo-600 uppercase font-black">${c.user}</b><span class="text-[8px] text-slate-400 font-bold">${new Date(c.date).toLocaleDateString()}</span></div>
                                            <p class="text-slate-600">${c.text}</p>
                                        </div>`).join('')}
                                </div>
                                <div class="flex gap-2">
                                    <input id="comm-${t.id}" placeholder="Adicionar nota..." class="flex-1 text-xs p-3 rounded-xl border border-slate-200 outline-none focus:ring-2 focus:ring-indigo-500 bg-white">
                                    <button onclick="addComm('${t.id}')" class="bg-slate-900 hover:bg-indigo-600 text-white p-3 rounded-xl transition-colors shadow-sm"><i data-lucide="send" class="w-4 h-4"></i></button>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>`).join('');
            lucide.createIcons();
        }

        function canEdit(t) {
            if (!currentUser) return false;
            if (currentUser.isMaster || currentUser.category.toLowerCase() === 'presidência') return true;
            return currentUser.role === 'Diretor' && t.category.toLowerCase() === currentUser.category.toLowerCase();
        }

        window.editTask = (id) => {
            const task = allTasks.find(t => t.id === id);
            if (!task) return;
            document.getElementById('modal-task-title').textContent = "Editar Demanda";
            document.getElementById('task-id').value = task.id;
            document.getElementById('task-title').value = task.title;
            document.getElementById('task-desc').value = task.description || '';
            document.getElementById('task-category').value = task.category;
            updateTaskAssignedSelect();
            document.getElementById('task-assigned').value = task.assignedTo;
            document.getElementById('modal-task').classList.remove('hidden');
        };

        window.toggleStatus = async (id, s) => {
            const nextStatus = s === 'concluída' ? 'pendente' : 'concluída';
            await updateDoc(doc(db, 'artifacts', appId, 'public', 'data', 'tasks', id), { status: nextStatus });
        };

        window.deleteT = async (id) => { if(confirm("Deseja eliminar esta demanda?")) await deleteDoc(doc(db, 'artifacts', appId, 'public', 'data', 'tasks', id)); };

        window.addComm = async (id) => {
            const input = document.getElementById(`comm-${id}`);
            const text = input.value.trim(); if (!text) return;
            const task = allTasks.find(t => t.id === id);
            const comments = [...(task.comments || []), { user: currentUser.login, text: text, date: new Date().toISOString() }];
            await updateDoc(doc(db, 'artifacts', appId, 'public', 'data', 'tasks', id), { comments });
            input.value = '';
        };

        document.getElementById('btn-new-task').onclick = () => {
            document.getElementById('modal-task-title').textContent = "Nova Demanda";
            document.getElementById('task-form').reset();
            document.getElementById('task-id').value = '';
            const catS = document.getElementById('task-category');
            if (currentUser.role === 'Diretor' && !currentUser.isMaster && currentUser.category.toLowerCase() !== 'presidência') {
                catS.value = currentUser.category; catS.disabled = true;
            } else { catS.disabled = false; catS.value = "Presidência"; }
            updateTaskAssignedSelect();
            document.getElementById('modal-task').classList.remove('hidden');
        };

        document.getElementById('task-form').onsubmit = async (e) => {
            e.preventDefault();
            const id = document.getElementById('task-id').value;
            const data = {
                title: document.getElementById('task-title').value.trim(),
                description: document.getElementById('task-desc').value.trim(),
                category: document.getElementById('task-category').value,
                assignedTo: document.getElementById('task-assigned').value
            };

            if (id) {
                await updateDoc(doc(db, 'artifacts', appId, 'public', 'data', 'tasks', id), data);
            } else {
                await addDoc(collection(db, 'artifacts', appId, 'public', 'data', 'tasks'), {
                    ...data,
                    status: 'pendente',
                    createdAt: serverTimestamp(),
                    comments: []
                });
            }
            closeModals();
        };

        window.closeModals = () => document.querySelectorAll('.modal-backdrop').forEach(m => m.classList.add('hidden'));

        function updateTaskAssignedSelect() {
            const select = document.getElementById('task-assigned');
            const cat = document.getElementById('task-category').value.toLowerCase();
            let filtered = allUsers.filter(u => u.login.toUpperCase() !== MASTER_CONFIG.login.toUpperCase());
            if (!(currentUser.isMaster || currentUser.category.toLowerCase() === 'presidência')) {
                filtered = filtered.filter(u => u.category.toLowerCase() === cat);
            }
            select.innerHTML = '<option value="">ATRIBUIR A...</option>';
            filtered.sort((a,b) => a.login.localeCompare(b.login)).forEach(u => {
                select.innerHTML += `<option value="${u.login}">${u.login.toUpperCase()} (${u.role})</option>`;
            });
        }
        
        document.getElementById('task-category').onchange = updateTaskAssignedSelect;
        document.getElementById('btn-admin-panel').onclick = () => document.getElementById('modal-admin').classList.remove('hidden');
        
        document.getElementById('admin-user-form').onsubmit = async (e) => {
            e.preventDefault();
            const login = document.getElementById('new-user-login').value.trim();
            await setDoc(doc(db, 'artifacts', appId, 'public', 'data', 'users', login.toLowerCase()), { 
                login: login, pass: document.getElementById('new-user-pass').value.trim(), 
                category: document.getElementById('new-user-category').value, role: document.getElementById('new-user-role').value 
            });
            document.getElementById('admin-user-form').reset();
        };

        function renderAdminUsers() {
            document.getElementById('admin-users-list').innerHTML = allUsers.filter(u => u.login.toUpperCase() !== MASTER_CONFIG.login.toUpperCase()).map(u => `
                <div class="p-5 border rounded-[2rem] bg-white flex justify-between items-center shadow-sm">
                    <div><p class="font-black text-xs uppercase text-slate-900">${u.login}</p><p class="text-[9px] text-slate-400 font-bold uppercase tracking-widest">${u.category} • ${u.role}</p></div>
                    <button onclick="delUser('${u.id}')" class="text-slate-200 hover:text-red-500 transition-colors"><i data-lucide="user-minus" class="w-5 h-5"></i></button>
                </div>`).join('');
            lucide.createIcons();
        }

        window.delUser = async (id) => { if(confirm("Remover utilizador?")) await deleteDoc(doc(db, 'artifacts', appId, 'public', 'data', 'users', id)); };
        
        window.processBulkImport = async () => {
            const text = document.getElementById('import-bulk-text').value.trim();
            if (!text) return;
            const batch = writeBatch(db);
            text.split('\n').forEach(line => {
                const p = line.split(/\t/).map(s => s.trim());
                if (p.length >= 2) batch.set(doc(db, 'artifacts', appId, 'public', 'data', 'users', p[0].toLowerCase()), { login: p[0], pass: p[1].replace(/\//g, ''), category: p[2] || 'Secretaria', role: p[3] || 'Colaborador' });
            });
            await batch.commit();
            document.getElementById('import-bulk-text').value = '';
            alert("Sucesso!");
        };

        const si = document.getElementById('search-user-input');
        const sr = document.getElementById('search-results');
        si.oninput = () => {
            const val = si.value.trim().toLowerCase();
            if (!val) { sr.classList.add('hidden'); return; }
            let m = allUsers.filter(u => u.login.toLowerCase().includes(val) && u.login.toUpperCase() !== MASTER_CONFIG.login.toUpperCase());
            if (!currentUser.isMaster && currentUser.category.toLowerCase() !== 'presidência') m = m.filter(u => u.category.toLowerCase() === currentUser.category.toLowerCase());
            if (m.length > 0) {
                sr.innerHTML = m.map(u => `<button onclick="applyF('${u.login}')" class="w-full text-left px-6 py-4 hover:bg-slate-50 border-b transition-colors"><div class="flex justify-between items-center"><span class="font-black text-xs uppercase">${u.login}</span><span class="text-[8px] font-bold text-slate-400 uppercase">${u.role}</span></div></button>`).join('');
                sr.classList.remove('hidden');
            } else { sr.innerHTML = `<div class="p-6 text-center text-[10px] font-bold text-slate-400">NADA ENCONTRADO</div>`; sr.classList.remove('hidden'); }
        };
        window.applyF = (l) => { filteredUserLogin = l; si.value = ''; sr.classList.add('hidden'); document.getElementById('filter-user-name').textContent = l; document.getElementById('selected-filter-badge').classList.remove('hidden'); renderTasks(); };
        window.clearUserFilter = () => { filteredUserLogin = null; document.getElementById('selected-filter-badge').classList.add('hidden'); renderTasks(); };
    </script>
    <script>lucide.createIcons();</script>
</body>
</html>
