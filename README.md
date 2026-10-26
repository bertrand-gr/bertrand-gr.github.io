<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Patte &amp; Tatras</title>
    <script src="https://unpkg.com/vue@3/dist/vue.global.prod.js"></script>
    <style>
        :root {
            --bg: #0f1020;
            --card: #1a1b33;
            --text: #eef0ff;
            --muted: #9a9dc4;
            --patte: #ff6b9d;
            --tatras: #4ecdc4;
            --pattatras: #ffd166;
            --nombre: #2a2c4d;
        }
        * { box-sizing: border-box; }
        body {
            margin: 0;
            font-family: "Segoe UI", system-ui, sans-serif;
            color: var(--text);
            background: radial-gradient(circle at 20% 0%, #2b1d5c 0%, var(--bg) 55%) fixed;
            min-height: 100vh;
        }
        .wrap { max-width: 1100px; margin: 0 auto; padding: 32px 20px 60px; }
        header { text-align: center; margin-bottom: 28px; }
        h1 {
            font-size: clamp(2rem, 6vw, 3.4rem);
            margin: 0 0 6px;
            background: linear-gradient(90deg, var(--patte), var(--pattatras), var(--tatras));
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            letter-spacing: -1px;
        }
        header p { color: var(--muted); margin: 0; }
 
        .stats {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(190px, 1fr));
            gap: 14px;
            margin-bottom: 24px;
        }
        .stat {
            background: var(--card);
            border-radius: 16px;
            padding: 16px 18px;
            border-left: 5px solid var(--c);
        }
        .stat strong { display: block; font-size: 1.8rem; color: var(--c); }
        .stat span { color: var(--muted); font-size: .9rem; }
 
        .toolbar {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            align-items: center;
            background: var(--card);
            padding: 14px;
            border-radius: 16px;
            margin-bottom: 22px;
        }
        .toolbar input, .toolbar select {
            background: #10112a;
            color: var(--text);
            border: 1px solid #34366b;
            border-radius: 10px;
            padding: 9px 12px;
            font-size: .95rem;
        }
        .toolbar input { flex: 1 1 180px; }
        .chip {
            border: 1px solid #34366b;
            background: transparent;
            color: var(--muted);
            padding: 8px 14px;
            border-radius: 999px;
            cursor: pointer;
            font-size: .9rem;
            transition: .15s;
        }
        .chip:hover { color: var(--text); }
        .chip.active { background: var(--text); color: var(--bg); border-color: var(--text); font-weight: 600; }
 
        .grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(120px, 1fr));
            gap: 10px;
        }
        .cell {
            position: relative;
            border-radius: 12px;
            padding: 22px 8px 14px;
            text-align: center;
            font-weight: 700;
            background: var(--nombre);
            transition: transform .12s;
        }
        .cell:hover { transform: translateY(-3px) scale(1.03); }
        .cell small {
            position: absolute;
            top: 6px; left: 9px;
            font-size: .7rem;
            font-weight: 500;
            opacity: .65;
        }
        .cell.patte { background: var(--patte); color: #3a0a1d; }
        .cell.tatras { background: var(--tatras); color: #04302d; }
        .cell.pattatras {
            background: linear-gradient(135deg, var(--pattatras), var(--patte));
            color: #3a2600;
            box-shadow: 0 0 18px rgba(255, 209, 102, .35);
        }
        .cell.nombre { color: var(--muted); font-weight: 500; }
 
        .pager {
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 16px;
            margin-top: 26px;
            color: var(--muted);
        }
        .pager button {
            background: var(--card);
            color: var(--text);
            border: 1px solid #34366b;
            padding: 9px 18px;
            border-radius: 10px;
            cursor: pointer;
        }
        .pager button:disabled { opacity: .35; cursor: not-allowed; }
        .empty { text-align: center; color: var(--muted); padding: 40px 0; }
    </style>
</head>
<body>
<div id="app" class="wrap">
    <header>
        <h1>Patte &amp; Tatras</h1>
        <p>Les nombres de 1 à {{ max }}, version « Pattatras »</p>
    </header>
 
    <section class="stats">
        <div class="stat" style="--c: var(--patte)">
            <strong>{{ fmt(stats.patte) }}</strong><span>« Patte » (multiples de 3 seuls)</span>
        </div>
        <div class="stat" style="--c: var(--tatras)">
            <strong>{{ fmt(stats.tatras) }}</strong><span>« Tatras » (multiples de 5 seuls)</span>
        </div>
        <div class="stat" style="--c: var(--pattatras)">
            <strong>{{ fmt(stats.pattatras) }}</strong><span>« Pattatras » (multiples de 15)</span>
        </div>
        <div class="stat" style="--c: var(--muted)">
            <strong>{{ fmt(stats.nombre) }}</strong><span>Nombres affichés tels quels</span>
        </div>
    </section>
 
    <section class="toolbar">
        <input v-model.trim="recherche" type="search" placeholder="Rechercher un nombre ou un mot…">
        <button v-for="f in filtres" :key="f.id" class="chip"
                :class="{ active: filtre === f.id }" @click="filtre = f.id">
            {{ f.nom }}
        </button>
        <select v-model.number="parPage">
            <option :value="100">100 / page</option>
            <option :value="250">250 / page</option>
            <option :value="500">500 / page</option>
        </select>
    </section>
 
    <div v-if="visibles.length" class="grid">
        <div v-for="it in visibles" :key="it.n" class="cell" :class="it.type">
            <small v-if="it.type !== 'nombre'">{{ it.n }}</small>
            {{ it.label }}
        </div>
    </div>
    <p v-else class="empty">Aucun résultat.</p>
 
    <nav class="pager" v-if="totalPages > 1">
        <button :disabled="page === 1" @click="page--">← Précédent</button>
        <span>Page {{ page }} / {{ totalPages }} · {{ fmt(filtres_items.length) }} résultats</span>
        <button :disabled="page === totalPages" @click="page++">Suivant →</button>
    </nav>
</div>
 
<script>
    const DONNEES = <?= $donnees ?>;
 
    Vue.createApp({
        data() {
            return {
                max: DONNEES.max,
                items: DONNEES.items,
                stats: DONNEES.stats,
                recherche: '',
                filtre: 'tous',
                page: 1,
                parPage: 250,
                filtres: [
                    { id: 'tous', nom: 'Tous' },
                    { id: 'patte', nom: 'Patte' },
                    { id: 'tatras', nom: 'Tatras' },
                    { id: 'pattatras', nom: 'Pattatras' },
                    { id: 'nombre', nom: 'Nombres' },
                ],
            };
        },
        computed: {
            filtres_items() {
                const q = this.recherche.toLowerCase();
                return this.items.filter(it =>
                    (this.filtre === 'tous' || it.type === this.filtre) &&
                    (!q || String(it.n).includes(q) || it.label.toLowerCase().includes(q))
                );
            },
            totalPages() {
                return Math.max(1, Math.ceil(this.filtres_items.length / this.parPage));
            },
            visibles() {
                const debut = (this.page - 1) * this.parPage;
                return this.filtres_items.slice(debut, debut + this.parPage);
            },
        },
        watch: {
            recherche() { this.page = 1; },
            filtre() { this.page = 1; },
            parPage() { this.page = 1; },
        },
        methods: {
            fmt(n) { return n.toLocaleString('fr-FR'); },
        },
    }).mount('#app');
</script>
</body>
</html>
 
Claude a terminé sa réponse.
