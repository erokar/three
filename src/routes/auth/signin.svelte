<script>
	import supabase from '$lib/db'
	import { goto } from '$app/navigation'

	let email, password
	let errorMessage

	async function signIn() {
		const { session: userSession, error } = await supabase.auth.signIn({
			email,
			password
		})
		if (error) {
			errorMessage = error.message
		}
		if (userSession) {
			goto('/things')
		}
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
				<h2>Sign In</h2>
			</div>

			<div style="padding-top:30px" class="panel-body">
				<div style="display:none" id="login-alert" class="alert alert-danger col-sm-12" />

				{#if errorMessage}
					<div class="alert alert-danger" role="alert">
						{errorMessage}
					</div>
				{/if}
				<form id="loginform" class="form-horizontal" role="form" on:submit|preventDefault={signIn}>
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

					<div style="margin-top:10px" class="form-group">
						<!-- Button -->

						<div class="col-sm-12 controls">
							<button id="btn-login" class="btn btn-success">Sign In </button>
						</div>
					</div>
				</form>
			</div>
		</div>
	</div>
	<div class="mt-4">
		<a href="/auth/signup">Don't have an account?</a>
		<br />
		<a href="/magiclink">Want a magic link?</a>
	</div>
</div>
