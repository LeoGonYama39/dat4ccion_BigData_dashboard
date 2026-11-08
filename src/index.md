---
title: Desiertos de Cuidado en la CDMX
toc: false
theme: [ midnight, wide ]
---

<style>
  /* Estilos css */
  .hero-box {
    background: linear-gradient(135deg, rgba(30, 41, 59, 0.95), rgba(15, 23, 42, 0.95));
    border: 1px solid rgba(148, 163, 184, 0.18);
    border-left: 5px solid #f59e0b;
    border-radius: 12px;
    padding: 1.5rem;
    margin-bottom: 1.5rem;
  }
  .hero-badges {
    display: flex;
    flex-wrap: wrap;
    gap: 0.5rem;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 0.75rem;
  }
  .badge-tag {
    font-size: 0.75rem;
    font-weight: 700;
    text-transform: uppercase;
    letter-spacing: 0.05em;
    padding: 0.25rem 0.75rem;
    border-radius: 999px;
    background: rgba(245, 158, 11, 0.15);
    color: #fbbf24;
    border: 1px solid rgba(245, 158, 11, 0.3);
  }
  .badge-author {
    font-size: 0.8rem;
    color: #cbd5e1;
    background: rgba(51, 65, 85, 0.6);
    padding: 0.25rem 0.85rem;
    border-radius: 999px;
    border: 1px solid rgba(148, 163, 184, 0.25);
  }
  .main-title {
    font-size: 1.75rem;
    font-weight: 800;
    color: #f8fafc;
    line-height: 1.25;
    margin: 0 0 0.5rem 0;
  }
  .sub-title {
    font-size: 0.95rem;
    color: #94a3b8;
    line-height: 1.5;
    margin: 0;
  }
  .section-banner {
    margin-top: 2rem;
    margin-bottom: 0.75rem;
    padding-bottom: 0.5rem;
    border-bottom: 1px solid rgba(148, 163, 184, 0.2);
  }
  .section-step {
    font-size: 0.75rem;
    font-weight: 700;
    text-transform: uppercase;
    letter-spacing: 0.08em;
    color: #fbbf24;
  }
  .section-heading {
    font-size: 1.25rem;
    font-weight: 700;
    color: #f1f5f9;
    margin: 0.15rem 0;
  }
  .section-desc {
    font-size: 0.85rem;
    color: #94a3b8;
  }
  .card-title {
    font-size: 0.95rem;
    font-weight: 700;
    color: #f1f5f9;
    margin-bottom: 0.2rem;
  }
  .card-subtitle {
    font-size: 0.8rem;
    color: #94a3b8;
    margin-bottom: 0.75rem;
  }
  .btn-group {
    display: flex;
    flex-wrap: wrap;
    gap: 0.6rem;
    margin-top: 0.75rem;
  }
  .btn-link {
    display: inline-flex;
    align-items: center;
    gap: 0.4rem;
    font-size: 0.78rem;
    font-weight: 600;
    padding: 0.45rem 0.85rem;
    border-radius: 8px;
    background: #1e293b;
    color: #e2e8f0 !important;
    text-decoration: none !important;
    border: 1px solid #334155;
    transition: all 0.15s ease;
  }
  .btn-link:hover {
    background: #334155;
    border-color: #fbbf24;
    color: #fbbf24 !important;
  }
  details.custom-accordion {
    background: rgba(30, 41, 59, 0.6);
    border: 1px solid rgba(148, 163, 184, 0.2);
    border-radius: 10px;
    padding: 0.85rem 1.2rem;
    margin-bottom: 0.75rem;
  }
  details.custom-accordion summary {
    font-weight: 700;
    font-size: 0.92rem;
    color: #e2e8f0;
    cursor: pointer;
  }
  .footer-box {
    margin-top: 2.5rem;
    padding: 1.25rem;
    border-top: 1px solid rgba(148, 163, 184, 0.2);
    font-size: 0.78rem;
    color: #94a3b8;
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
    gap: 1rem;
  }
</style>

