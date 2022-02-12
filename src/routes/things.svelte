<script lang="ts">
	import { onMount } from 'svelte'
	import { session } from '$app/stores'
	import { goto } from '$app/navigation'
	import { fade, fly } from 'svelte/transition'
	import supabase from '$lib/db'

	let entries = []
	let todaysEntrySubmitted = false
	let thing = ''
	let todaysEntry = []

	onMount(async () => {
		if (!$session?.user) {
			goto('/auth/signin')
			return
		}
		const { data, error } = await supabase
			.from('things')
			.select('id, created_at, entry')
			.order('created_at', { ascending: false })
		entries = data
		todaysEntrySubmitted = entryForToday(entries)
		if (todaysEntrySubmitted) {
			todaysEntry = entries[0].entry
			entries.splice(0, 1)
			entries = entries
		}
	})

	function addThing() {
		if (!thing) {
			return
		}
		todaysEntry = [...todaysEntry, thing]
		thing = ''
		if (todaysEntry.length === 3) {
			addEntry()
		}
	}

	async function addEntry() {
		const { error } = await supabase.from('things').insert({ entry: todaysEntry })
		if (error) {
			console.error('Error adding entry: ', error.message)
		}
		todaysEntrySubmitted = true
	}

	function entryForToday(entries): boolean {
		if (!entries[0]) {
			return false
		}
		const lastEntry = new Date(entries[0].created_at)
		const today = new Date()

		console.log(
			'entry for today: ',
			today.getFullYear() === lastEntry.getFullYear() &&
				today.getMonth() === lastEntry.getMonth() &&
				today.getDate() === lastEntry.getDate()
		)

		return (
			today.getFullYear() === lastEntry.getFullYear() &&
			today.getMonth() === lastEntry.getMonth() &&
			today.getDate() === lastEntry.getDate()
		)
	}
</script>

<div class="container pt-4">
	<div class="d-flex justify-content-center">
		<div>
			<!-- <div class="spinner-border" role="status" /> -->
			<br />
			<h2>Today</h2>
			{#if !todaysEntrySubmitted}
				<div class="input-group">
					<input
						bind:value={thing}
						id="newThing"
						type="text"
						class="form-control"
						on:keydown={(event) => (event.key === 'Enter' ? addThing() : null)}
					/>
					<div class="input-group-append">
						<button class="btn btn-outline-secondary" on:click={addThing}>Add</button>
					</div>
				</div>
			{/if}

			{#each todaysEntry as thing, i}
				<div in:fade class="lead fw-normal text-muted">
					{i + 1}. {thing}
				</div>
			{/each}

			{#if todaysEntrySubmitted}
				<svg
					xmlns="http://www.w3.org/2000/svg"
					width="60"
					height="60"
					fill="green"
					class="bi bi-check-lg"
					viewBox="0 0 16 16"
					in:fade
				>
					<path
						d="M12.736 3.97a.733.733 0 0 1 1.047 0c.286.289.29.756.01 1.05L7.88 12.01a.733.733 0 0 1-1.065.02L3.217 8.384a.757.757 0 0 1 0-1.06.733.733 0 0 1 1.047 0l3.052 3.093 5.4-6.425a.247.247 0 0 1 .02-.022Z"
					/>
				</svg>
			{/if}

			<i class="bi bi-check-lg" />

			<br /><br />
			<hr />
			<br />

			{#each entries as entry, i}
				<div class="mb-4">
					<h5>
						{new Date(entry.created_at).toLocaleDateString('en-us', {
							weekday: 'long',
							year: 'numeric',
							month: 'short',
							day: 'numeric'
						})}
					</h5>
					{#each entry.entry as thing, i}
						<div class="lead fw-normal text-muted">{i + 1}. {thing}</div>
					{/each}
				</div>
			{/each}
		</div>
	</div>
</div>
