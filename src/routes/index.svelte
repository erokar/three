<script>
	import { onMount } from 'svelte'
	import { fade } from 'svelte/transition'

	let steps

	onMount(() => (steps = [1]))

	function typewriter(node, { speed = 1 }) {
		const valid = node.childNodes.length === 1 && node.childNodes[0].nodeType === Node.TEXT_NODE

		if (!valid) {
			throw new Error(`This transition only works on elements with a single text node child`)
		}

		const text = node.textContent
		const duration = text.length / (speed * 0.01)

		return {
			duration,
			tick: (t) => {
				const i = Math.trunc(text.length * t)
				node.textContent = text.slice(0, i)
			}
		}
	}
</script>

<header class="masthead">
	<div class="container px-5 ">
		<div class="row gx-5 align-text-top">
			<div class="col-lg-12">
				<h1 class="display-1 lh-1 mb-3">Three good tings. <br /> Every day.</h1>
			</div>
			<div class="col-lg-6 ml-4 mt-5  pl-5 ml-5">
				{#if steps.includes(1)}
					<h3 class="pt-8">
						1. <span
							in:typewriter
							on:introend={() => setTimeout(() => (steps = [...steps, 2]), 1200)}
							>Had a good laugh with Sue when we met for coffee this morning.</span
						>
					</h3>
				{/if}
				{#if steps.includes(2)}
					<h3>
						2. <span
							in:typewriter
							on:introend={() => setTimeout(() => (steps = [...steps, 3]), 1200)}
							>Got a solid bit of writing done today.</span
						>
					</h3>
				{/if}
				{#if steps.includes(3)}
					<h3>
						3. <span
							in:typewriter
							on:introend={() => setTimeout(() => (steps = [...steps, 4]), 400)}
							>The sunset was beautiful, pink and crazy.</span
						>
					</h3>
				{/if}
				{#if steps.includes(4)}
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
			</div>
			<div class="col-lg-6">
				<!-- Mashead text and app badges-->
				<div class="mb-5 mb-lg-0 text-center text-lg-start">
					<p class="mt-5">
						Research has shown that writing down three good things that happend to you throughout
						the day can reduce depression and increase happiness and wellbeing.
					</p>
					Each night, at the end of the day:
					<ul>
						<li>Think about three good things that happened to you during the day.</li>
						<li>Reflect on your role in why they happened.</li>
						<li>Write them down, including where you were and sensory detail.</li>
					</ul>
					<p>
						The good thing can be big or small, something that brought you joy, something nice you
						did to someone, something nice someone did to you, something beautiful you saw,
						something interesting you heard. In short, anything that brought forth positive feelings
						in you. Also consider why it happened, and your role in making it happen.
					</p>
					<p>
						Try to do the excercise at approxiamtely the same time every night. This app helps you
						keep a record and make a habit of writing down the things that brought you joy.
					</p>
					<p>The practice was developed by Martin Seligman.</p>
					<p>References:</p>
					<ol>
						<li>
							<a href="https://pubmed.ncbi.nlm.nih.gov/16045394/" rel="external" target="_blank">
								Positive psychology progress: empirical validation of interventions
							</a>
						</li>
						<li>
							<a
								href="https://www.researchgate.net/publication/313845439_'The_Three_Good_Things'_-_The_effects_of_gratitude_practice_on_wellbeing_A_randomised_controlled_trial"
								rel="external"
								target="_blank"
							>
								'The Three Good Things' – The effects of gratitude practice on wellbeing: A
								randomised controlled trial
							</a>
						</li>
					</ol>
				</div>
			</div>
		</div>
	</div>
</header>

<footer class="bg-black text-center py-5 mt-auto">
	<div class="container px-5">
		<div class="text-white-50 small">
			<div class="mb-2">&copy; Your Website 2021. All Rights Reserved.</div>
			<a href="#!">Privacy</a>
			<span class="mx-1">&middot;</span>
			<a href="#!">Terms</a>
			<span class="mx-1">&middot;</span>
			<a href="#!">FAQ</a>
		</div>
	</div>
</footer>
