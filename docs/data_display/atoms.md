<script src="../../myscripts/d3.v7.js" ></script>

<script type="module">
 function drawAtom(
    width,
    height,
    boxX,
    boxY,
    boxWidth,
    boxHeight,
    nodes,
    links,
    TextLabel,
    showLinks = false
    
){
    // get svg
    const svg = d3.create("svg")
        .attr("width", width)
        .attr("height", height)  
    svg.append("text")
            .text(TextLabel)
            .attr("x", boxX + 10)
            .attr("y", boxY + 20)

        svg.append('rect')
    .attr('x', boxX)
    .attr('y', boxY)
    .attr('width', boxWidth)
    .attr('height', boxHeight)
    .attr('stroke', 'black')
    .attr('fill', 'none')

    const graphGroup = svg.append("g")
    .attr("transform", `translate(${boxX}, ${boxY})`);


    const simulation = d3.forceSimulation(nodes)
    .force('charge',d3.forceManyBody().strength(-100))
    .force('collision', d3.forceCollide(10))
    .force('center',d3.forceCenter(boxWidth / 2, boxHeight / 2))
    .force('link',d3.forceLink(links).id(d => d.id).distance(d => d.value))
    // .force('x', d3.forceX())
    // .force('y', d3.forceY())
    // .alpha(1)
    // .alphaDecay(0.1)
    .on("tick",ticked);

    const link = graphGroup.append("g")
    .selectAll()
    .data(links)
    .join("line")
    .attr("stroke","#999")
    .attr("stroke-width", 1)

    const node  = graphGroup.append("g")
    .selectAll()
    .data(nodes)
    .join('g')

        
    node.append("circle")
        .attr("r",d => d.type === 0?0:10)
        .attr("stroke", "gray")
        .attr("fill","transparent")
    
    // horizontal line
    node.append("line")
    .filter(d => d.type === 1 || d.type === 2)
    .attr("stroke","#000")
    .attr("stroke-width", 1.5)
    .attr("x1",-5)
    .attr("y1",0)
    .attr("x2",5)
    .attr("y2",0)
    
    // verticle line
    node.append("line")
    .filter(d => d.type === 1)
    .attr("stroke","#000")
    .attr("stroke-width", 1.5)
    .attr("x1",0)
    .attr("y1",-5)
    .attr("x2",0)
    .attr("y2",5)
    




    node.call(d3.drag()
        .on("start", dragstarted)
        .on("drag", dragged)
        .on("end",dragended));


    function dragstarted(event){
        if (!event.active) simulation.alphaTarget(1).restart();
        event.subject.fx = event.subject.x;
        event.subject.fy = event.subject.y;
    }

    function dragged(event) {
        event.subject.fx = event.x;
        event.subject.fy = event.y;
    }

    function dragended(event) {
        if (!event.active) simulation.alphaTarget(0);
        event.subject.fx = null;
        event.subject.fy = null;
    }



    function ticked(){
              nodes.forEach(d => {
        d.x = Math.max(10, Math.min(boxWidth - 10, d.x));
        d.y = Math.max(10, Math.min(boxHeight - 10, d.y));
      });

        if(showLinks){
        link
            .attr("x1", d => d.source.x)
            .attr("y1", d => d.source.y)
            .attr("x2", d => d.target.x)
            .attr("y2", d => d.target.y);   
        }
 

            node
                .attr("transform", d => `translate(${d.x}, ${d.y})`)
        }

    return svg.node();

}

    const [width, height] = [300, 400];
    const [boxWidth, boxHeight] = [210, 300]
    const [boxX, boxY] = [10, 10]

const HeData = {
    nodes: [
        // 0 - nucleus
        // 1 - proton
        // 2 - electron
        // 3 - neutron
        {
            id: 0,
            type: 0
        },
        {
            id: 1,
            type: 1
        },
        {
            id: 2,
            type: 1
        },
        {
            id: 3,
            type: 2
        },
        {
            id: 4,
            type: 2
        },
        {
            id: 5,
            type: 3
        },
        {
            id: 6,
            type: 3
        },
    ],

    links: [
        {
            source: 0,
            target: 1,
            value: 1
        },
        {
            source: 0,
            target: 2,
            value: 1
        },
        {
            source: 0,
            target: 3,
            value: 60
        },
        {
            source: 0,
            target: 4,
            value: 60
        },
        {
            source: 0,
            target: 5,
            value: 1
        },
        {
            source: 0,
            target: 6,
            value: 1
        }
    ]
}

const HData = {
    nodes:[
        {
            id: 0,
            type: 0
        },
        {
            id: 1,
            type: 1
        },
        {
            id: 2,
            type: 2
        },
    ],
    links: [
        {
            source: 0,
            target: 1,
            value: 1
        },
        {
            source: 0,
            target: 2,
            value: 20
        }
    ]
}


const He = drawAtom(
    width,
    height,
    boxX,
    boxY,
    boxWidth,
    boxHeight,
    HeData.nodes,
    HeData.links,
    "He"
);

const H = drawAtom(
    width,
    height,
    boxX,
    boxY,
    boxWidth,
    boxHeight,
    HData.nodes,
    HData.links,
    "H"

);

container.append(He)
container.append(H)

</script>

<h1>Atom model examples</h1>


<div id="container"></div>