<!-- Header -->
<div class="hero-box">
  <div class="hero-badges">
    <span class="badge-tag">DAT4CCIÓN 2026 • Cuidados y Trabajo No Remunerado</span>
    <span class="badge-author">Autor: <b>Leo González Yamada</b> | Proyecto Big Data — Universidad Iberoamericana CDMX</span>
  </div>
  <div class="main-title">Atlas de Desiertos de Cuidado en la CDMX: Dónde la falta de infraestructura pública frena la autonomía económica de las mujeres</div>
  <div class="sub-title">Tablero interactivo de diagnóstico territorial y simulador de inversión pública para la redistribución del trabajo de cuidados no remunerado (Censo INEGI 2020 y DENUE)</div>
  <div class="btn-group">
    <a class="btn-link" href="https://www.inegi.org.mx/programas/ccpv/2020/#datos_abiertos" target="_blank" rel="noopener">Fuente: Censo 2020 (INEGI)</a>
    <a class="btn-link" href="https://www.inegi.org.mx/app/descarga/default.html" target="_blank" rel="noopener">Fuente: DENUE (INEGI)</a>
    <a class="btn-link" href="https://datos.cdmx.gob.mx/dataset/alcaldias" target="_blank" rel="noopener">Fuente: Límites Alcaldías (CDMX)</a>
    <a class="btn-link" href="https://github.com/LeoGonYama39/dat4ccion_BigData_dashboard" target="_blank" rel="noopener">Repositorio GitHub (Notebook Colab + Datos)</a>
  </div>
</div>

```js
import * as L from "npm:leaflet";

// 1. Cargar los 3 archivos desde src/data/
const alcaldias = await FileAttachment("data/alcaldias_maestro.json").json();
const puntosDenue = await FileAttachment("data/unidades_denue.json").json();
const geojsonAlcaldias = await FileAttachment("data/limites_alcaldias.json").json();
```

<!-- Pregunta: ¿Qué está pasando? -->
<div class="section-banner">
  <div class="section-step">¿Qué está pasando en el territorio?</div>
  <div class="section-heading">Diagnóstico General de Demanda, Oferta y Brecha de Género Actual</div>
  <div class="section-desc">Filtra por alcaldía para explorar la carga actual de cuidados y su relación con la exclusión laboral femenina frente a la masculina.</div>
</div>

<div class="grid grid-cols-2">
  <div class="card">

```js
const filtroAlcaldia = view(Inputs.select(
  ["TODAS", ...alcaldias.map(d => d.NOM_MUN)],
  { label: "Explorar Alcaldía:", value: "TODAS" }
));
```

  </div>
  <div class="card">

```js
const capasMapa = view(Inputs.checkbox(
  ["Público", "Privado"],
  { label: "Mostrar unidades económicas en el mapa:", value: ["Público"] }
));
```

  </div>
</div>

```js
// Resultados del daignóstico
const datosFiltrados = filtroAlcaldia === "TODAS"
  ? alcaldias
  : alcaldias.filter(d => d.NOM_MUN === filtroAlcaldia);

const totalDemanda = d3.sum(datosFiltrados, d => d.DEMANDA_TOTAL);
const totalNinos0a2 = d3.sum(datosFiltrados, d => d.P_0A2);
const totalGuardPub = d3.sum(datosFiltrados, d => d.GUARDERIAS_PUB);
const brechaInfantil = Math.round(totalNinos0a2 / Math.max(1, totalGuardPub));

const promInactividadM = d3.mean(datosFiltrados, d => d.PCT_MUJERES_INACTIVAS);
const promInactividadH = d3.mean(datosFiltrados, d => d.PCT_HOMBRES_INACTIVOS ?? 22.4);
const brechaGeneroInact = promInactividadM - promInactividadH;

const totalPub = d3.sum(datosFiltrados, d => d.UNIDADES_PUBLICAS);
const totalPriv = d3.sum(datosFiltrados, d => d.UNIDADES_PRIVADAS);
const pctPrivatizacion = (totalPriv / Math.max(1, totalPub + totalPriv)) * 100;
```

