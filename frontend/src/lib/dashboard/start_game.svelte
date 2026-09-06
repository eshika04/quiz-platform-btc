<!--
SPDX-FileCopyrightText: 2023 Marlon W (Mawoka)

SPDX-License-Identifier: MPL-2.0
-->

<script lang="ts">
	// import { alertModal } from '$lib/stores';
	import { captcha_enabled } from '$lib/config';
	import StartGameBackground from './start_game_background.svg';
	import { fade } from 'svelte/transition';
	import Spinner from '$lib/Spinner.svelte';
	import { onMount } from 'svelte';
	import { createTippy } from 'svelte-tippy';
	import { getLocalization } from '$lib/i18n';
	import { PlayIcon, CloseIcon, CheckIcon, GearIcon } from '$lib/components/icons';

	const { t } = getLocalization();
	let { quiz_id = $bindable() } = $props();
	let captcha_selected = $state(false);
	let selected_game_mode = $state('kahoot');
	let loading = $state(false);
	let custom_field = $state('');
	let cqcs_enabled = $state(false);
	let randomized_answers = $state(false);

	const tippy = createTippy({
		arrow: true,
		animation: 'perspective-subtle',
		placement: 'top-start',
		allowHTML: true
	});

	onMount(() => {
		const ls_data = localStorage.getItem('custom_field');
		custom_field = ls_data ? ls_data : '';
	});

	const start_game = async (id: string) => {
		let res;
		loading = true;
		localStorage.setItem('custom_field', custom_field);
		const cqcs_enabled_parsed = cqcs_enabled ? 'True' : 'False';
		const randomized_answers_parsed = randomized_answers ? 'True' : 'False';
		if (captcha_enabled && captcha_selected) {
			res = await fetch(
				`/api/v1/quiz/start/${id}?captcha_enabled=True&game_mode=${selected_game_mode}&custom_field=${custom_field}&cqcs_enabled=${cqcs_enabled_parsed}`,
				{
					method: 'POST'
				}
			);
		} else {
			res = await fetch(
				`/api/v1/quiz/start/${id}?captcha_enabled=False&game_mode=${selected_game_mode}&custom_field=${custom_field}&cqcs_enabled=${cqcs_enabled_parsed}&randomize_answers=${randomized_answers_parsed}`,
				{
					method: 'POST'
				}
			);
		}
		if (res.status !== 200) {
			/*			alertModal.set({
				open: true,
				title: 'Start failed',
				body: `Failed to start game, ${await res.text()}`
			});*/
			/*alertModal.subscribe((_) => {
				window.location.assign('/account/login?returnTo=/dashboard');
			});*/
			alert('Starting game failed');
			window.location.assign('/account/login?returnTo=/dashboard');
		} else {
			const data = await res.json();
			// eslint-disable-next-line no-undef
			plausible('Started Game', { props: { quiz_id: id, game_id: data.game_id } });
			window.location.assign(
				`/admin?token=${data.game_id}&pin=${data.game_pin}&connect=1&cqc_code=${data.cqc_code}`
			);
		}
	};

	const on_parent_click = (e: Event) => {
		if (e.target !== e.currentTarget) {
			return;
		}
		quiz_id = null;
	};
	const close_start_game_if_esc_is_pressed = (key: KeyboardEvent) => {
		if (key.code === 'Escape') {
			quiz_id = null;
		}
	};
	onMount(() => {
		document.body.addEventListener('keydown', close_start_game_if_esc_is_pressed);
	});
</script>

<div
	class="fixed top-0 left-0 flex justify-center items-center w-screen h-screen bg-black/80 z-50 text-white font-vt p-4"
	transition:fade|global={{ duration: 100 }}
	onclick={on_parent_click}
