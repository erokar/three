<script lang="ts" context="module">
	export async function load({ url, session }) {
		if (url.hash && url.hash.includes('type=recovery')) {
			const paramObject = new URLSearchParams(url.hash.substring(1))
			session['access_token'] = paramObject.get('access_token')
			return {
				status: 302,
				redirect: `/auth/changepassword`
			}
		}
		return {}
	}
</script>

<script lang="ts">
	import '../app.css'
	import supabase from '$lib/db'
	import { session, page } from '$app/stores'
	import { browser } from '$app/env'
	import { goto } from '$app/navigation'

	if (browser) {
		$session = supabase.auth.session()
		supabase.auth.onAuthStateChange((_, supaSession) => {
			$session = supaSession
		})
	}

	async function signOut() {
		await supabase.auth.signOut()
		$session = null
		goto('/auth/signin')
	}
</script>

<nav class="navbar navbar-expand-lg navbar-light fixed-top shadow-sm" id="mainNav">
	<div class="container px-5">
		<a class="navbar-brand fw-bold brand-color" href="/">Three good things</a>
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
				{#if $session}
					<li class="nav-item"><a class="nav-link me-lg-3" href="/things">My Good Things</a></li>
				{/if}
				{#if $session}
					<li class="nav-item dropdown">
						<a
							class="nav-link dropdown-toggle"
							href=""
							id="navbarDropdown"
							role="button"
							data-bs-toggle="dropdown"
							aria-expanded="false"
						>
							{$session.user?.email}
						</a>
						<ul class="dropdown-menu" aria-labelledby="navbarDropdown">
							<li><a on:click={signOut} class="dropdown-item" href="">Sign out</a></li>
							<li><a class="dropdown-item" href="/account">Account</a></li>
						</ul>
					</li>
				{:else}
					<li class="nav-item">
						<a class="nav-link me-lg-3" href="/auth/signin">Sign in</a>
					</li>
					<li class="nav-item">
						<a class="btn btn-outline-primary" href="/auth/signup">Sign up</a>
					</li>
				{/if}
			</ul>
		</div>
	</div>
</nav>
<div><slot /></div>

<style>
	.brand-color,
	.brand-color:hover {
		color: #d62929 !important;
	}
</style>