<!-- kpi -->
<div class="grid grid-cols-4">
  <div class="card">
    <div class="card-title">Demanda Potencial (${filtroAlcaldia})</div>
    <span class="big" style="color: #38bdf8;">${totalDemanda.toLocaleString("es-MX")}</span>
    <div class="muted">Personas (Niños 0-5, Adultos 60+ y Discapacidad)</div>
  </div>
  <div class="card">
    <div class="card-title">Déficit en Primera Infancia (0-2 años)</div>
    <span class="big" style="color: #f59e0b;">${brechaInfantil.toLocaleString("es-MX")}</span>
    <div class="muted">Niños de 0 a 2 años por cada Guardería Pública</div>
  </div>
  <div class="card">
    <div class="card-title">Exclusión Laboral Femenina</div>
    <span class="big" style="color: #f59e0b;">${promInactividadM.toFixed(1)}%</span>
    <div class="muted">vs. <b>${promInactividadH.toFixed(1)}%</b> en hombres (Brecha: <b>+${brechaGeneroInact.toFixed(1)} pts</b>)</div>
  </div>
  <div class="card">
    <div class="card-title">Privatización de la Infraestructura</div>
    <span class="big" style="color: #cbd5e1;">${pctPrivatizacion.toFixed(1)}%</span>
    <div class="muted">${totalPriv.toLocaleString("es-MX")} privadas vs. ${totalPub.toLocaleString("es-MX")} públicas</div>
  </div>
</div>

<!-- Pregunta: ¿Por qué improta? -->
<div class="section-banner">
  <div class="section-step">¿Por qué importa y dónde se concentra la desigualdad?</div>
  <div class="section-heading">Distribución Espacial de los Desiertos de Cuidado y su Efecto en la Inactividad Económica</div>
  <div class="section-desc">En las alcaldías periféricas donde escasean las guarderías públicas, casi la mitad de las mujeres en edad productiva quedan fuera del mercado laboral.</div>
</div>

<div class="grid grid-cols-2">
  <div class="card">
    <div class="card-title">Mapa Territorial: Índice de Prioridad de Cuidado (0-100) y Unidades DENUE</div>
    <div class="card-subtitle">Las zonas en tonos más cálidos concentran mayor demanda desatendida y mayor exclusión laboral femenina</div>

```js
{
  const div = display(document.createElement("div"));
  div.style = "height: 430px; width: 100%; border-radius: 8px; margin-top: 4px; z-index: 1;";

  const map = L.map(div).setView([19.32, -99.14], 10);
  L.tileLayer("https://{s}[.basemaps.cartocdn.com/dark_all/](https://.basemaps.cartocdn.com/dark_all/){z}/{x}/{y}{r}.png", {
    attribution: "&copy; OpenStreetMap &copy; CARTO | INEGI"
  }).addTo(map);

  const dict = new Map(alcaldias.map(d => [d.CVEGEO, d]));
  const colorScale = d3.scaleSequential(d3.interpolateYlOrRd).domain([10, 70]);

  L.geoJSON(geojsonAlcaldias, {
    style: f => {
      const info = dict.get(f.properties.CVEGEO);
      const isSelected = filtroAlcaldia === "TODAS" || (info && info.NOM_MUN === filtroAlcaldia);
      return {
        fillColor: info ? colorScale(info.INDICE_PRIORIDAD_CUIDADO) : "#333",
        weight: isSelected ? 2.2 : 0.8,
        color: isSelected ? "#ffffff" : "#1e293b",
        fillOpacity: isSelected ? 0.8 : 0.22
      };
    },
    onEachFeature: (f, layer) => {
      const info = dict.get(f.properties.CVEGEO);
      if (info) {
        const hInact = info.PCT_HOMBRES_INACTIVOS ?? 22.4;
        layer.bindPopup(
          `<b>${info.NOM_MUN}</b><br>` +
          `Índice de Prioridad: <b>${info.INDICE_PRIORIDAD_CUIDADO} / 100</b><br>` +
          `Niños (0-2) por Guardería Púb: <b>${info.NINOS_0A2_POR_GUARDERIA_PUB.toLocaleString("es-MX")}</b><br>` +
          `Mayores/Disc. por Centro Púb: <b>${info.MAYORES_POR_CENTRO_PUB.toLocaleString("es-MX")}</b><br>` +
          `Inactividad Mujeres: <b>${info.PCT_MUJERES_INACTIVAS.toFixed(1)}%</b> (vs. ${hInact.toFixed(1)}% hombres)`
        );
      }
    }
  }).addTo(map);

  puntosDenue
    .filter(p => capasMapa.includes(p.SECTOR))
    .forEach(p => {
      L.circleMarker([p.latitud, p.longitud], {
        radius: 3.5,
        fillColor: p.SECTOR === "Público" ? "#10b981" : "#f59e0b",
        color: "#0f172a",
        weight: 0.8,
        fillOpacity: 0.9
      })
      .bindPopup(`<b>${p.nom_estab}</b><br>${p.TIPO_SERVICIO} (${p.SECTOR})<br>Personal: ${p.per_ocu}`)
      .addTo(map);
    });

  setTimeout(() => map.invalidateSize(), 100);
}
```

  </div>
