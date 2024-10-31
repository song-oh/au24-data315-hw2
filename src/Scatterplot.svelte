<script>
    import * as d3 from "d3";

    export let data;
    export let fullData;
    export let xVariable;
    export let yVariable;
    export let filter;
    export let update;

    let margin = { top: 10, right: 30, bottom: 30, left: 40 };
    let width = 360;
    let height = 180;
    let chartW = width - margin.left - margin.right;
    let chartH = height - margin.top - margin.bottom;

    let brushLayer;
    let xAxis;
    let yAxis;

    const brush = d3
        .brush()
        .extent([
            [0, 0],
            [chartW, chartH],
        ])
        .on("brush", brushed)
        .on("end", brushended);

    function brushed(event) {
        if (event && event.selection) {
            const [[x0, y0], [x1, y1]] = event.selection;
            filter = [
                xScale.invert(x0),
                xScale.invert(x1),
                yScale.invert(y1),
                yScale.invert(y0),
            ];
            update();
        }
    }

    function brushended(event) {
        if (event && !event.selection) {
            filter = [];
            update();
        }
    }

    $: xScale = d3
        .scaleLinear()
        .range([0, chartW])
        .domain([0, d3.max(fullData, (d) => d.properties[xVariable]) * 1.05]);
    $: yScale = d3
        .scaleLinear()
        .range([chartH, 0])
        .domain([0, d3.max(fullData, (d) => d.properties[yVariable]) * 1.05]);

    $: backgroundPoints = fullData.map((d) => ({
        x: xScale(d.properties[xVariable]),
        y: yScale(d.properties[yVariable]),
    }));
    $: points = data.map((d) => ({
        x: xScale(d.properties[xVariable]),
        y: yScale(d.properties[yVariable]),
    }));

    $: {
        d3.select(brushLayer).call(brush);
        d3.select(xAxis).call(d3.axisBottom(xScale));
        d3.select(yAxis).call(d3.axisLeft(yScale));
    }
</script>

<main>
    <svg {width} {height}>
        <g transform="translate({margin.left}, {margin.top})">
            {#each backgroundPoints as p}
                <circle class="backgroundpoint" cx={p.x} cy={p.y} r="5" />
            {/each}
            {#each points as p}
                <circle class="point" cx={p.x} cy={p.y} r="5" />
            {/each}
        </g>

        <g
            transform="translate({margin.left}, {margin.top})"
            bind:this={brushLayer}
        />

        <g
            transform="translate({margin.left}, {chartH + margin.top})"
            bind:this={xAxis}
        />

        <g
            transform="translate({margin.left}, {margin.top})"
            bind:this={yAxis}
        />
    </svg>
</main>

<style>
    .backgroundpoint {
        fill: grey;
        opacity: 0.4;
    }

    .point {
        fill: maroon;
    }

    circle {
        stroke: white;
        stroke-width: 1px;
    }
</style>
