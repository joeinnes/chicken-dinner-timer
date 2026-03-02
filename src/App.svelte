<script lang="ts">
  let chickenWeightStr: string = '1500';
  $: chickenWeight = Math.max(1, parseInt(chickenWeightStr) || 1);
  let times: Record<number, string[]> = {};
  let chickenReadyTime: number = 0;
  let cookStart: string = `${String(new Date().getHours()).padStart(2, '0')}:${String(new Date().getMinutes()).padStart(2, '0')}`;
  let cookStartTimestamp: Date = new Date();

  const addTime = (base: Date, amount: number): Date => {
    return new Date(base.getTime() + amount * 60000);
  };

  $: {
    chickenReadyTime = Math.ceil(45 * chickenWeight / 1000 + 20);
    times = {};
    times[chickenReadyTime]      = ['Chicken ready', 'Veg ready', 'Potatoes ready'];
    times[chickenReadyTime - 5]  = ['Make gravy'];
    times[chickenReadyTime - 10] = ['Chicken out of the oven', 'Veg in water', 'Flip potatoes', 'Boil kettle for gravy', 'Oven to 210°C'];
    times[chickenReadyTime - 15] = ['Boil water for vegetables'];
    times[chickenReadyTime - 20] = ['Peel veg', 'Oven to 200°C', 'Yorkshires in'];
    times[chickenReadyTime - 25] = ['Flip potatoes', 'Baste chicken', 'Mix 140g flour and 4 eggs, then stir in 200ml milk'];
    times[chickenReadyTime - 40] = ['Flip potatoes', 'Baste chicken', 'Oiled Yorkshire pudding tin in the oven'];
    times[chickenReadyTime - 45] = ['Drain and fluff potatoes', 'Potatoes into baking tray', 'Baste chicken'];
    times[chickenReadyTime - 65] = ['Potatoes in cold water and high heat to boil'];
    times[chickenReadyTime - 75] = ['Peel and chop potatoes'];
    const [hours, minutes] = cookStart.split(':');
    const t = new Date();
    t.setHours(parseInt(hours), parseInt(minutes), 0, 0);
    cookStartTimestamp = t;
  }

  $: serveAt = addTime(cookStartTimestamp, chickenReadyTime)
    .toLocaleTimeString('en-GB', { hour: '2-digit', minute: '2-digit' });
</script>

<svelte:head>
  <style>
    input[type='number']::-webkit-inner-spin-button,
    input[type='number']::-webkit-outer-spin-button { -webkit-appearance: none; margin: 0; }
    input[type='number'] { -moz-appearance: textfield; }
  </style>
</svelte:head>

