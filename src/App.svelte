<script lang="ts">
  import 'bonsai.css/dist/bonsai.min.css';

	let chickenWeight = 1500;
	let isWeightValid = true;
	let isCookStartValid = true;
	let times = {};
	let chickenReadyTime = 0;
	let cookStart = `${new Date().getHours()}:${new Date().getMinutes()}`;
	let cookStartTimestamp = new Date();
	const addTime = (timeFromInput, amount) => {
		return new Date(cookStartTimestamp.getTime() + (amount * 60000))
	}
	
	$: {
		isWeightValid = chickenWeight > 0;

		if (isWeightValid) {
			chickenReadyTime = Math.ceil(45 * chickenWeight / 1000 + 20);
			times = {}
			times[chickenReadyTime] = ['Chicken ready', 'Veg ready', 'Potatoes ready'];
			if (chickenReadyTime >= 5) times[chickenReadyTime - 5] = ['Make gravy'];
			if (chickenReadyTime >= 10) times[chickenReadyTime - 10] = ['Chicken out of the oven', 'Veg in water', 'Flip potatoes', 'Boil kettle for gravy', 'Oven to 210'];
			if (chickenReadyTime >= 15) times[chickenReadyTime - 15] = ['Boil water for vegetables'];
			if (chickenReadyTime >= 20) times[chickenReadyTime - 20] = ['Peel veg', 'Oven to 200', 'Yorkshires in'];
			if (chickenReadyTime >= 25) times[chickenReadyTime - 25] = ['Flip potatoes', 'Baste chicken', 'Mix 140g flour and 4 eggs, then stir in 200ml milk'];
			if (chickenReadyTime >= 40) times[chickenReadyTime - 40] = ['Flip potatoes', 'Baste chicken', 'Oiled Yorkshire pudding tin in the oven'];
			if (chickenReadyTime >= 45) times[chickenReadyTime - 45] = ['Drain and fluff potatoes', 'Potatoes into baking tray', 'Baste chicken'];
			if (chickenReadyTime >= 65) times[chickenReadyTime - 65] = ['Potatoes in cold water and high heat to boil'];
			if (chickenReadyTime >= 75) times[chickenReadyTime - 75] = ['Peel and chop potatoes'];
		} else {
			times = {};
		}

		isCookStartValid = !!cookStart;
		if (isCookStartValid) {
			const [hours, minutes] = cookStart.split(':')
			let tempCookStart = new Date()
			tempCookStart.setHours(hours);
			tempCookStart.setMinutes(minutes);
			cookStartTimestamp = tempCookStart;
		}
	}
</script>

<article style="--levitate: 8; --bg: white; --p:2rem; --br: 0.5rem; --mx: auto; --maxw: 60ch;">
  <h1>Chicken Dinner Timer</h1>
  <label>Weight of chicken (in grams):
    <input type="number" bind:value={chickenWeight} class:invalid={!isWeightValid} />
    {#if !isWeightValid}
      <span class="error">Weight must be a positive number.</span>
    {/if}
  </label>
  <label>Cook start time:
      <input type="time" bind:value={cookStart} class:invalid={!isCookStartValid} />
      {#if !isCookStartValid}
        <span class="error">Cook start time cannot be empty.</span>
      {/if}
  </label>

  <label>Making Yorkshire Puddings?<br />
    <input type="radio" checked/> Yes
    <input type="radio" on:click|preventDefault={(e) => alert('Well you bloody should be')} /> No
  </label>

  <ul>
    {#each Object.keys(times).sort() as time}
      <li>{addTime(cookStartTimestamp, time).toLocaleTimeString('en-GB', { hour: '2-digit', minute: '2-digit' })} ({time} minutes in)
        <ul>
          {#each times[time] as activity}
            <li>{activity}</li>
          {/each}
        </ul>
      </li>
    {/each}
  </ul>
</article>

<style>
  .invalid {
    border-color: red;
  }
  .error {
    color: red;
    font-size: 0.8em;
  }
</style>
