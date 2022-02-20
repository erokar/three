<script lang="ts">
	import supabase from '$lib/db'
	import { session } from '$app/stores'

	let email: string
	let password: string
	let passwordConfirmation: string
	let errorMessage: string
	let successMessage: string
	let awaiting: boolean = false

	async function signUp() {
		if (!email || !password || !passwordConfirmation) {
			errorMessage = 'All fields must be filled in.'
			return
		}
		if (password !== passwordConfirmation) {
			errorMessage = 'Password and password confirmation differ.'
			return
		}
		awaiting = true
		const { session: userSession, error } = await supabase.auth.signUp({
			email,
			password
		})
		if (error) {
			errorMessage = error.message
			awaiting = false
			return
		}
		awaiting = false
		errorMessage = null
		successMessage = `Sign in email sent to ${email}.`
		$session = userSession
	}
</script>

<div class="container pt-2">
	<div
		id="loginbox"
		style="margin-top:50px;"
		class="mainbox col-md-6 col-md-offset-3 col-sm-8 col-sm-offset-2"
	>
		<div class="panel panel-info">
			<div class="panel-heading">
				<h2>Sign Up</h2>
			</div>

			<div style="padding-top:30px" class="panel-body">
				<div style="display:none" id="login-alert" class="alert alert-danger col-sm-12" />

				{#if errorMessage}
					<div class="alert alert-danger" role="alert">
						{errorMessage}
					</div>
				{/if}
				{#if successMessage}
					<div class="alert alert-success" role="alert">{successMessage}</div>
				{/if}
				<form id="loginform" class="form-horizontal" on:click|preventDefault>
					<div style="margin-bottom: 25px" class="input-group">
						<span class="input-group-addon"><i class="glyphicon glyphicon-user" /></span>
						<input
							bind:value={email}
							id="login-username"
							type="text"
							class="form-control"
							name="username"
							placeholder="Email"
						/>
					</div>

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

					<div style="margin-top:10px" class="form-group">
						<!-- Button -->

						<div class="col-sm-12 controls">
							<button on:click={signUp} id="btn-login" class="btn btn-success">
								{#if awaiting}
									<span class="spinner-border spinner-border-sm" role="status" aria-hidden="true" />
								{/if}
								<span class="m-2">Sign Up</span>
							</button>
						</div>
					</div>
				</form>
			</div>
		</div>
	</div>
</div>
