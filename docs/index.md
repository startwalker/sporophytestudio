<script type="module">

import * as d3 from "https://cdn.jsdelivr.net/npm/d3@7/+esm";

// // Declare the chart dimensions and margins.
const width = 1024;
const height = 800;
const marginTop = 20;
const marginRight = 20;
const marginBottom = 30;
const marginLeft = 40;

const data = [
    // Glycolysis
    {
        source: "glucose(6C)",
        target: "fructose 1,6-bisphosphate"
    },
    {
        source: "fructose 1,6-bisphosphate",
        target: "pyruvate(3C)"
    },
    // Link reaction
    {
        source: "pyruvate(3C)",
        target: "acetyl-CoA(2C)"
    },
    // Citric Acid Cycle (Krebs Cycle)
    {
        source: "acetyl-CoA(2C)",
        target: "citrate(6C)"
    },
    {
        source: "citrate(6C)",
        target: "isocitrate(6C)"
    },
    {
        source: "isocitrate(6C)",
        target: "α-ketoglutarate(5C)"
    },
    {
        source: "α-ketoglutarate(5C)",
        target: "succinyl-CoA(4C)"
    },
    {
        source: "succinyl-CoA(4C)",
        target: "succinate(4C)"
    },
    {
        source: "succinate(4C)",
        target: "fumarate(4C)"
    },
    {
        source: "fumarate(4C)",
        target: "malate(4C)"
    },
    {
        source: "malate(4C)",
        target: "oxaloacetate(4C)"
    },
    {
        source: "oxaloacetate(4C)",
        target: "citrate(6C)" // Cycle continues
    }
];


const nodes = Array.from(new Set(data.flatMap(d => [d.source, d.target])), id => ({id}));
const links = data.map(d => Object.create(d));

const drag = simulation => {
  
  function dragstarted(event, d) {
    if (!event.active) simulation.alphaTarget(0.3).restart();
    d.fx = d.x;
    d.fy = d.y;
  }
  
  function dragged(event, d) {
    d.fx = event.x;
    d.fy = event.y;
  }
  
  function dragended(event, d) {
    if (!event.active) simulation.alphaTarget(0);
    d.fx = null;
    d.fy = null;
  }
  
  return d3.drag()
      .on("start", dragstarted)
      .on("drag", dragged)
      .on("end", dragended);
}

const svg = d3.create("svg")
      .attr("viewBox", [-width / 2, -height / 2, width, height])
      .attr("width", width)
      .attr("height", height)
      .attr("style", "max-width: 100%; height: auto; font: 18px sans-serif;");

const simulation = d3.forceSimulation(nodes)
      .force("link", d3.forceLink(links).id(d => d.id))
      .force("charge", d3.forceManyBody().strength(-800))
      .force("x", d3.forceX())
      .force("y", d3.forceY());

const node = svg.append("g")
      .attr("fill", "currentColor")
      .attr("stroke-linecap", "round")
      .attr("stroke-linejoin", "round")
    .selectAll("g")
    .data(nodes)
    .join("g")
      .call(drag(simulation));

  node.append("circle")
      .attr("stroke", "white")
      .attr("stroke-width", 1.5)
      .attr("r", 12);

  node.append("text")
      .attr("x", 10)
      .attr("y", "0.31em")
      .text(d => d.id)
    .clone(true).lower()
      .attr("fill", "none")
      .attr("stroke", "white")
      .attr("stroke-width", 3);

const link = svg.append("g")
      .attr("fill", "none")
      .attr("stroke-width", 1.5)
    .selectAll("path")
    .data(links)
    .join("path")
      .attr("stroke", "gray")
      .attr("marker-end", d => `url(#${d.source})`);

simulation.on("tick", () => {
    link.attr("d", linkArc);
    node.attr("transform", d => `translate(${d.x},${d.y})`);
  });

function linkArc(d) {
  const r = Math.hypot(d.target.x - d.source.x, d.target.y - d.source.y);
  return `
    M${d.source.x},${d.source.y}
    A${r},${r} 0 0,1 ${d.target.x},${d.target.y}
  `;
}


container.append(svg.node());

</script>
<style>
.title {
  color: Green
}
</style>
<h1>Welcome to <span class="title">Sporophyte Studio</span></h1>
Sporophyte Studio aims to teach general biology lessons to students struggling to understand biological concepts.

<div id="container"></div>
