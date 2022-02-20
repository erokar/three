<script lang="ts">
	import { onMount } from 'svelte'
	import { session } from '$app/stores'
	import { goto } from '$app/navigation'
	import supabase from '$lib/db'

	let successMessage = null
	let awaiting = false

	onMount(async () => {
		if (!$session?.user) {
			goto('/auth/signin')
			return
		}
	})

	async function resetPassword() {
		awaiting = true
		const { error } = await supabase.auth.api.resetPasswordForEmail($session.user?.email, {
			redirectTo: '/auth/changepassword'
		})
		if (error) {
			alert(error.message)
			awaiting = false
			return
		}
		awaiting = false
		successMessage = `A password reset link has been sent to ${$session.user?.email}.`
	}
</script>

<div class="container pt-4">
	<h2>Account</h2>
	<button on:click={resetPassword} class="btn btn-primary">
		{#if awaiting}
			<span class="spinner-border spinner-border-sm" role="status" aria-hidden="true" />
		{/if}
		Reset password
	</button>
	{#if successMessage}
		<div class="alert alert-success mt-2" role="alert">{successMessage}</div>
	{/if}
</div>