<div class="card">
    <div class="card-title">A menor cobertura pública de cuidados, mayor exclusión laboral de las mujeres</div>
    <div class="card-subtitle">Tamaño de burbuja = Población Total de la alcaldía | Color = Índice de Prioridad (0-100)</div>

```js
display(resize((width) => Plot.plot({
  width,
  height: 410,
  marginTop: 25,
  marginRight: 35,
  grid: false,
  color: { scheme: "YlOrRd", legend: true, label: "Índice de Prioridad" },
  r: { range: [3, 14] },
  x: { label: "Niños (0-2 años) por cada Guardería Pública →", tickFormat: "s" },
  y: { label: "↑ % Mujeres Económicamente Inactivas", domain: [34, 50] },
  marks: [
    Plot.ruleY([d3.mean(alcaldias, d => d.PCT_MUJERES_INACTIVAS)], { stroke: "#475569", strokeDasharray: "4,4" }),
    Plot.dot(alcaldias, {
      x: "NINOS_0A2_POR_GUARDERIA_PUB",
      y: "PCT_MUJERES_INACTIVAS",
      r: "POBTOT",
      fill: "INDICE_PRIORIDAD_CUIDADO",
      stroke: d => (filtroAlcaldia === "TODAS" || d.NOM_MUN === filtroAlcaldia) ? "#ffffff" : "#1e293b",
      strokeWidth: d => (d.NOM_MUN === filtroAlcaldia) ? 2.5 : 0.8,
      fillOpacity: d => (filtroAlcaldia === "TODAS" || d.NOM_MUN === filtroAlcaldia) ? 0.75 : 0.20,
      tip: true,
      title: d => `${d.NOM_MUN}\nPoblación Total: ${d.POBTOT.toLocaleString("es-MX")}\nÍndice Prioridad: ${d.INDICE_PRIORIDAD_CUIDADO}\nNiños (0-2)/Guardería Púb: ${d.NINOS_0A2_POR_GUARDERIA_PUB.toLocaleString("es-MX")}\nMujeres Inactivas: ${d.PCT_MUJERES_INACTIVAS.toFixed(1)}% (vs. ${(d.PCT_HOMBRES_INACTIVOS ?? 22.4).toFixed(1)}% hombres)`
    }),
    Plot.text(alcaldias, {
      x: "NINOS_0A2_POR_GUARDERIA_PUB",
      y: "PCT_MUJERES_INACTIVAS",
      text: "NOM_MUN",
      dy: d => -(Math.sqrt(d.POBTOT / 1850000) * 12 + 6),
      fontSize: 10,
      fill: d => (filtroAlcaldia === "TODAS" || d.NOM_MUN === filtroAlcaldia) ? "#f8fafc" : "#64748b"
    })
  ]
})));
```

  </div>
</div>

