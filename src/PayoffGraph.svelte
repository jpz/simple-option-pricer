<script>
  export let strikePrice;
  export let putCall;
  export let stockPrice;

  import { scaleLinear } from "d3-scale";
  //import points from './data.js';
  import _ from "lodash";

  const yTicks = _.range(0, 100, 10);
  const xTicks = _.range(0, 100, 10);
  const padding = { top: 20, right: 15, bottom: 20, left: 25 };

  let width = 500;
  let height = 200;

  $: points = [
    { x: 0, y: putCall == "call" ? 0 : strikePrice },
    { x: strikePrice, y: 0 },
    { x: 100, y: putCall == "call" ? 100 - strikePrice : 0 }
  ];

  $: xScale = scaleLinear()
    .domain([minX, maxX])
    .range([padding.left, width - padding.right]);

  $: yScale = scaleLinear()
    .domain([Math.min.apply(null, yTicks), Math.max.apply(null, yTicks)])
    .range([height - padding.bottom, padding.top]);

  $: minX = points[0].x;
  $: maxX = points[points.length - 1].x;
  $: path = `M${points.map(p => `${xScale(p.x)},${yScale(p.y)}`).join("L")}`;
  $: area = `${path}L${xScale(maxX)},${yScale(0)}L${xScale(minX)},${yScale(
    0
  )}Z`;
  $: stockPricePath = `M${[{ x: stockPrice, y: 0 }, { x: stockPrice, y: 100 }]
    .map(p => `${xScale(p.x)},${yScale(p.y)}`)
    .join("L")}`;

  function formatMobile(tick) {
    return "'" + tick.toString().slice(-2);
  }
</script>

<style>
  .chart,
  h2,
  p {
    width: 100%;
    max-width: 500px;
    margin-left: auto;
    margin-right: auto;
  }

  svg {
    position: relative;
    width: 100%;
    height: 200px;
    overflow: visible;
  }

  .tick {
    font-size: 0.725em;
    font-weight: 200;
  }

  .tick line {
    stroke: #aaa;
    stroke-dasharray: 2;
  }

  .tick text {
    fill: #666;
    text-anchor: start;
  }

  .tick.tick-0 line {
    stroke-dasharray: 0;
  }

  .x-axis .tick text {
    text-anchor: middle;
  }

  .path-line {
    fill: none;
    stroke: rgb(0, 100, 100);
    stroke-linejoin: round;
    stroke-linecap: round;
    stroke-width: 2;
  }

  .path-stock-price {
    fill: none;
    stroke: rgb(243, 16, 8);
    stroke-linejoin: round;
    stroke-linecap: round;
    stroke-width: 2;
  }

  .path-area {
    fill: rgba(0, 100, 100, 0.2);
  }
</style>

<h2>Option Payoff at Expiry</h2>

<div class="chart" bind:clientWidth={width} bind:clientHeight={height}>
  <svg>
    <!-- y axis -->
    <g class="axis y-axis" transform="translate(0, {padding.top})">
      {#each yTicks as tick}
        <g
          class="tick tick-{tick}"
          transform="translate(0, {yScale(tick) - padding.bottom})">
          <line x2="100%" />
          <text y="-4">{tick} {tick === 8 ? ' million sq km' : ''}</text>
        </g>
      {/each}
    </g>

    <!-- x axis -->
    <g class="axis x-axis">
      {#each xTicks as tick}
        <g
          class="tick tick-{tick}"
          transform="translate({xScale(tick)},{height})">
          <line y1="-{height}" y2="-{padding.bottom}" x1="0" x2="0" />
          <text y="-2">{width > 380 ? tick : formatMobile(tick)}</text>
        </g>
      {/each}
    </g>

    <!-- data -->
    <path class="path-area" d={area} />
    <path class="path-line" d={path} />
    <path class="path-stock-price" d={stockPricePath} />
  </svg>
</div>
