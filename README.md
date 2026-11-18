```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Estadísticas NBA En Vivo | ESPN API</title>
  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Google Fonts: Inter -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800;900&display=swap" rel="stylesheet">
  <script>
    tailwind.config = {
      theme: {
        extend: {
          fontFamily: {
            sans: ['Inter', '-apple-system', 'BlinkMacSystemFont', 'Segoe UI', 'Roboto', 'sans-serif'],
          },
          colors: {
            nbaBlue: '#1D428A',
            nbaRed: '#C8102E',
          }
        }
      }
    }
  </script>
</head>
<body class="bg-white text-slate-900 font-sans antialiased selection:bg-blue-100 selection:text-nbaBlue">

  <!-- Encabezado Fijo -->
  <header class="sticky top-0 z-40 bg-white/95 backdrop-blur border-b-2 border-slate-200 shadow-sm">
    <div class="max-w-[1440px] mx-auto px-4 md:px-6 py-3 flex flex-wrap items-center justify-between gap-3">
      <!-- Marca y Estado en Vivo -->
      <div class="flex items-center gap-3 flex-wrap">
        <div class="px-2.5 py-1.5 rounded-lg text-white font-black text-sm tracking-wider shadow-sm select-none"
             style="background: linear-gradient(135deg, #1D428A 50%, #C8102E 50%);">
          NBA
        </div>
        <h1 class="text-lg md:text-xl font-extrabold tracking-tight text-slate-900">
          Estadísticas NBA En Vivo
        </h1>
        <span class="inline-flex items-center gap-1.5 px-2.5 py-1 rounded-full text-xs font-bold bg-emerald-50 text-emerald-700 border border-emerald-200">
          <span class="w-2 h-2 rounded-full bg-emerald-500 animate-pulse"></span>
          Conectado a ESPN API
        </span>
      </div>

      <!-- Buscador y Pestañas Principales -->
      <div class="flex flex-wrap items-center gap-2 w-full md:w-auto">
        <input
          type="text"
          id="rosterFilterInput"
          placeholder="Filtrar jugador en este equipo..."
          class="w-full md:w-60 px-3 py-2 text-sm bg-white border border-slate-300 rounded-lg text-slate-900 placeholder-slate-400 focus:outline-none focus:ring-2 focus:ring-nbaBlue/20 focus:border-nbaBlue transition"
        />
        <div class="flex items-center gap-1.5 w-full md:w-auto overflow-x-auto">
          <button id="tabTeams" onclick="switchView('teams')"
            class="flex-1 md:flex-none px-3.5 py-2 rounded-lg text-xs md:text-sm font-bold border transition bg-nbaBlue text-white border-nbaBlue cursor-pointer whitespace-nowrap">
            Por Equipos (30)
          </button>
          <button id="tabLeaders" onclick="switchView('leaders')"
            class="flex-1 md:flex-none px-3.5 py-2 rounded-lg text-xs md:text-sm font-bold border transition bg-white text-slate-800 border-slate-300 hover:bg-slate-100 cursor-pointer whitespace-nowrap">
            Líderes NBA
          </button>
          <button id="tabInjuries" onclick="switchView('injuries')"
            class="flex-1 md:flex-none px-3.5 py-2 rounded-lg text-xs md:text-sm font-bold border transition bg-white text-slate-800 border-slate-300 hover:bg-slate-100 cursor-pointer whitespace-nowrap">
            Lesionados NBA
          </button>
        </div>
      </div>
    </div>
  </header>

  <!-- Contenedor Principal -->
  <main class="max-w-[1440px] mx-auto px-3 md:px-6 pt-5 pb-16">

    <!-- ================= VISTA: POR EQUIPOS ================= -->
    <section id="teamsView">

      <!-- Selector Móvil (<= 768px) con Logos Oficiales en 5 columnas -->
      <div class="md:hidden mb-4 bg-slate-50 border border-slate-200 rounded-xl p-3">
        <div class="flex gap-2 mb-3">
          <button id="mConfEste" onclick="setMobileConf('Este')"
            class="flex-1 py-2 px-3 rounded-lg text-xs font-extrabold border transition bg-nbaBlue text-white border-nbaBlue">
            Conferencia Este (15)
          </button>
          <button id="mConfOeste" onclick="setMobileConf('Oeste')"
            class="flex-1 py-2 px-3 rounded-lg text-xs font-extrabold border transition bg-white text-slate-800 border-slate-300">
            Conferencia Oeste (15)
          </button>
        </div>
        <div id="mobileLogosGrid" class="grid grid-cols-5 gap-1.5">
          <!-- Tarjetas de equipos generadas dinámicamente -->
        </div>
      </div>

      <!-- Directorio de Escritorio (> 768px) -->
      <div class="hidden md:block mb-6 bg-slate-50 border border-slate-200 rounded-xl p-4">
        <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
          <div>
            <div class="flex items-center justify-between pb-2 mb-3 border-b-2 border-slate-200 text-xs font-extrabold uppercase tracking-wider text-slate-600">
              <span>Conferencia Este</span>
              <span>15 Equipos</span>
            </div>
            <div id="eastDivisions" class="grid grid-cols-3 gap-3"></div>
          </div>
          <div>
            <div class="flex items-center justify-between pb-2 mb-3 border-b-2 border-slate-200 text-xs font-extrabold uppercase tracking-wider text-slate-600">
              <span>Conferencia Oeste</span>
              <span>15 Equipos</span>
            </div>
            <div id="westDivisions" class="grid grid-cols-3 gap-3"></div>
          </div>
        </div>
      </div>

      <!-- Detalle del Equipo Seleccionado -->
      <div id="teamDetailSection">
        <div class="bg-slate-50 border border-slate-200 rounded-xl p-10 text-center text-slate-600 font-semibold text-sm">
          Cargando equipos oficiales de la NBA en vivo...
        </div>
      </div>
    </section>

    <!-- ================= VISTA: LÍDERES NBA ================= -->
    <section id="leadersView" class="hidden">
      <div id="leagueLeadersGrid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-5">
        <div class="col-span-full bg-slate-50 border border-slate-200 rounded-xl p-10 text-center text-slate-600 font-semibold text-sm">
          Cargando líderes oficiales de la NBA...
        </div>
      </div>
    </section>

    <!-- ================= VISTA: LESIONADOS NBA ================= -->
    <section id="injuriesView" class="hidden">
      <div class="bg-white border border-slate-200 rounded-xl overflow-hidden shadow-sm">
        <div class="px-4 py-3 bg-slate-50 border-b border-slate-200 flex items-center justify-between">
          <h2 id="injuriesCountTitle" class="text-sm font-extrabold text-slate-900">
            Reporte Médico Oficial NBA (En Vivo)
          </h2>
        </div>
        <div class="overflow-x-auto">
          <table class="w-full text-left border-collapse text-sm">
            <thead>
              <tr class="bg-slate-100 text-slate-600 text-[11px] font-extrabold uppercase border-b border-slate-300">
                <th class="py-2.5 px-3">Equipo</th>
                <th class="py-2.5 px-3">Jugador</th>
                <th class="py-2.5 px-3">POS</th>
                <th class="py-2.5 px-3 text-right">Estado</th>
                <th class="py-2.5 px-3">Detalle de Lesión</th>
              </tr>
            </thead>
            <tbody id="injuriesTableBody" class="divide-y divide-slate-200">
              <tr>
                <td colspan="5" class="py-8 text-center text-slate-500 font-medium">
                  Cargando reporte de lesionados...
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </section>

  </main>

  <!-- Modal de Ficha de Jugador -->
  <div id="playerModal" onclick="if(event.target === this) closeModal()"
       class="hidden fixed inset-0 z-50 bg-slate-900/55 backdrop-blur-[1px] items-center justify-center p-4">
    <div id="modalContent" class="bg-white rounded-2xl max-w-xl w-full p-5 md:p-6 border border-slate-300 shadow-2xl">
      <!-- Contenido dinámico -->
    </div>
  </div>

  <script>
    // Mapa de conferencias y divisiones oficiales de la NBA
    const TEAM_META = {
      // Conferencia Este
      "BOS": { conf: "Este", div: "Atlántico" },
      "BKN": { conf: "Este", div: "Atlántico" },
      "NY":  { conf: "Este", div: "Atlántico" },
      "PHI": { conf: "Este", div: "Atlántico" },
      "TOR": { conf: "Este", div: "Atlántico" },
      "CHI": { conf: "Este", div: "Central" },
      "CLE": { conf: "Este", div: "Central" },
      "DET": { conf: "Este", div: "Central" },
      "IND": { conf: "Este", div: "Central" },
      "MIL": { conf: "Este", div: "Central" },
      "ATL": { conf: "Este", div: "Sureste" },
      "CHA": { conf: "Este", div: "Sureste" },
      "MIA": { conf: "Este", div: "Sureste" },
      "ORL": { conf: "Este", div: "Sureste" },
      "WSH": { conf: "Este", div: "Sureste" },
      // Conferencia Oeste
      "DEN": { conf: "Oeste", div: "Noroeste" },
      "MIN": { conf: "Oeste", div: "Noroeste" },
      "OKC": { conf: "Oeste", div: "Noroeste" },
      "POR": { conf: "Oeste", div: "Noroeste" },
      "UTAH":{ conf: "Oeste", div: "Noroeste" },
      "GS":  { conf: "Oeste", div: "Pacífico" },
      "LAC": { conf: "Oeste", div: "Pacífico" },
      "LAL": { conf: "Oeste", div: "Pacífico" },
      "PHX": { conf: "Oeste", div: "Pacífico" },
      "SAC": { conf: "Oeste", div: "Pacífico" },
      "DAL": { conf: "Oeste", div: "Suroeste" },
      "HOU": { conf: "Oeste", div: "Suroeste" },
      "MEM": { conf: "Oeste", div: "Suroeste" },
      "NO":  { conf: "Oeste", div: "Suroeste" },
      "SA":  { conf: "Oeste", div: "Suroeste" }
    };

    // Estado global de la aplicación
    let allTeams = [];
    let selectedTeamId = null;
    let rosterCache = {};
    let currentTeamPlayers = [];
    let sortField = "pts";
    let sortAsc = false;
    let posFilter = "ALL";
    let textFilter = "";
    let mobileConf = "Este";
    let leadersLoaded = false;
    let injuriesLoaded = false;

    /**
     * Genera un avatar SVG de respaldo con las iniciales del jugador
     */
    function getFallbackAvatar(name) {
      const initials = (name || "NB")
        .split(" ")
        .filter(Boolean)
        .map(n => n[0])
        .slice(0, 2)
        .join("")
        .toUpperCase();
      return `data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='80' height='80' viewBox='0 0 80 80'><rect width='80' height='80' fill='%231D428A'/><text x='50%' y='54%' dominant-baseline='middle' text-anchor='middle' fill='white' font-family='Inter,Arial,sans-serif' font-weight='800' font-size='26'>${initials}</text></svg>`;
    }

    /**
     * Inicializa la aplicación cargando los 30 equipos desde la API de ESPN
     */
    async function initApp() {
      try {
        const res = await fetch("https://site.api.espn.com/apis/site/v2/sports/basketball/nba/teams?limit=40");
        if (!res.ok) throw new Error("Error HTTP al obtener equipos");
        const data = await res.json();
        const rawTeams = data.sports[0].leagues[0].teams.map(t => t.team);

        allTeams = rawTeams.map(t => {
          const meta = TEAM_META[t.abbreviation] || { conf: "Este", div: "Atlántico" };
          return {
            id: t.id,
            abbr: t.abbreviation,
            name: t.displayName,
            color: t.color ? `#${t.color}` : "#1D428A",
            logo: (t.logos && t.logos[0]) ? t.logos[0].href : "",
            conf: meta.conf,
            div: meta.div,
            record: ""
          };
        });

        renderDirectory();
        const defaultTeam = allTeams.find(t => t.abbr === "LAL") || allTeams[0];
        if (defaultTeam) selectTeam(defaultTeam.id);
      } catch (err) {
        document.getElementById("teamDetailSection").innerHTML = `
          <div class="bg-red-50 border border-red-200 text-red-800 p-4 rounded-xl text-sm font-semibold">
            No se pudo conectar con la API de ESPN. Verifica tu conexión a internet o abre la aplicación desde un entorno web (https://).
          </div>
        `;
      }
    }

    /**
     * Renderiza el directorio de equipos (escritorio y móvil)
     */
    function renderDirectory() {
      const eastDivs = ["Atlántico", "Central", "Sureste"];
      const westDivs = ["Noroeste", "Pacífico", "Suroeste"];

      const buildDivHtml = (conf, divs) => divs.map(div => {
        const teams = allTeams
          .filter(t => t.conf === conf && t.div === div)
          .sort((a, b) => a.abbr.localeCompare(b.abbr));

        return `
          <div>
            <h4 class="text-[11px] font-bold uppercase text-slate-500 mb-1.5">${div}</h4>
            <div class="space-y-1.5">
              ${teams.map(t => {
                const isSelected = t.id === selectedTeamId;
                return `
                  <button onclick="selectTeam('${t.id}')"
                    class="w-full flex items-center justify-between px-2 py-1.5 rounded-lg text-left transition cursor-pointer ${
                      isSelected
                        ? 'bg-blue-50 border-2 border-nbaBlue shadow-xs'
                        : 'bg-white border border-slate-200 hover:bg-slate-100 hover:border-slate-300'
                    }">
                    <span class="flex items-center gap-2 min-w-0">
                      <img src="${t.logo}" alt="${t.abbr}" class="w-5 h-5 object-contain shrink-0">
                      <span class="text-xs font-extrabold text-slate-900">${t.abbr}</span>
                    </span>
                    <span id="rec-${t.id}" class="text-[11px] font-semibold text-slate-500 tabular-nums">${t.record}</span>
                  </button>
                `;
              }).join("")}
            </div>
          </div>
        `;
      }).join("");

      document.getElementById("eastDivisions").innerHTML = buildDivHtml("Este", eastDivs);
      document.getElementById("westDivisions").innerHTML = buildDivHtml("Oeste", westDivs);
      renderMobileLogos();
    }

    /**
     * Cambia la conferencia visible en el selector móvil
     */
    function setMobileConf(conf) {
      mobileConf = conf;
      const btnEste = document.getElementById("mConfEste");
      const btnOeste = document.getElementById("mConfOeste");

      if (conf === "Este") {
        btnEste.className = "flex-1 py-2 px-3 rounded-lg text-xs font-extrabold border transition bg-nbaBlue text-white border-nbaBlue";
        btnOeste.className = "flex-1 py-2 px-3 rounded-lg text-xs font-extrabold border transition bg-white text-slate-800 border-slate-300";
      } else {
        btnOeste.className = "flex-1 py-2 px-3 rounded-lg text-xs font-extrabold border transition bg-nbaBlue text-white border-nbaBlue";
        btnEste.className = "flex-1 py-2 px-3 rounded-lg text-xs font-extrabold border transition bg-white text-slate-800 border-slate-300";
      }
      renderMobileLogos();
    }

    /**
     * Renderiza la cuadrícula móvil de 5 columnas con logos oficiales
     */
    function renderMobileLogos() {
      const grid = document.getElementById("mobileLogosGrid");
      if (!grid) return;

      const teams = allTeams
        .filter(t => t.conf === mobileConf)
        .sort((a, b) => a.abbr.localeCompare(b.abbr));

      grid.innerHTML = teams.map(t => {
        const isSelected = t.id === selectedTeamId;
        return `
          <button onclick="selectTeam('${t.id}')"
            class="flex flex-col items-center justify-center py-2 px-1 rounded-lg transition cursor-pointer ${
              isSelected
                ? 'bg-blue-50 border-2 border-nbaBlue'
                : 'bg-white border border-slate-200 active:bg-slate-100'
            }">
            <img src="${t.logo}" alt="${t.abbr}" class="w-7 h-7 object-contain mb-0.5">
            <span class="text-[10px] font-extrabold text-slate-900 leading-tight">${t.abbr}</span>
            <span id="mrec-${t.id}" class="text-[9px] font-semibold text-slate-500 tabular-nums leading-tight">${t.record}</span>
          </button>
        `;
      }).join("");
    }

    /**
     * Selecciona un equipo y carga en paralelo su récord, plantilla y estadísticas por jugador
     */
    async function selectTeam(teamId) {
      selectedTeamId = teamId;
      posFilter = "ALL";
      textFilter = "";
      document.getElementById("rosterFilterInput").value = "";
      renderDirectory();

      const team = allTeams.find(t => t.id === teamId);
      if (!team) return;

      // Usar caché si ya se cargó previamente
      if (rosterCache[teamId]) {
        currentTeamPlayers = rosterCache[teamId].players;
        renderTeamDetail(team, rosterCache[teamId].record);
        return;
      }

      document.getElementById("teamDetailSection").innerHTML = `
        <div class="bg-slate-50 border border-slate-200 rounded-xl p-10 text-center text-slate-600 font-semibold text-sm">
          Cargando plantilla completa, fotos oficiales, lesionados y estadísticas en vivo de <strong class="text-slate-900">${team.name}</strong>...
        </div>
      `;

      try {
        const [teamRes, rosterRes] = await Promise.all([
          fetch(`https://site.api.espn.com/apis/site/v2/sports/basketball/nba/teams/${teamId}`),
          fetch(`https://site.api.espn.com/apis/site/v2/sports/basketball/nba/teams/${teamId}/roster`)
        ]);

        const teamJson = await teamRes.json();
        const rosterJson = await rosterRes.json();

        const record = teamJson.team?.record?.items?.[0]?.summary || "En vivo";
        team.record = record;
        const recEl = document.getElementById(`rec-${team.id}`);
        if (recEl) recEl.textContent = record;
        const mRecEl = document.getElementById(`mrec-${team.id}`);
        if (mRecEl) mRecEl.textContent = record;

        const athletes = rosterJson.athletes || [];
        const players = athletes.map(a => {
          const inj = (a.injuries && a.injuries.length > 0) ? a.injuries[0] : null;
          return {
            id: a.id,
            num: a.jersey || "-",
            name: a.fullName,
            pos: a.position?.abbreviation || "-",
            photo: a.headshot?.href || getFallbackAvatar(a.fullName),
            status: inj ? (inj.status || "Out") : "",
            injuryDetail: inj ? (inj.longComment || inj.shortComment || inj.details?.type || "Lesión reportada") : "",
            pj: 0, min: 0, pts: 0, tpm: 0, tpp: 0, reb: 0, ast: 0, stl: 0, blk: 0
          };
        });

        // Obtener estadísticas de temporada regular en paralelo para cada jugador
        await Promise.all(players.map(async (p) => {
          try {
            const sRes = await fetch(`https://site.web.api.espn.com/apis/common/v3/sports/basketball/nba/athletes/${p.id}/overview`);
            if (!sRes.ok) return;
            const sData = await sRes.json();
            const statsObj = sData.statistics;
            if (!statsObj || !statsObj.names || !statsObj.splits) return;

            const regSplit = statsObj.splits.find(s => s.displayName && s.displayName.toLowerCase().includes("regular")) || statsObj.splits[0];
            if (!regSplit || !regSplit.stats) return;

            const map = {};
            statsObj.names.forEach((n, idx) => {
              map[n] = regSplit.stats[idx];
            });

            p.pj  = parseInt(map["gamesPlayed"] || map["GP"] || 0, 10) || 0;
            p.min = parseFloat(map["avgMinutes"] || map["MIN"] || 0) || 0;
            p.pts = parseFloat(map["avgPoints"] || map["PTS"] || 0) || 0;
            p.reb = parseFloat(map["avgRebounds"] || map["REB"] || 0) || 0;
            p.ast = parseFloat(map["avgAssists"] || map["AST"] || 0) || 0;
            p.stl = parseFloat(map["avgSteals"] || map["STL"] || 0) || 0;
            p.blk = parseFloat(map["avgBlocks"] || map["BLK"] || 0) || 0;

            if (map["avgThreePointFieldGoalsMade-avgThreePointFieldGoalsAttempted"]) {
              p.tpm = parseFloat(String
