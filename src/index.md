---
title: Atlas y Simulador de Desiertos de Cuidado CDMX
toc: false
theme: [ midnight, wide ]
---

# DAT4CCIÓN 2026 | Atlas y Simulador de Desiertos de Cuidado en la CDMX
### Cruce de Demanda Censal (INEGI 2020) e Infraestructura de Cuidados (DENUE) para la Redistribución del Trabajo No Remunerado

```js
import * as L from "npm:leaflet";

// 1. Cargar los 3 archivos desde src/data/
const alcaldias = await FileAttachment("data/alcaldias_maestro.json").json();
const puntosDenue = await FileAttachment("data/unidades_denue.json").json();
const geojsonAlcaldias = await FileAttachment("data/limites_alcaldias.json").json();
```

<div class="grid grid-cols-4">
  <div class="card">

```js
const filtroAlcaldia = view(Inputs.select(
  ["TODAS", ...alcaldias.map(d => d.NOM_MUN)],
  { label: "Filtrar Alcaldía:", value: "TODAS" }
));
```

  </div>
  <div class="card">

```js
const nuevasGuarderias = view(Inputs.range([0, 150], {
  step: 5,
  value: 50,
  label: "Nuevos CENDIs Públicos:"
}));
```

  </div>
  <div class="card">

```js
const nuevosCentrosDia = view(Inputs.range([0, 100], {
  step: 5,
  value: 30,
  label: "Nuevos Centros de Día:"
}));
```

  </div>
  <div class="card">

```js
const capasMapa = view(Inputs.checkbox(
  ["Público", "Privado"],
  { label: "Mostrar en mapa:", value: ["Público"] }
));
```

  </div>
</div>

```js
// Cálculos Reactivos de KPIs
const datosFiltrados = filtroAlcaldia === "TODAS"
  ? alcaldias
  : alcaldias.filter(d => d.NOM_MUN === filtroAlcaldia);

const totalDemanda = d3.sum(datosFiltrados, d => d.DEMANDA_TOTAL);
const totalNinos0a2 = d3.sum(datosFiltrados, d => d.P_0A2);
const totalGuardPub = d3.sum(datosFiltrados, d => d.GUARDERIAS_PUB);
const brechaInfantil = Math.round(totalNinos0a2 / Math.max(1, totalGuardPub));
const promInactividad = d3.mean(datosFiltrados, d => d.PCT_MUJERES_INACTIVAS);

// Cálculos del Simulador en las Top 8 alcaldías con mayor Índice de Prioridad
const top8 = alcaldias.slice(0, 8);
const sumPesoInf = d3.sum(top8, d => d.P_0A2 * d.NINOS_0A2_POR_GUARDERIA_PUB);
const sumPesoMay = d3.sum(top8, d => d.DEMANDA_MAYORES_Y_DISC * d.MAYORES_POR_CENTRO_PUB);

const simulacionTop8 = top8.map(d => {
  const asigG = Math.round(((d.P_0A2 * d.NINOS_0A2_POR_GUARDERIA_PUB) / sumPesoInf) * nuevasGuarderias);
  const asigC = Math.round(((d.DEMANDA_MAYORES_Y_DISC * d.MAYORES_POR_CENTRO_PUB) / sumPesoMay) * nuevosCentrosDia);
  const brechaAntes = Math.round(d.NINOS_0A2_POR_GUARDERIA_PUB);
  const brechaDespues = Math.round(d.P_0A2 / Math.max(1, d.GUARDERIAS_PUB + asigG));
  return {
    Alcaldía: d.NOM_MUN,
    Índice: d.INDICE_PRIORIDAD_CUIDADO,
    "+ CENDIs": asigG,
    "+ Centros Día": asigC,
    "Brecha Actual": brechaAntes,
    "Nueva Brecha": brechaDespues
  };
});

const mujeresBeneficiadas = (nuevasGuarderias * 120) + (nuevosCentrosDia * 150);
const horasLiberadas = mujeresBeneficiadas * 28;
```

<div class="grid grid-cols-4">
  <div class="card">
    <h2>Demanda Potencial (${filtroAlcaldia})</h2>
    <span class="big">${totalDemanda.toLocaleString("es-MX")}</span>
    <div class="muted">Niños 0-5, Adultos 60+ y Discapacidad</div>
  </div>
  <div class="card">
    <h2>Brecha Primera Infancia (0-2 años)</h2>
    <span class="big" style="color: #fbbf24;">${brechaInfantil.toLocaleString("es-MX")}</span>
    <div class="muted">Niños por cada Guardería Pública</div>
  </div>
  <div class="card">
    <h2>Exclusión Laboral Femenina</h2>
    <span class="big" style="color: #f43f5e;">${promInactividad.toFixed(1)}%</span>
    <div class="muted">Mujeres fuera de la fuerza laboral</div>
  </div>
  <div class="card">
    <h2>Impacto Simulado: Mujeres Beneficiadas</h2>
    <span class="big" style="color: #10b981;">${mujeresBeneficiadas.toLocaleString("es-MX")}</span>
    <div class="muted">${horasLiberadas.toLocaleString("es-MX")} hrs/sem liberadas (ENUT)</div>
  </div>
