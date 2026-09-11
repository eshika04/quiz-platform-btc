<!--
SPDX-FileCopyrightText: 2023 Marlon W (Mawoka)

SPDX-License-Identifier: MPL-2.0
-->

<script lang="ts">
	import { onMount } from 'svelte';
	import { getLocalization } from '$lib/i18n';
	import { fly } from 'svelte/transition';
	import confetti from 'canvas-confetti';
	import { TrophyIcon, MedalIcon } from '$lib/components/icons';

	const { t } = getLocalization();

	interface Props {
		data: any;
		username?: any;
		show_final_results: boolean;
	}

	let { data = $bindable(), username, show_final_results }: Props = $props();

	let player_names = $derived(Object.keys(data).sort((a, b) => {
		const scoreA = parseFloat(data[a]) || 0;
		const scoreB = parseFloat(data[b]) || 0;
		return scoreB - scoreA;
	}));

	let player_count_or_five = $derived(player_names.length >= 5 ? 5 : player_names.length);

	let canvas: HTMLCanvasElement = $state();
	onMount(() => {
		setTimeout(
			() => {
				confetti.create(canvas, {
					resize: true,
					useWorker: true
				});
				confetti({ particleCount: 200, spread: 160 });
			},
			player_count_or_five * 1200 - 800
		);
	});
</script>

{#if show_final_results}
	<canvas bind:this={canvas} class="fixed inset-0 pointer-events-none z-50"></canvas>
	<div class="min-h-screen flex flex-col justify-center items-center p-4 font-game-body">
		<div class="mc-panel-dark p-6 max-w-2xl w-full shadow-2xl text-center mb-8">
			<div class="w-16 h-16 mx-auto mb-2 flex items-center justify-center text-[#ffff55] animate-bounce">
				<TrophyIcon class="w-12 h-12" />
			</div>
			<h1 class="font-bungee text-2xl sm:text-4xl text-[#ffff55] mc-text-shadow-gold mb-6 tracking-wide">
				FINAL RESULTS
			</h1>

			<div class="flex flex-col gap-3">
				{#each player_names as player, i}
					{#if i <= player_count_or_five - 1}
						{@const rankColors = [
							'text-[#ffff55] border-[#f59e0b] bg-[#f59e0b]/20',
							'text-[#dcdcdc] border-[#a0a0a0] bg-[#a0a0a0]/20',
							'text-[#d98218] border-[#b07d4b] bg-[#b07d4b]/20',
							'text-[#55ff55] border-[#55ff55]/50 bg-[#1e1e1e]',
							'text-[#4eedf5] border-[#4eedf5]/50 bg-[#1e1e1e]'
						]}
						{@const rankTitles = ['1ST PLACE', '2ND PLACE', '3RD PLACE', '4TH PLACE', '5TH PLACE']}
						<div
							in:fly|global={{ y: -200, delay: player_count_or_five * 1000 - (i + 1) * 800 }}
							class="mc-slot-dark p-3 sm:p-4 flex items-center justify-between border-2 {rankColors[i] ?? 'text-white'}"
						>
							<div class="flex items-center gap-3">
								<div class="flex items-center gap-1.5 font-bungee text-xs sm:text-sm tracking-wider">
									<MedalIcon class="w-4 h-4 inline-block" />
									<span>{rankTitles[i]}</span>
								</div>
								<span class="font-bungee text-base sm:text-xl mc-text-shadow tracking-wide">{player}</span>
							</div>
							<span class="font-bungee text-sm sm:text-lg tracking-wider">{data[player]} PTS</span>
						</div>
					{/if}
				{/each}
			</div>
		</div>

		{#if data[username]}
			<div class="mc-panel-dark p-4 max-w-md w-full shadow-2xl text-center">
				<p class="font-bungee text-xs text-[#a0a0a0] uppercase mb-1 tracking-wider">YOUR SCORE:</p>
				<p class="font-bungee text-2xl text-[#55ff55] mc-text-shadow">{data[username]} PTS</p>
				{#each player_names as player, i}
					{#if player === username}
						<p class="font-game-body text-xl text-[#ffff55] mt-1 font-semibold">
							Final Rank: #{i + 1} of {player_names.length} players
						</p>
					{/if}
				{/each}
			</div>
		{/if}
	</div>
{/if}
