<!--
SPDX-FileCopyrightText: 2023 Marlon W (Mawoka)

SPDX-License-Identifier: MPL-2.0
-->

<script lang="ts">
	import { navbarVisible } from '$lib/stores.svelte.ts';
	import { getLocalization } from '$lib/i18n';
	import Footer from '$lib/footer.svelte';
	import WebPOpenGraph from '$lib/assets/landing/opengraph-home.webp';
	import JpgOpenGraph from '$lib/assets/landing/opengraph-home.jpg';
	import Newsletter from '$lib/landing/newsletter.svelte';
	import { fly, fade } from 'svelte/transition';
	import {
		PlayIcon,
		CompassIcon,
		SearchIcon,
		DashboardIcon,
		DocsIcon,
		TrophyIcon,
		SparklesIcon,
		CheckIcon,
		ClockIcon,
		HammerIcon,
		HeartIcon,
		SunIcon,
		MoonIcon
	} from '$lib/components/icons';

	import FindScreenshot from '$lib/assets/landing_new/find.webp';
	import ImportScreenshot from '$lib/assets/landing_new/import.webp';
	import EditScreenshot from '$lib/assets/landing_new/edit.webp';
	import SelectScreenshot from '$lib/assets/landing_new/select.webp';
	import ResultScreenshot from '$lib/assets/landing_new/result.webp';
	import WinnersScreenshot from '$lib/assets/landing_new/winners.webp';
	import { onMount } from 'svelte';

	const { t } = getLocalization();

	navbarVisible.visible = true;

	let newsletterModalOpen: boolean = $state();
	onMount(() => {
		const ls = localStorage.getItem('newsletter');
		newsletterModalOpen = ls === null;
	});

	const splashPhrases = [
		'Real-time multiplayer quizzes!',
		'Crafting quizzes since day one!',
		'Achievement Get: 100% Score!',
		'Lightning-fast websocket gameplay!',
		'Built for classrooms and friends!',
		'Made with open-source passion!',
		'100% Pure Open-Source Quiz Platform!'
	];
	let currentSplash = $state(splashPhrases[0]);

	onMount(() => {
		currentSplash = splashPhrases[Math.floor(Math.random() * splashPhrases.length)];
	});

	enum SelectedCreateThing {
		Create,
		Find,
		Import
	}

	enum SelectedPlayThing {
		Select,
		Results,
		Winners
	}

	let selected_create_thing = $state(SelectedCreateThing.Create);
	let selected_play_thing = $state(SelectedPlayThing.Select);

	const classquiz_reasons = [
		{
			headline: $t('index_page.no_player_limit'),
			content: $t('index_page.no_player_limit_content'),
			icon: PlayIcon
		},
		{
			headline: $t('index_page.no_tracking'),
			content: $t('index_page.no_tracking_content'),
			icon: CheckIcon
		},
		{
			headline: $t('index_page.self_hostable'),
			content: $t('index_page.self_hostable_content'),
			icon: DashboardIcon
		},
		{
			headline: $t('index_page.german_server'),
			content: $t('index_page.german_server_content'),
			icon: SparklesIcon
		},
		{
			headline: $t('index_page.user_friendly'),
			content: $t('index_page.user_friendly_content'),
			icon: PlayIcon
		},
		{
			headline: $t('index_page.completely_free'),
			content: $t('index_page.completely_free_content'),
			icon: TrophyIcon
		},
		{
			headline: $t('index_page.quiz_results_downloadable'),
			content: $t('index_page.quiz_results_downloadable_content'),
			icon: DocsIcon
		},
		{
			headline: $t('index_page.multilingual'),
			content: $t('index_page.multilingual_content'),
			icon: CompassIcon
		},
		{
			headline: $t('index_page.dark_mode'),
			content: $t('index_page.dark_mode_content'),
			icon: MoonIcon
		},
		{
			headline: $t('index_page.download_quizzes'),
			content: $t('index_page.download_quizzes_content'),
			icon: DashboardIcon
		},
		{
			headline: $t('index_page.community_driven'),
			content: $t('index_page.community_driven_content'),
			icon: HeartIcon
		}
	];
	let selected_classquiz_reason = $state(0);
	let ActiveIcon = $derived(classquiz_reasons[selected_classquiz_reason].icon);
</script>

<svelte:head>
	<title>ClassQuiz - {$t('index_page.meta.title')}</title>
	<meta name="description" content={$t('index_page.meta.description')} />
	<meta property="og:url" content="https://classquiz.de/" />
	<meta property="og:type" content="website" />
	<meta property="og:title" content="ClassQuiz - {$t('index_page.meta.title')}" />
	<meta
		property="og:description"
		content="ClassQuiz is an open-source quiz application. Create quizzes and play them with your friends."
	/>
	<meta property="og:image" content={JpgOpenGraph} />
	<meta name="twitter:card" content="summary_large_image" />
	<meta name="twitter:image" content={WebPOpenGraph} />
