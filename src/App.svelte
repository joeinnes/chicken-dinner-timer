<script lang="ts">
  import 'bonsai.css/dist/bonsai.min.css';

	let chickenWeight = 1500;
	let makingYorkshires = true;
	let times = {};
	let chickenReadyTime = 0;
	let cookStart = `${new Date().getHours()}:${new Date().getMinutes()}`;
	let cookStartTimestamp = new Date();
	const addTime = (timeFromInput, amount) => {
		return new Date(cookStartTimestamp.getTime() + (amount * 60000))
	}
	
	$: {
		chickenReadyTime = Math.ceil(45 * chickenWeight / 1000 + 20);
		times = {}
		times[chickenReadyTime] = ['Chicken ready', 'Veg ready', 'Potatoes ready'];
		if (chickenReadyTime >= 5) times[chickenReadyTime - 5] = ['Make gravy'];
		if (chickenReadyTime >= 10) times[chickenReadyTime - 10] = ['Chicken out of the oven', 'Veg in water', 'Flip potatoes', 'Boil kettle for gravy', 'Oven to 210'];
		if (chickenReadyTime >= 15) times[chickenReadyTime - 15] = ['Boil water for vegetables'];
		if (chickenReadyTime >= 20) {
			const tasks = ['Peel veg', 'Oven to 200'];
			if (makingYorkshires) {
				tasks.push('Yorkshires in');
			}
			times[chickenReadyTime - 20] = tasks;
		}
		if (chickenReadyTime >= 25) {
			const tasks = ['Flip potatoes', 'Baste chicken'];
			if (makingYorkshires) {
				tasks.push('Mix 140g flour and 4 eggs, then stir in 200ml milk');
			}
			times[chickenReadyTime - 25] = tasks;
		}
		if (chickenReadyTime >= 40) {
			const tasks = ['Flip potatoes', 'Baste chicken'];
			if (makingYorkshires) {
				tasks.push('Oiled Yorkshire pudding tin in the oven');
			}
			times[chickenReadyTime - 40] = tasks;
		}
		if (chickenReadyTime >= 45) times[chickenReadyTime - 45] = ['Drain and fluff potatoes', 'Potatoes into baking tray', 'Baste chicken'];
		if (chickenReadyTime >= 65) times[chickenReadyTime - 65] = ['Potatoes in cold water and high heat to boil'];
		if (chickenReadyTime >= 75) times[chickenReadyTime - 75] = ['Peel and chop potatoes'];
		const [hours, minutes] = cookStart.split(':')
		let tempCookStart = new Date()
		tempCookStart.setHours(hours);
		tempCookStart.setMinutes(minutes);
		cookStartTimestamp = tempCookStart;
	}
</script>

<article style="--levitate: 8; --bg: white; --p:2rem; --br: 0.5rem; --mx: auto; --maxw: 60ch;">
  <h1>Chicken Dinner Timer</h1>
  <label>Weight of chicken (in grams):
    <input type="numeric" bind:value={chickenWeight} />
  </label>
  <label>Cook start time:
      <input type="time" bind:value={cookStart} />
  </label>

  <label>Making Yorkshire Puddings?<br />
    <input type="radio" bind:group={makingYorkshires} name="yorkies" value={true} /> Yes
    <input type="radio" bind:group={makingYorkshires} name="yorkies" value={false} /> No
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
