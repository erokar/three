<script lang="ts">
	import { onMount } from 'svelte'
	import { session } from '$app/stores'
	import { goto } from '$app/navigation'
	import supabase from '$lib/db'

	let password = ''
	let passwordConfirmation = ''
	let errorMessage = null
	let successMessage = null
	let awaiting = false

	onMount(async () => {
		if (!$session?.user) {
			goto('/auth/signin')
			return
		}
	})

	async function changePassword() {
		awaiting = true
		const { error } = await supabase.auth.api.updateUser($session['access_token'], {
			password
		})
		if (error) {
			console.error(error.message)
			errorMessage = `Could not change password. ${error.message}.`
			awaiting = false
			return
		}
		awaiting = false
		delete $session['access_token']
		successMessage = `Password has been updated.`
	}
</script>

<div class="container pt-4">
	<div
		id="passwordBox"
		style="margin-top:50px;"
		class="mainbox col-md-6 col-md-offset-3 col-sm-8 col-sm-offset-2"
	>
		<h3>Change password</h3>
		<div class="panel panel-info">
			<div style="padding-top:30px" class="panel-body">
				{#if errorMessage}
					<div class="alert alert-danger" role="alert">
						{errorMessage}
					</div>
				{/if}
				{#if successMessage}
					<div class="alert alert-success" role="alert">{successMessage}</div>
				{/if}
				<form id="changePasswordForm" class="form-horizontal" on:click|preventDefault>
					<div style="margin-bottom: 25px" class="input-group">
						<span class="input-group-addon"><i class="glyphicon glyphicon-lock" /></span>
						<input
							bind:value={password}
							id="login-password"
							type="password"
							class="form-control"
							name="password"
							placeholder="Password"
						/>
					</div>

					<div style="margin-bottom: 25px" class="input-group">
						<span class="input-group-addon"><i class="glyphicon glyphicon-lock" /></span>
						<input
							bind:value={passwordConfirmation}
							id="login-password-confirmation"
							type="password"
							class="form-control"
							name="passwordConfirmation"
							placeholder="Password confirmation"
						/>
					</div>
					<div class="col-sm-12 controls">
						<button on:click={changePassword} id="btn-login" class="btn btn-success">
							{#if awaiting}
								<span class="spinner-border spinner-border-sm" role="status" aria-hidden="true" />
							{/if}
							<span class="m-2">Submit</span>
						</button>
					</div>
				</form>
			</div>
		</div>
	</div>
</div>
