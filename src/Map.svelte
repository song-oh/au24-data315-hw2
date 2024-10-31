<script>
    import * as d3 from "d3";
    import { legendColor } from "d3-svg-legend";

    export let data;
    export let fullData;

    let width = 600;
    let height = 700;

    let proj = d3
        .geoMercator()
        .scale(55000)
        .center([-87.39, 41.52])
        .translate([width, height]);

    let path = d3.geoPath().projection(proj);

    $: scale = d3
        .scaleSequential(d3.interpolateYlGn)
        .domain([
            d3.min(data.map((d) => +d.properties.income)),
            d3.median(data.map((d) => +d.properties.income)),
            d3.max(data.map((d) => +d.properties.income)),
        ]);

    let legend;
    $: {
        const colorLegend = legendColor().shape("rect").cells(9).scale(scale);

        d3.select(legend).call(colorLegend);
    }

    let tooltip;
    let tooltipContent = "";

    function showTooltip(event, d) {
        tooltipContent = `${d.properties.pri_neigh}: $${(+d.properties.income).toLocaleString()}`;
        tooltip.style.left = `${event.pageX + 10}px`;
        tooltip.style.top = `${event.pageY + 10}px`;
        tooltip.style.opacity = 1;
    }
    function hideTooltip() {
        tooltip.style.opacity = 0;
    }
</script>

<main>
    <svg {width} {height}>
        {#each data as d}
            <path
                style="fill: {scale(+d.properties.income)};"
                d={path(d)}
                role="img"
                aria-label={`${d.properties.pri_neigh}: $${(+d.properties.income).toLocaleString()}`}
                on:mouseover={(event) => showTooltip(event, d)}
                on:mousemove={(event) => showTooltip(event, d)}
                on:mouseout={hideTooltip}
                on:focus={(event) => showTooltip(event, d)}
                on:blur={hideTooltip}
            />
        {/each}
        {#each fullData as d}
            <path class="geooverlay" d={path(d)} />
        {/each}
        <text x="50" y={height - 425} font-size="12" font-weight="bold">
            Income Range
        </text>
        <text x="47" y={height - 410} font-size="12" font-weight="bold">
            (lowest values)
        </text>
        <g transform="translate(50, {height - 400})" bind:this={legend} />
    </svg>

    <div bind:this={tooltip} class="tooltip">
        {tooltipContent}
    </div>
</main>

<style>
    .geooverlay {
        stroke: grey;
        stroke-width: 1px;
        fill: none;
    }
    .tooltip {
        position: absolute;
        padding: 5px;
        background-color: rgba(0, 0, 0, 0.7);
        color: white;
        border-radius: 4px;
        font-size: 12px;
        pointer-events: none;
        opacity: 0;
        transition: opacity 0.2s ease;
    }
</style>
