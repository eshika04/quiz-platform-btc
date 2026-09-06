<!--
SPDX-FileCopyrightText: 2023 Marlon W (Mawoka)

SPDX-License-Identifier: MPL-2.0
-->
<script lang="ts">
	import { getLocalization } from '$lib/i18n';
	const { t } = getLocalization();
	import SearchCard from '$lib/search-card.svelte';
	import { onMount } from 'svelte';
	import { SearchIcon, CompassIcon } from '$lib/components/icons';

	let search_term = $state('');
	let resp_data = $state(null);

	const submit = async () => {
		const res = await fetch('/api/v1/search/', {
			method: 'POST',
			headers: {
				'Content-Type': 'application/json'
			},
			body: JSON.stringify({
				q: search_term,
				attributesToHighlight: ['*']
			})
		});
		if (res.status === 200) {
			let resp_data_temp: string = await res.text();
			resp_data_temp = resp_data_temp.replaceAll('<em>', '<mark>');
			resp_data_temp = resp_data_temp.replaceAll('</em>', '</mark>');
			resp_data = JSON.parse(resp_data_temp);
			const url = new URLSearchParams(window.location.search);
			url.set('q', search_term);
			history.pushState(null, null, '?' + url.toString());
		} else {
			console.error('Error!', res.status);
		}
	};
	onMount(() => {
		const url = new URLSearchParams(window.location.search);
		search_term = url.get('q') ?? '';
		submit();
	});
</script>

<svelte:head>
	<title>ClassQuiz - Search</title>
</svelte:head>

<div class="max-w-7xl mx-auto px-4 py-8 font-vt">
	<div class="text-center mb-6">
		<h1 class="font-minecraft text-3xl sm:text-5xl text-[#ffff55] mc-text-shadow-gold flex items-center justify-center gap-3">
			<SearchIcon class="w-8 h-8 sm:w-12 sm:h-12" />
			<span>SEARCH QUIZZES</span>
		</h1>
		<p class="font-vt text-xl text-[#1e1e1e] dark:text-[#a0a0a0] mt-2">
			Find any quiz across the ClassQuiz universe
		</p>
	</div>

	<div class="flex justify-center mb-8">
		<form
			class="flex items-center gap-2 max-w-xl w-full"
			onsubmit={(e: Event) => {
				e.preventDefault();
				submit();
			}}
		>
			<input
				type="search"
				class="mc-input flex-1 text-xl py-2 px-4"
				placeholder={$t('search_page.at_least_3_characters')}
				aria-label="Search"
				bind:value={search_term}
			/>
			<button
				class="mc-btn-diamond mc-btn font-minecraft text-xs uppercase py-3 px-6 flex items-center gap-1.5"
				disabled={search_term.length <= 2}
				type="submit"
			>
				<SearchIcon class="w-3.5 h-3.5" />
				<span>Find</span>
			</button>
		</form>
	</div>

	{#if resp_data}
		{#if resp_data.hits.length !== 0}
			<div class="grid lg:grid-cols-3 sm:grid-cols-2 grid-cols-1 gap-6">
				{#each resp_data.hits as quiz}
					<SearchCard quiz={quiz._formatted} />
				{/each}
			</div>
		{:else}
			<div class="mc-panel-dark p-8 max-w-md mx-auto text-center shadow-2xl">
				<div class="w-12 h-12 mx-auto mb-2 flex items-center justify-center text-[#ffff55]">
					<CompassIcon class="w-10 h-10" />
				</div>
				<h2 class="font-minecraft text-xl text-[#ff5555] mb-2">
					{$t('search_page.nothing_here')}
				</h2>
				<p class="font-vt text-lg text-[#dcdcdc]">
					Not finding what you are looking for? Search on <a
						class="underline text-[#4eedf5]"
						href="https://create.kahoot.it/search?query={search_term}&tags=test&filter=filter%3D1"
						target="_blank">Kahoot!</a
					>
					and <a href="/import" class="underline text-[#55ff55]">import</a> it!
				</p>
			</div>
		{/if}
	{/if}
</div>