>
	<div
		class="w-full max-w-2xl mc-panel-dark p-6 shadow-2xl flex flex-col gap-4 border-4"
	>
		<div class="flex items-center justify-between border-b-2 border-[#555555] pb-2">
			<h2 class="font-minecraft text-lg sm:text-xl text-[#ffff55] mc-text-shadow-gold flex items-center gap-2">
				<PlayIcon class="w-5 h-5 text-[#55ff55]" />
				<span>LAUNCH QUIZ GAME</span>
			</h2>
			<button
				class="mc-btn font-minecraft text-xs p-1.5 flex items-center justify-center min-w-[28px] min-h-[28px]"
				onclick={() => {
					quiz_id = null;
				}}
				aria-label="Close modal"
			>
				<CloseIcon class="w-3.5 h-3.5" />
			</button>
		</div>

		<!-- Game Mode Selector -->
		<div class="grid grid-cols-2 gap-4 my-2">
			<button
				class="mc-slot-dark p-4 text-left transition-all flex flex-col gap-2"
				class:outline={selected_game_mode === 'kahoot'}
				class:outline-2={selected_game_mode === 'kahoot'}
				class:outline-[#55ff55]={selected_game_mode === 'kahoot'}
				onclick={() => {
					selected_game_mode = 'kahoot';
				}}
			>
				<div class="flex items-center gap-2">
					<PlayIcon class="w-5 h-5 text-[#55ff55]" />
					<h3 class="font-minecraft text-sm sm:text-base text-[#ffff55]">
						{$t('words.normal')}
					</h3>
				</div>
				<p class="font-vt text-base text-[#dcdcdc]">
					{$t('start_game.normal_mode_description')}
				</p>
			</button>

			<button
				class="mc-slot-dark p-4 text-left transition-all flex flex-col gap-2"
				class:outline={selected_game_mode === 'normal'}
				class:outline-2={selected_game_mode === 'normal'}
				class:outline-[#55ff55]={selected_game_mode === 'normal'}
				onclick={() => {
					selected_game_mode = 'normal';
				}}
			>
				<div class="flex items-center gap-2">
					<GearIcon class="w-5 h-5 text-[#4eedf5]" />
					<h3 class="font-minecraft text-sm sm:text-base text-[#ffff55]">
						{$t('start_game.old_school_mode')}
					</h3>
				</div>
				<p class="font-vt text-base text-[#dcdcdc]">
					{$t('start_game.old_school_mode_description')}
				</p>
			</button>
		</div>

		<!-- Custom Field Input -->
		<div class="mc-slot-dark p-3 flex flex-col sm:flex-row items-center gap-3">
			<label class="font-minecraft text-xs text-[#a0a0a0] uppercase whitespace-nowrap">
				{$t('result_page.custom_field')}:
			</label>
			<input
				bind:value={custom_field}
				class="mc-input text-lg py-1 px-3 w-full"
				placeholder="Phone Number or Email"
			/>
		</div>

		<!-- Toggle Options -->
		<div class="flex flex-col gap-2 font-minecraft text-xs text-[#dcdcdc]">
			<label class="flex items-center gap-2 cursor-pointer mc-slot-dark p-2">
				<input
					type="checkbox"
					bind:checked={randomized_answers}
					class="w-4 h-4 accent-[#55ff55]"
				/>
				<span>Randomize answers</span>
			</label>

			<label class="flex items-center gap-2 cursor-pointer mc-slot-dark p-2">
				<input
					type="checkbox"
					bind:checked={cqcs_enabled}
					class="w-4 h-4 accent-[#55ff55]"
				/>
				<span>ClassQuizControllers {cqcs_enabled ? 'enabled' : 'disabled'}</span>
			</label>
		</div>

		<!-- Start Game Action Button -->
		<button
			class="mc-btn-green mc-btn font-minecraft text-base sm:text-lg py-3 w-full mt-2 flex items-center justify-center gap-2"
			onclick={() => {
				start_game(quiz_id);
			}}
		>
			{#if loading}
				<Spinner my_20={false} />
			{:else}
				<PlayIcon class="w-5 h-5" />
				<span>{$t('start_game.start_game')}</span>
			{/if}
		</button>
	</div>
</div>