<!-- Pregunta: ¿Qué cambiar? -->
<div class="section-banner">
  <div class="section-step">¿Qué debería cambiar? (Del Dato a la Acción)</div>
  <div class="section-heading">Simulador de Inversión Pública para el Sistema de Cuidados de la CDMX</div>
  <div class="section-desc">Ajusta la meta de construcción de nuevos centros públicos. El modelo asigna las unidades a las 8 alcaldías más críticas ponderando déficit relativo y masa demográfica.</div>
</div>

<div class="grid grid-cols-4">
  <div class="card">

```js
const nuevasGuarderias = view(Inputs.range([0, 150], {
  step: 5,
  value: 50,
  label: "Nuevos CENDIs Públicos (0-5 años):"
}));
```

  </div>
  <div class="card">

```js
const nuevosCentrosDia = view(Inputs.range([0, 100], {
  step: 5,
  value: 30,
  label: "Nuevos Centros de Día (Mayores/Disc.):"
}));
```

  </div>
</div>

```js
// Cálculos Reactivos del Simulador (Fuera del grid para no ocupar una celda en el DOM)
const top8 = alcaldias.slice(0, 8);
const sumPesoInf = d3.sum(top8, d => d.P_0A2 * d.NINOS_0A2_POR_GUARDERIA_PUB);
const sumPesoMay = d3.sum(top8, d => d.DEMANDA_MAYORES_Y_DISC * d.MAYORES_POR_CENTRO_PUB);

const simulacionTop8 = top8.map(d => {
  const asigG = Math.round(((d.P_0A2 * d.NINOS_0A2_POR_GUARDERIA_PUB) / sumPesoInf) * nuevasGuarderias);
  const asigC = Math.round(((d.DEMANDA_MAYORES_Y_DISC * d.MAYORES_POR_CENTRO_PUB) / sumPesoMay) * nuevosCentrosDia);
  const brechaAntes = Math.round(d.NINOS_0A2_POR_GUARDERIA_PUB);
  const brechaDespues = Math.round(d.P_0A2 / Math.max(1, d.GUARDERIAS_PUB + asigG));
  const reduccionPct = ((1 - (brechaDespues / brechaAntes)) * 100).toFixed(1);
  return {
    Alcaldía: d.NOM_MUN,
    Índice: d.INDICE_PRIORIDAD_CUIDADO,
    "+ CENDIs": asigG,
    "+ Centros Día": asigC,
    "Brecha Actual": brechaAntes,
    "Nueva Brecha": brechaDespues,
    "Reducción": `-${reduccionPct}%`
  };
});

const mujeresBeneficiadas = (nuevasGuarderias * 120) + (nuevosCentrosDia * 150);
const horasLiberadas = mujeresBeneficiadas * 28;
```
</div>

<!-- Resultados del simulador -->
<div class="grid grid-cols-2">
  <div class="card">
    <div class="card-title">Impacto de la Solución: Reducción de la Brecha en Primera Infancia (0-2 años)</div>
    <div class="card-subtitle">Comparativa Antes (círculo ámbar) vs. Después de construir +${nuevasGuarderias} CENDIs (círculo verde esmeralda)</div>

```js
display(resize((width) => Plot.plot({
  width,
  height: 330,
  marginLeft: 155,
  marginRight: 30,
  grid: false,
  x: { label: "Niños (0-2 años) por cada Guardería Pública →" },
  y: { label: null },
  marks: [
    Plot.ruleX([0], { stroke: "#334155" }),
    Plot.link(simulacionTop8, {
      x1: "Brecha Actual",
      x2: "Nueva Brecha",
      y: "Alcaldía",
      stroke: "#64748b",
      strokeWidth: 2.5
    }),
    Plot.dot(simulacionTop8, {
      x: "Brecha Actual",
      y: "Alcaldía",
      fill: "#f59e0b",
      r: 6.5,
      tip: true,
      title: d => `${d.Alcaldía} (Situación Actual): ${d["Brecha Actual"].toLocaleString("es-MX")} niños/guardería`
    }),
    Plot.dot(simulacionTop8, {
      x: "Nueva Brecha",
      y: "Alcaldía",
      fill: "#10b981",
      r: 6.5,
      tip: true,
      title: d => `${d.Alcaldía} (Con +${d["+ CENDIs"]} CENDIs): ${d["Nueva Brecha"].toLocaleString("es-MX")} niños/guardería (${d.Reducción})`
    })
  ]
})));
```

  </div>
  <div class="card">
    <div class="card-title">Asignación Óptima de Infraestructura Pública (Top 8 Alcaldías Prioritarias)</div>
    <div class="card-subtitle">Distribución matemática que pondera severidad del déficit e intensidad demográfica</div>

