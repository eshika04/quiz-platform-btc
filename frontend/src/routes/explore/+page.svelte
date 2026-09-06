<!--
SPDX-FileCopyrightText: 2023 Marlon W (Mawoka)

SPDX-License-Identifier: MPL-2.0
-->

<script lang="ts">
	import { navbarVisible } from '$lib/stores.svelte.ts';
	import SearchCard from '$lib/search-card.svelte';
	import type { PageData } from './$types';
	import { CompassIcon, HammerIcon, SearchIcon } from '$lib/components/icons';

	navbarVisible.visible = true;

	interface Props {
		data: PageData;
	}

	let { data }: Props = $props();

	const quizzes = $derived(data?.results?.hits ?? []);
</script>

<svelte:head>
	<title>ClassQuiz - Explore</title>
</svelte:head>

<div class="max-w-7xl mx-auto px-4 py-8 font-vt">
	<div class="text-center mb-8">
		<h1 class="font-minecraft text-3xl sm:text-5xl text-[#ffff55] mc-text-shadow-gold flex items-center justify-center gap-3">
			<CompassIcon class="w-8 h-8 sm:w-12 sm:h-12" />
			<span>EXPLORE QUIZZES</span>
		</h1>
		<p class="font-vt text-xl text-[#1e1e1e] dark:text-[#a0a0a0] mt-2">
			Discover community-crafted quizzes and challenges
		</p>
	</div>

	{#if quizzes.length > 0}
		<div class="grid lg:grid-cols-3 sm:grid-cols-2 grid-cols-1 gap-6">
			{#each quizzes as quiz}
				<SearchCard {quiz} />
			{/each}
		</div>
	{:else}
		<div class="mc-panel-dark p-8 max-w-md mx-auto text-center shadow-2xl">
			<div class="w-12 h-12 mx-auto mb-2 flex items-center justify-center text-[#ffff55]">
				<CompassIcon class="w-10 h-10" />
			</div>
			<h2 class="font-minecraft text-xl text-[#ffff55] mb-2">
				QUIZ ARCHIVES
			</h2>
			<p class="font-vt text-lg text-[#dcdcdc] mb-4">
				Start crafting your own quiz or search for existing ones.
			</p>
			<div class="flex justify-center gap-3">
				<a href="/create" class="mc-btn-green mc-btn text-xs font-minecraft flex items-center gap-1.5">
					<HammerIcon class="w-3.5 h-3.5" />
					<span>Create Quiz</span>
				</a>
				<a href="/search" class="mc-btn-diamond mc-btn text-xs font-minecraft flex items-center gap-1.5">
					<SearchIcon class="w-3.5 h-3.5" />
					<span>Search</span>
				</a>
			</div>
		</div>
	{/if}
</div>
