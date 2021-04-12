<script>
  import blackScholes from "black-scholes";
  import PayoffGraph from "./PayoffGraph.svelte";
  import OptionPriceGraph from "./OptionPriceGraph.svelte";

  let stockPrice = 50;
  let strikePrice = 50;
  let daysToMaturity = 50;
  let putCall = "call";
  let interestRate = 0.02;
  let volatility = 0.2;
</script>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  .parametergrid {
    display: grid;
    grid-template-columns: 0.25fr 1fr 1fr;
    grid-template-rows: 1fr 1fr 1fr 1fr 1fr 1fr;
    border-style: groove;
    padding-left: 5%;
    padding-right: 5%;
  }

  .graphgrid {
    display: grid;
    grid-template-columns: 1fr 1fr;
  }

  h1 {
    color: #ff3e00;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>

<main>
  <h1>Simple Option Pricer</h1>
  <p>
    Source code on
    <a href="https://github.com/jpz/simple-option-pricer">Github</a>
  </p>
  <div class="parametergrid">

    <div>Stock Price:</div>
    <div>${stockPrice}</div>
    <input type="range" bind:value={stockPrice} min="0" max="100" step="0.1" />
    <div>Strike Price:</div>
    <div>${strikePrice}</div>
    <input type="range" bind:value={strikePrice} min="0" max="100" step="5" />
    <div>Days to Maturity:</div>
    <div>{daysToMaturity} days</div>
    <input
      type="range"
      bind:value={daysToMaturity}
      min="0"
      max="500"
      step="1" />
    <div>Volatility:</div>
    <div>{(volatility * 100).toFixed(2)}%</div>
    <input type="range" bind:value={volatility} min="0" max="5" step="0.01" />
    <div>Put/Call:</div>
    <div>{putCall}</div>
    <div>
      <label>
        <input type="radio" bind:group={putCall} value="put" />
        Put
      </label>

      <label>
        <input type="radio" bind:group={putCall} value="call" />
        Call
      </label>
    </div>
    <div>InterestRate:</div>
    <div>{(interestRate * 100).toFixed(2)}%</div>
    <input
      type="range"
      bind:value={interestRate}
      min="0"
      max=".05"
      step=".0025" />
    <div>Price:</div>
    ${blackScholes
      .blackScholes(
        stockPrice,
        strikePrice,
        daysToMaturity / 365.35,
        volatility,
        interestRate,
        putCall
      )
      .toFixed(2)};
  </div>

  <div class="graphgrid">
    <div>
      <PayoffGraph {putCall} {strikePrice} {stockPrice} />
    </div>
    <div>
      <OptionPriceGraph
        {stockPrice}
        {strikePrice}
        {daysToMaturity}
        {putCall}
        {interestRate}
        {volatility} />
    </div>
  </div>
</main>
