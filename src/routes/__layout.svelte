<script lang="ts">
	import '../app.css'
	import supabase from '$lib/db'
	import { session } from '$app/stores'
	import { browser } from '$app/env'
	import { goto } from '$app/navigation'

	if (browser) {
		$session = supabase.auth.session()
		supabase.auth.onAuthStateChange((_, supaSession) => {
			$session = supaSession
			console.log('settign session', $session)
		})
	}

	async function signOut() {
		await supabase.auth.signOut()
		$session = null
		goto('/')
	}
</script>

<nav class="navbar navbar-expand-lg navbar-light fixed-top shadow-sm" id="mainNav">
	<div class="container px-5">
		<a class="navbar-brand fw-bold" href="/">Three good things</a>
		<button
			class="navbar-toggler"
			type="button"
			data-bs-toggle="collapse"
			data-bs-target="#navbarResponsive"
			aria-controls="navbarResponsive"
			aria-expanded="false"
			aria-label="Toggle navigation"
		>
			Menu
			<i class="bi-list" />
		</button>
		<div class="collapse navbar-collapse" id="navbarResponsive">
			<ul class="navbar-nav ms-auto me-4 my-3 my-lg-0">
				<li class="nav-item"><a class="nav-link me-lg-3" href="/">About</a></li>
				{#if $session}
					<li class="nav-item"><a class="nav-link me-lg-3" href="/things">Good things</a></li>
				{/if}
				<li class="nav-item">
					<a class="nav-link me-lg-3" href="">{$session ? $session.user?.email : ''}</a>
				</li>
				{#if $session}
					<li class="nav-item">
						<a on:click={signOut} class="nav-link me-lg-3" href="">Sign out</a>
					</li>
				{:else}
					<li class="nav-item">
						<a on:click={signOut} class="nav-link me-lg-3" href="/auth/signin">Sign in</a>
					</li>
					<li class="nav-item">
						<a on:click={signOut} class="nav-link me-lg-3" href="/auth/signup">Sign up</a>
					</li>
				{/if}
			</ul>
			<button
				class="btn btn-primary rounded-pill px-3 mb-2 mb-lg-0"
				data-bs-toggle="modal"
				data-bs-target="#feedbackModal"
			>
				<span class="d-flex align-items-center">
					<span class="small">Send Feedback</span>
				</span>
			</button>
		</div>
	</div>
</nav>
<div><slot /></div>