</svelte:head>

<div class="min-h-screen flex flex-col font-vt">
	<!-- Hero Section -->
	<section class="pt-16 pb-20 px-4 text-center relative overflow-hidden">
		<div class="max-w-4xl mx-auto flex flex-col items-center">
			<!-- 3D Title -->
			<div class="relative inline-block mt-8 mb-4">
				<h1
					class="font-minecraft text-4xl sm:text-6xl md:text-7xl lg:text-8xl tracking-wider text-[#d0d0d0] drop-shadow-[0_6px_0_#2b2b2b]"
					style="text-shadow: 4px 4px 0px #383838, 7px 7px 0px #1a1a1a, 10px 10px 0px #000000;"
				>
					CLASSQUIZ
				</h1>
				<!-- Bouncing Yellow Splash Text -->
				<div class="absolute -bottom-4 right-0 sm:-right-8 z-20 pointer-events-none">
					<span class="mc-splash-text text-sm sm:text-lg md:text-xl font-minecraft whitespace-nowrap">
						{currentSplash}
					</span>
				</div>
			</div>

			<p class="font-vt text-2xl sm:text-3xl text-[#1e1e1e] dark:text-[#ffffa0] mt-6 max-w-2xl leading-relaxed">
				{$t('index_page.slogan')}
			</p>

			<!-- Hero Action Buttons -->
			<div class="flex flex-wrap gap-4 justify-center items-center mt-10 z-10">
				<a
					href="/play"
					class="mc-btn-green mc-btn text-base sm:text-lg px-6 py-3 tracking-wider font-minecraft uppercase shadow-lg hover:scale-105 transition-transform flex items-center gap-2"
				>
					<PlayIcon class="w-4 h-4" />
					<span>{$t('words.play')} Now</span>
				</a>
				<a
					href="/explore"
					class="mc-btn-diamond mc-btn text-base sm:text-lg px-6 py-3 tracking-wider font-minecraft uppercase shadow-lg hover:scale-105 transition-transform flex items-center gap-2"
				>
					<CompassIcon class="w-4 h-4" />
					<span>{$t('words.explore')}</span>
				</a>
				<a
					href="/create"
					class="mc-btn-wood mc-btn text-base sm:text-lg px-6 py-3 tracking-wider font-minecraft uppercase shadow-lg hover:scale-105 transition-transform flex items-center gap-2"
				>
					<HammerIcon class="w-4 h-4" />
					<span>{$t('words.create')}</span>
				</a>
			</div>
		</div>
	</section>

	<!-- How Does ClassQuiz Work Section -->
	<section class="max-w-6xl mx-auto px-4 mb-20 w-full">
		<div class="text-center mb-8">
			<h2 class="font-minecraft text-2xl sm:text-4xl text-[#1e1e1e] dark:text-[#ffff55] mc-text-shadow flex items-center justify-center gap-3">
				<DocsIcon class="w-7 h-7 inline-block" />
				<span>{$t('index_page.how_does_classquiz_work')}</span>
			</h2>
		</div>

		<!-- Step 1: Get a Quiz -->
		<div class="mb-14">
			<div class="flex justify-start">
				<div class="mc-panel-dark px-4 py-2 font-minecraft text-sm sm:text-base border-b-0 text-[#ffff55] flex items-center gap-2">
					<HammerIcon class="w-4 h-4" />
					<span>{$t('index_page.get_a_quiz')}</span>
				</div>
			</div>

			<div class="mc-panel-dark grid grid-cols-1 lg:grid-cols-2 gap-4 p-4 shadow-2xl">
				<!-- Screenshot display inside item frame -->
				<div class="mc-slot-dark p-2 flex items-center justify-center min-h-[260px]">
					{#if selected_create_thing === SelectedCreateThing.Create}
						<img
							class="w-full max-h-[360px] object-contain border-2 border-[#555555]"
							src={EditScreenshot}
							in:fade|global={{ duration: 150 }}
							alt="Screenshot of quiz editor"
						/>
					{:else if selected_create_thing === SelectedCreateThing.Find}
						<img
							class="w-full max-h-[360px] object-contain border-2 border-[#555555]"
							src={FindScreenshot}
							in:fade|global={{ duration: 150 }}
							alt="Screenshot of search page"
						/>
					{:else if selected_create_thing === SelectedCreateThing.Import}
						<img
							class="w-full max-h-[360px] object-contain border-2 border-[#555555]"
							src={ImportScreenshot}
							in:fade|global={{ duration: 150 }}
							alt="Screenshot of import page"
						/>
					{/if}
				</div>

				<!-- Selection buttons -->
				<div class="flex flex-col gap-3 justify-center">
					<button
						class="mc-btn text-left p-4 flex items-center gap-4 transition-all"
						class:outline={selected_create_thing === SelectedCreateThing.Create}
						class:outline-2={selected_create_thing === SelectedCreateThing.Create}
						class:outline-[#55ff55]={selected_create_thing === SelectedCreateThing.Create}
						onclick={() => {
							selected_create_thing = SelectedCreateThing.Create;
						}}
					>
						<div class="w-10 h-10 flex items-center justify-center text-[#ffff55]">
							<DocsIcon class="w-7 h-7" />
						</div>
						<div>
							<h3 class="font-minecraft text-sm sm:text-base text-[#ffff55]">{$t('words.create')}</h3>
							<p class="font-vt text-lg text-[#dcdcdc]">{$t('index_page.create_a_quiz_from_scratch')}</p>
						</div>
					</button>

					<button
						class="mc-btn text-left p-4 flex items-center gap-4 transition-all"
						class:outline={selected_create_thing === SelectedCreateThing.Find}
						class:outline-2={selected_create_thing === SelectedCreateThing.Find}
						class:outline-[#55ff55]={selected_create_thing === SelectedCreateThing.Find}
						onclick={() => {
							selected_create_thing = SelectedCreateThing.Find;
						}}
					>
						<div class="w-10 h-10 flex items-center justify-center text-[#ffff55]">
							<SearchIcon class="w-7 h-7" />
						</div>
						<div>
							<h3 class="font-minecraft text-sm sm:text-base text-[#ffff55]">{$t('words.find')}</h3>
							<p class="font-vt text-lg text-[#dcdcdc]">{$t('index_page.find_or_explore')}</p>
						</div>
					</button>

					<button
						class="mc-btn text-left p-4 flex items-center gap-4 transition-all"
						class:outline={selected_create_thing === SelectedCreateThing.Import}
						class:outline-2={selected_create_thing === SelectedCreateThing.Import}
						class:outline-[#55ff55]={selected_create_thing === SelectedCreateThing.Import}
						onclick={() => {
							selected_create_thing = SelectedCreateThing.Import;
						}}
					>
						<div class="w-10 h-10 flex items-center justify-center text-[#ffff55]">
							<DashboardIcon class="w-7 h-7" />
						</div>
						<div>
							<h3 class="font-minecraft text-sm sm:text-base text-[#ffff55]">{$t('words.import')}</h3>
							<p class="font-vt text-lg text-[#dcdcdc]">Import quizzes from other formats easily</p>
						</div>
					</button>
				</div>
			</div>
		</div>

		<!-- Step 2: Play Quiz -->
		<div>
			<div class="flex justify-start">
				<div class="mc-panel-dark px-4 py-2 font-minecraft text-sm sm:text-base border-b-0 text-[#55ff55] flex items-center gap-2">
					<PlayIcon class="w-4 h-4" />
					<span>{$t('index_page.play_quiz')}</span>
				</div>
			</div>

			<div class="mc-panel-dark grid grid-cols-1 lg:grid-cols-2 gap-4 p-4 shadow-2xl">
				<!-- Screenshot display inside item frame -->
				<div class="mc-slot-dark p-2 flex items-center justify-center min-h-[260px]">
					{#if selected_play_thing === SelectedPlayThing.Select}
						<img
							class="w-full max-h-[360px] object-contain border-2 border-[#555555]"
							src={SelectScreenshot}
							in:fade|global={{ duration: 150 }}
							alt="Screenshot of answer selection"
						/>
					{:else if selected_play_thing === SelectedPlayThing.Results}
						<img
							class="w-full max-h-[360px] object-contain border-2 border-[#555555]"
							src={ResultScreenshot}
							in:fade|global={{ duration: 150 }}
							alt="Screenshot of question results"
						/>
					{:else if selected_play_thing === SelectedPlayThing.Winners}
						<img
							class="w-full max-h-[360px] object-contain border-2 border-[#555555]"
							src={WinnersScreenshot}
							in:fade|global={{ duration: 150 }}
							alt="Screenshot of victory podium"
						/>
					{/if}
				</div>

				<!-- Selection buttons -->
				<div class="flex flex-col gap-3 justify-center">
					<button
						class="mc-btn text-left p-4 flex items-center gap-4 transition-all"
						class:outline={selected_play_thing === SelectedPlayThing.Select}
						class:outline-2={selected_play_thing === SelectedPlayThing.Select}
						class:outline-[#55ff55]={selected_play_thing === SelectedPlayThing.Select}
						onclick={() => {
							selected_play_thing = SelectedPlayThing.Select;
						}}
					>
						<div class="w-10 h-10 flex items-center justify-center text-[#ffff55]">
							<CheckIcon class="w-7 h-7" />
						</div>
						<div>
							<h3 class="font-minecraft text-sm sm:text-base text-[#ffff55]">{$t('index_page.select_answer')}</h3>
							<p class="font-vt text-lg text-[#dcdcdc]">{$t('index_page.choose_answer_wisely')}</p>
						</div>
					</button>

					<button
						class="mc-btn text-left p-4 flex items-center gap-4 transition-all"
						class:outline={selected_play_thing === SelectedPlayThing.Results}
						class:outline-2={selected_play_thing === SelectedPlayThing.Results}
						class:outline-[#55ff55]={selected_play_thing === SelectedPlayThing.Results}
						onclick={() => {
							selected_play_thing = SelectedPlayThing.Results;
						}}
					>
						<div class="w-10 h-10 flex items-center justify-center text-[#ffff55]">
							<ClockIcon class="w-7 h-7" />
						</div>
						<div>
							<h3 class="font-minecraft text-sm sm:text-base text-[#ffff55]">{$t('index_page.view_results')}</h3>
							<p class="font-vt text-lg text-[#dcdcdc]">{$t('index_page.check_if_chosen_wisely')}</p>
						</div>
					</button>

					<button
						class="mc-btn text-left p-4 flex items-center gap-4 transition-all"
						class:outline={selected_play_thing === SelectedPlayThing.Winners}
						class:outline-2={selected_play_thing === SelectedPlayThing.Winners}
						class:outline-[#55ff55]={selected_play_thing === SelectedPlayThing.Winners}
						onclick={() => {
							selected_play_thing = SelectedPlayThing.Winners;
						}}
					>
						<div class="w-10 h-10 flex items-center justify-center text-[#ffff55]">
							<TrophyIcon class="w-7 h-7" />
						</div>
						<div>
							<h3 class="font-minecraft text-sm sm:text-base text-[#ffff55]">{$t('index_page.list_winners')}</h3>
							<p class="font-vt text-lg text-[#dcdcdc]">{$t('index_page.get_ranking_and_winners')}</p>
						</div>
					</button>
				</div>
			</div>
		</div>
	</section>

	<!-- Why ClassQuiz Section -->
	<section class="max-w-6xl mx-auto px-4 mb-24 w-full">
		<div class="text-center mb-8">
			<h2 class="font-minecraft text-2xl sm:text-4xl text-[#1e1e1e] dark:text-[#ffff55] mc-text-shadow flex items-center justify-center gap-3">
				<SparklesIcon class="w-7 h-7" />
				<span>{$t('index_page.why_classquiz')}</span>
			</h2>
		</div>

		<div class="mc-panel-dark p-6 shadow-2xl">
			<!-- Top: Reasons Grid -->
			<div class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-6 gap-2 mb-6">
				{#each classquiz_reasons as reason, index}
					{@const IconComponent = reason.icon}
					<button
						class="mc-slot-dark p-2 flex flex-col items-center justify-center text-center transition-all hover:bg-[#333333]"
						class:outline={selected_classquiz_reason === index}
						class:outline-2={selected_classquiz_reason === index}
						class:outline-[#ffff55]={selected_classquiz_reason === index}
						onclick={() => {
							selected_classquiz_reason = index;
						}}
					>
						<div class="w-7 h-7 mb-1 flex items-center justify-center text-[#ffff55]">
							<IconComponent class="w-5 h-5" />
						</div>
						<span class="font-minecraft text-[10px] text-[#dcdcdc] line-clamp-2 leading-tight">
							{reason.headline}
						</span>
					</button>
				{/each}
			</div>

			<!-- Bottom: Active Item Description Box -->
			<div class="mc-tooltip p-6 border-4">
				<h3 class="font-minecraft text-lg sm:text-xl text-[#ffff55] mb-2 flex items-center gap-2">
					<ActiveIcon class="w-5 h-5 inline-block" />
					<span>{classquiz_reasons[selected_classquiz_reason].headline}</span>
				</h3>
				<p class="font-vt text-xl sm:text-2xl text-[#f0f0f0] leading-relaxed">
					{classquiz_reasons[selected_classquiz_reason].content}
				</p>
			</div>
		</div>
	</section>
</div>

{#if newsletterModalOpen}
	<div
		class="fixed bottom-6 right-6 z-50 p-2 mc-panel-dark shadow-2xl max-w-sm"
		transition:fly|global={{ y: 20 }}
	>
		<Newsletter bind:open={newsletterModalOpen} />
	</div>
{/if}

<Footer />