</div>

<div class="grid grid-cols-2">
  <div class="card">
    <h2>Mapa Territorial: Índice de Desiertos de Cuidado y Unidades DENUE</h2>
    <h3>Coloreado por Índice de Prioridad (0-100) y ubicación GPS de infraestructura</h3>

```js
{
  const div = display(document.createElement("div"));
  div.style = "height: 440px; width: 100%; border-radius: 8px; margin-top: 8px; z-index: 1;";

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
        weight: isSelected ? 2 : 0.8,
        color: isSelected ? "#ffffff" : "#1e293b",
        fillOpacity: isSelected ? 0.78 : 0.25
      };
    },
    onEachFeature: (f, layer) => {
      const info = dict.get(f.properties.CVEGEO);
      if (info) {
        layer.bindPopup(
          `<b>${info.NOM_MUN}</b><br>` +
          `Índice Prioridad: <b>${info.INDICE_PRIORIDAD_CUIDADO}</b><br>` +
          `Niños (0-2)/Guardería Púb: <b>${info.NINOS_0A2_POR_GUARDERIA_PUB}</b><br>` +
          `Mujeres Inactivas: <b>${info.PCT_MUJERES_INACTIVAS.toFixed(1)}%</b>`
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
        color: "#000",
        weight: 0.8,
        fillOpacity: 0.9
      })
      .bindPopup(`<b>${p.nom_estab}</b><br>${p.TIPO_SERVICIO} (${p.SECTOR})`)
      .addTo(map);
    });

  // Asegurar que Leaflet calcule bien el tamaño del contenedor al renderizar
  setTimeout(() => map.invalidateSize(), 100);
}
```

  </div>
  <div class="card">
    <h2>Asignación Óptima de Infraestructura Pública (Top 8 Alcaldías Prioritarias)</h2>
    <h3>Se recalcula en vivo al mover los deslizadores de Nuevas Guarderías y Centros de Día</h3>

```js
display(Inputs.table(simulacionTop8, {
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

<div class="grid grid-cols-2">
  <div class="card">
    <h2>Impacto de la Solución: Reducción de la Brecha en Primera Infancia (0-2 años)</h2>
    <h3>Comparativa Antes (círculo rojo) vs. Después de construir +${nuevasGuarderias} CENDIs (círculo verde)</h3>

```js
display(resize((width) => Plot.plot({
  width,
  height: 340,
  marginLeft: 150,
  x: { label: "Niños (0-2 años) por Guardería Pública", grid: true },
  y: { label: null },
  marks: [
    Plot.link(simulacionTop8, {
      x1: "Brecha Actual",
      x2: "Nueva Brecha",
      y: "Alcaldía",
      stroke: "#94a3b8",
      strokeWidth: 2.5
    }),
    Plot.dot(simulacionTop8, {
      x: "Brecha Actual",
      y: "Alcaldía",
      fill: "#f43f5e",
      r: 6,
      tip: true,
      title: d => `${d.Alcaldía} (Actual): ${d["Brecha Actual"]} niños/guardería`
    }),
    Plot.dot(simulacionTop8, {
      x: "Nueva Brecha",
      y: "Alcaldía",
      fill: "#10b981",
      r: 6,
      tip: true,
      title: d => `${d.Alcaldía} (Con Solución): ${d["Nueva Brecha"]} niños/guardería`
    })
  ]
})));
```

  </div>
  <div class="card">
    <h2>Exclusión Laboral Femenina vs. Déficit de Cuidado Infantil</h2>
    <h3>El tamaño del círculo representa la Población Total de la Alcaldía; el color, su Índice de Prioridad</h3>

```js
display(resize((width) => Plot.plot({
  width,
  height: 340,
  grid: true,
  color: { scheme: "YlOrRd", legend: true, label: "Índice Prioridad" },
  x: { label: "Niños (0-2 años) por Guardería Pública →" },
  y: { label: "↑ % Mujeres Económicamente Inactivas" },
  marks: [
    Plot.dot(alcaldias, {
      x: "NINOS_0A2_POR_GUARDERIA_PUB",
      y: "PCT_MUJERES_INACTIVAS",
      r: d => Math.sqrt(d.POBTOT) / 60,
      fill: "INDICE_PRIORIDAD_CUIDADO",
      stroke: "#fff",
      strokeWidth: 1,
      tip: true,
      title: d => `${d.NOM_MUN}\nNiños/Guardería: ${d.NINOS_0A2_POR_GUARDERIA_PUB}\nMujeres Inactivas: ${d.PCT_MUJERES_INACTIVAS.toFixed(1)}%`
    }),
    Plot.text(alcaldias, {
      x: "NINOS_0A2_POR_GUARDERIA_PUB",
      y: "PCT_MUJERES_INACTIVAS",
      text: "NOM_MUN",
      dy: -12,
      fontSize: 10,
      fill: "currentColor"
    })
  ]
})));
```

  </div>
</div>