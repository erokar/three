<script context="module">
	import supabase from '$lib/db'

	export async function load({ params, fetch, session, stuff }) {
		const response = await supabase
			.from('things')
			.select('id, created_at, entry')
			.order('created_at', { ascending: false })
		const entries = response.data

		return {
			props: {
				entries
			}
		}
	}
</script>

<script lang="ts">
	export let entries

	console.log('entries ', entries)

	let thing = ''
	let entry = []

	let todaysEntrySubmitted = entryForToday()
	console.log('showEntryInput ', todaysEntrySubmitted)

	function addThing() {
		entry = [...entry, thing]
		thing = ''
		if (entry.length === 3) {
			addEntry()
		}
	}

	async function addEntry() {
		const newEntry = await supabase.from('things').insert({ entry })
		entry = []
		const response = await supabase
			.from('things')
			.select('id, created_at, entry')
			.order('created_at', { ascending: false })
		entries = response.data
		todaysEntrySubmitted = entryForToday()
	}

	function entryForToday(): boolean {
		if (!entries[0]) {
			console.log('return false')
			return false
		}
		const lastEntry = new Date(entries[0].created_at)
		const today = new Date()
		return (
			today.getFullYear() === lastEntry.getFullYear() &&
			today.getMonth() === lastEntry.getMonth() &&
			today.getDate() === lastEntry.getDate()
		)
	}
</script>

<div class="container pt-4">
	Things
	<br />
	<label for="newThing">
		<input bind:value={thing} id="newThing" type="text" />
	</label>
	<button on:click={addThing}>Add</button>

	{#each entry as thing}
		<div>{thing}</div>
	{/each}

	<div>
		{#each entries as entry, i}
			<div>
				{#if todaysEntrySubmitted && i === 0}
					Today
				{:else}
					{new Date(entry.created_at).toLocaleDateString('en-us', {
						weekday: 'long',
						year: 'numeric',
						month: 'short',
						day: 'numeric'
					})}
				{/if}
			</div>
			{#each entry.entry as thing, i}
				<div>{i + 1}. {thing}</div>
			{/each}
		{/each}
	</div>
</div>