```js
display(Inputs.table(simulacionTop8, {
  select: false,
  format: {
    Índice: x => x.toFixed(1),
    "+ CENDIs": x => `+${x}`,
    "+ Centros Día": x => `+${x}`,
    "Brecha Actual": x => `${x.toLocaleString("es-MX")} niños/u.`,
    "Nueva Brecha": x => `${x.toLocaleString("es-MX")} niños/u.`
  }
}));
```

  </div>
</div>

<!-- Sección de abajo, glosario y transparencia -->
<div class="section-banner">
  <div class="section-step">Transparencia Algorítmica y Ética de Datos</div>
  <div class="section-heading">Documentación Metodológica, Diccionario de Variables y Población Oculta</div>
</div>

<details class="custom-accordion">
  <summary>Glosario y Diccionario de Variables</summary>
  <div style="margin-top: 0.85rem; font-size: 0.83rem; color: #cbd5e1; line-height: 1.55;">
    <ul>
      <li><b>Demanda Potencial de Cuidado (<code>DEMANDA_TOTAL</code>):</b> Suma de niñas y niños de primera infancia (<code>P_0A2</code> + <code>P_3A5</code>), personas adultas mayores de 60 años y más (<code>P_60YMAS</code>) y personas con alguna discapacidad (<code>PCON_DISC</code>) del Censo 2020 (INEGI).</li>
      <li><b>Exclusión Laboral Femenina (<code>PCT_MUJERES_INACTIVAS</code>):</b> Proporción de mujeres de 12 años y más que se encuentran fuera de la Población Económicamente Activa (<code>PE_INAC_F / (PEA_F + PE_INAC_F)</code>), comparada directamente contra el mismo indicador en hombres (<code>PCT_HOMBRES_INACTIVOS</code>) para visibilizar la brecha por trabajo doméstico y de cuidados no remunerado.</li>
      <li><b>Infraestructura de Cuidados (<code>DENUE</code>):</b> Establecimientos activos filtrados por códigos SCIAN oficiales: Guarderías públicas (<code>624412</code>) y privadas (<code>624411</code>), Educación preescolar (<code>611111</code>/<code>611112</code>), Asilos para adultos mayores (<code>623311</code>/<code>623312</code>) y Centros de atención diurna para mayores y discapacidad (<code>624121</code>/<code>624122</code>).</li>
      <li><b>Índice de Prioridad de Cuidado (<code>0 a 100</code>):</b> Indicador compuesto normalizado (Min-Max) integrado en un 40% por el déficit de guarderías públicas en primera infancia (0-2 años), 30% por el déficit de centros públicos para mayores/discapacidad y 30% por la tasa de inactividad económica femenina.</li>
    </ul>
  </div>
</details>

<!-- Pie de página -->
<div class="footer-box">
  <div>
    <b>Autor:</b> Leo González Yamada — Ingeniería en Mecatrónica y Sistemas Ciberfísicos (Universidad Iberoamericana CDMX)<br>
    <b>Fuentes Oficiales:</b> INEGI (Censo de Población y Vivienda 2020, DENUE) y Portal de Datos Abiertos de la Ciudad de México.
  </div>
  <div style="text-align: right;">
    <b>Licenciamiento Abierto y Reproducibilidad:</b><br>
    Código distribuido bajo licencia <b>MIT</b> y contenido bajo <b>Creative Commons BY-SA 4.0</b> para libre auditoría y replicación comunitaria.
  </div>
</div>