<div class="page">
  <main>

    <header>
      <p class="kicker">The Perfect</p>
      <h1>Chicken<br/><em>Dinner</em></h1>
      <p class="descriptor">Oven roasted · serves a feast</p>
    </header>

    <div class="card">
      <section class="inputs">
        <label class="field">
          <span class="field-label">Bird weight</span>
          <div class="input-wrap">
            <input type="text" inputmode="numeric" pattern="[0-9]*" bind:value={chickenWeightStr} />
            <span class="unit">g</span>
          </div>
        </label>

        <div class="field-divider"></div>

        <label class="field">
          <span class="field-label">Into the oven</span>
          <input type="time" bind:value={cookStart} />
        </label>
      </section>

      <div class="card-rule"></div>

      <fieldset class="yorkshires">
        <legend class="field-label">Making Yorkshire puddings?</legend>
        <div class="radio-group">
          <label class="radio-opt">
            <input type="radio" name="yorkshires" checked /> Yes, obviously
          </label>
          <label class="radio-opt">
            <input type="radio" name="yorkshires" on:click|preventDefault={() => alert('Well you bloody should be')} /> No
          </label>
        </div>
      </fieldset>
    </div>

    <div class="serve-strip">
      <span class="serve-left">
        <span class="serve-label">On the table</span>
        <span class="serve-time">{serveAt}</span>
      </span>
      <span class="serve-right">{chickenReadyTime} minutes total</span>
    </div>

    <section class="timeline">
      <p class="timeline-heading">Your schedule</p>
      <ol class="timeline-list">
        {#each Object.keys(times).sort((a, b) => Number(a) - Number(b)) as time}
          {@const isReady = Number(time) === chickenReadyTime}
          <li class="row" class:is-ready={isReady}>
            <div class="time-col">
              <span class="clock">{addTime(cookStartTimestamp, Number(time)).toLocaleTimeString('en-GB', { hour: '2-digit', minute: '2-digit' })}</span>
              <span class="min-label">+{time}m</span>
            </div>
            <div class="track-col">
              <span class="dot"></span>
              {#if !isReady}<span class="rule"></span>{/if}
            </div>
            <ul class="tasks">
              {#each times[Number(time)] as task}
                <li>{task}</li>
              {/each}
            </ul>
          </li>
        {/each}
      </ol>
    </section>

  </main>
</div>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Abril+Fatface&family=Caveat:wght@400;600;700&family=Lora:ital,wght@0,400;0,500;1,400&display=swap');

  :global(*, *::before, *::after) { box-sizing: border-box; margin: 0; padding: 0; }

  :global(body) {
    background: #141008;
    min-height: 100vh;
    font-family: 'Lora', Georgia, serif;
  }

  /* ── PAGE WRAPPER ── */

  .page {
    min-height: 100vh;
    background:
      url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='300' height='300'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.75' numOctaves='4' stitchTiles='stitch'/%3E%3CfeColorMatrix type='saturate' values='0'/%3E%3C/filter%3E%3Crect width='300' height='300' filter='url(%23n)' opacity='0.07'/%3E%3C/svg%3E"),
      radial-gradient(ellipse at 20% 0%, #2e1f08 0%, transparent 60%),
      radial-gradient(ellipse at 80% 100%, #1a2208 0%, transparent 60%),
      #141008;
  }

  main {
    max-width: 480px;
    margin: 0 auto;
    padding: 2.75rem 1.5rem 6rem;
  }

  /* ── HEADER ── */

  header {
    margin-bottom: 2rem;
    padding-bottom: 2rem;
    border-bottom: 1px solid #3a2e18;
    position: relative;
  }

  header::after {
    content: '';
    position: absolute;
    bottom: -4px;
    left: 0;
    right: 0;
    height: 1px;
    background: #2a2010;
  }

  .kicker {
    font-family: 'Caveat', cursive;
    font-size: 1.3rem;
    font-weight: 600;
    color: #e8a830;
    letter-spacing: 0.02em;
    margin-bottom: 0.2rem;
    line-height: 1;
  }

  h1 {
    font-family: 'Abril Fatface', Georgia, serif;
    font-size: clamp(3.5rem, 14vw, 5.5rem);
    line-height: 0.9;
    color: #f5ead0;
    margin-bottom: 0.75rem;
    letter-spacing: -0.01em;
  }

  h1 em {
    font-style: italic;
    color: #e8a830;
  }

  .descriptor {
    font-family: 'Caveat', cursive;
    font-size: 1rem;
    color: #7a6a40;
    letter-spacing: 0.06em;
  }

  /* ── INPUTS CARD ── */

  .card {
    background: #1e1810;
    border: 1px solid #3a2e18;
    border-bottom: 3px solid #3a2e18;
    margin-bottom: 0;
  }

  .inputs {
    display: grid;
    grid-template-columns: 1fr auto 1fr;
    align-items: stretch;
  }

  .field {
    display: flex;
    flex-direction: column;
    padding: 1.1rem 1.25rem;
    cursor: text;
  }

  .field-label {
    font-family: 'Caveat', cursive;
    font-size: 0.95rem;
    font-weight: 600;
    color: #7a6a40;
    letter-spacing: 0.04em;
    margin-bottom: 0.25rem;
    display: block;
  }

  .field-divider {
    width: 1px;
    background: #3a2e18;
    margin: 0.75rem 0;
  }

  .input-wrap {
    position: relative;
  }

  .unit {
    position: absolute;
    right: 0;
    top: 50%;
    transform: translateY(-50%);
    color: #7a6a40;
    font-family: 'Caveat', cursive;
    font-size: 1.1rem;
    pointer-events: none;
  }


  input[type='text'],
  input[type='time'] {
    width: 100%;
    padding: 0;
    font-family: 'Abril Fatface', Georgia, serif;
    font-size: 2rem;
    line-height: 1;
    background: transparent;
    border: none;
    color: #f5ead0;
    appearance: none;
    -webkit-appearance: none;
    transition: color 0.15s;
  }

  input[type='text']:focus,
  input[type='time']:focus {
    outline: none;
    color: #e8a830;
  }

  .card-rule {
    height: 1px;
    background: #3a2e18;
    margin: 0 1.25rem;
  }

  /* Yorkshire row */

  .yorkshires {
    border: none;
    padding: 0.9rem 1.25rem;
    background: transparent;
    display: flex;
    align-items: center;
    gap: 1rem;
    flex-wrap: wrap;
  }

  .yorkshires legend {
    float: left;
    margin-right: 1rem;
  }

  .radio-group {
    display: flex;
    gap: 1.25rem;
    flex-wrap: wrap;
  }

  .radio-opt {
    display: flex;
    align-items: center;
    gap: 0.4rem;
    font-family: 'Caveat', cursive;
    font-size: 1rem;
    color: #c8b888;
    cursor: pointer;
    white-space: nowrap;
  }

  input[type='radio'] {
    width: 1rem;
    height: 1rem;
    accent-color: #e8a830;
    cursor: pointer;
  }

  /* ── SERVE STRIP ── */

  .serve-strip {
    background: #e8a830;
    padding: 0.75rem 1.25rem;
    display: flex;
    justify-content: space-between;
    align-items: baseline;
    margin-bottom: 2.5rem;
  }

  .serve-left {
    display: flex;
    flex-direction: column;
    gap: 0.1rem;
  }

  .serve-label {
    font-family: 'Caveat', cursive;
    font-size: 0.8rem;
    font-weight: 600;
    color: #5a3a00;
    letter-spacing: 0.06em;
    line-height: 1;
  }

  .serve-time {
    font-family: 'Abril Fatface', serif;
    font-size: 2rem;
    color: #1e1408;
    line-height: 1;
  }

  .serve-right {
    font-family: 'Caveat', cursive;
    font-size: 0.9rem;
    font-weight: 600;
    color: #5a3a00;
    opacity: 0.8;
  }

  /* ── TIMELINE ── */

  .timeline-heading {
    font-family: 'Caveat', cursive;
    font-size: 0.85rem;
    font-weight: 700;
    letter-spacing: 0.18em;
    text-transform: uppercase;
    color: #7a6a40;
    margin-bottom: 1.25rem;
  }

  .timeline-list {
    list-style: none;
  }

  .row {
    display: grid;
    grid-template-columns: 4.25rem 1.75rem 1fr;
    min-height: 3rem;
  }

  .time-col {
    text-align: right;
    padding-right: 0.875rem;
    padding-top: 0.05rem;
  }

  .clock {
    display: block;
    font-family: 'Abril Fatface', Georgia, serif;
    font-size: 1.1rem;
    color: #f5ead0;
    line-height: 1.15;
  }

  .is-ready .clock {
    color: #e8a830;
    font-size: 1.25rem;
  }

  .min-label {
    display: block;
    font-family: 'Caveat', cursive;
    font-size: 0.75rem;
    color: #5a4e28;
    letter-spacing: 0.04em;
    line-height: 1;
  }

  .track-col {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .dot {
    flex-shrink: 0;
    width: 11px;
    height: 11px;
    border-radius: 50%;
    background: #1e1810;
    border: 2px solid #5a4e28;
    margin-top: 0.3rem;
  }

  .is-ready .dot {
    width: 15px;
    height: 15px;
    background: #e8a830;
    border-color: #e8a830;
    margin-top: 0.15rem;
    box-shadow: 0 0 0 3px #141008, 0 0 0 5px #e8a83040;
  }

  .rule {
    flex: 1;
    width: 1px;
    background: #3a2e18;
    margin-top: 3px;
    margin-bottom: -1px;
  }

  .tasks {
    list-style: none;
    padding: 0.05rem 0 1.5rem 0.75rem;
  }

  .tasks li {
    font-size: 0.875rem;
    line-height: 1.65;
    color: #c8b888;
    padding-left: 0.85rem;
    position: relative;
  }

  .tasks li::before {
    content: '–';
    position: absolute;
    left: 0;
    color: #5a4e28;
  }

  .is-ready .tasks {
    padding-bottom: 0;
  }

  .is-ready .tasks li {
    font-size: 0.95rem;
    font-weight: 500;
    color: #e8a830;
  }

  .is-ready .tasks li::before {
    content: '✓';
    font-size: 0.65rem;
    top: 0.22em;
    color: #e8a830;
  }

  @media (max-width: 400px) {
    .inputs { grid-template-columns: 1fr; }
    .field-divider { height: 1px; width: auto; margin: 0 1.25rem; }
    h1 { font-size: 3rem; }
  }
</style>
