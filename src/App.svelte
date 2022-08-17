<script lang="ts">
  import 'bonsai.css/dist/bonsai.min.css';

	let chickenWeight = 1500;
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
		times[chickenReadyTime - 5] = ['Make gravy'];
		times[chickenReadyTime - 10] = ['Chicken out of the oven', 'Veg in water', 'Flip potatoes', 'Boil kettle for gravy', 'Oven to 210'];
		times[chickenReadyTime - 15] = ['Boil water for vegetables'];
		times[chickenReadyTime - 20] = ['Peel veg', 'Oven to 200', 'Yorkshires in'];
		times[chickenReadyTime - 25] = ['Flip potatoes', 'Baste chicken', 'Mix 140g flour and 4 eggs, then stir in 200ml milk'];
		times[chickenReadyTime - 40] = ['Flip potatoes', 'Baste chicken', 'Oiled Yorkshire pudding tin in the oven'];
		times[chickenReadyTime - 45] = ['Drain and fluff potatoes', 'Potatoes into baking tray', 'Baste chicken'];
		times[chickenReadyTime - 65] = ['Potatoes in cold water and high heat to boil'];
		times[chickenReadyTime - 75] = ['Peel and chop potatoes'];
		const [hours, minutes] = cookStart.split(':')
		let tempCookStart = new Date()
		tempCookStart.setHours(hours);
		tempCookStart.setMinutes(minutes);
		cookStartTimestamp = tempCookStart;
	}
</script>

<h1>Chicken Dinner Timer</h1>
<label>Weight of chicken (in grams):
	<input type="numeric" bind:value={chickenWeight} />
</label>
<label>Cook start time:
		<input type="time" bind:value={cookStart} />
</label>

<label>Making Yorkshire Puddings?
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