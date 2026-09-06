<!--
SPDX-FileCopyrightText: 2023 Marlon W (Mawoka)

SPDX-License-Identifier: MPL-2.0
-->

<script lang="ts">
	import { SparklesIcon } from '$lib/components/icons';

	function sortObjectbyValue(obj) {
		const ret = {};
		Object.keys(obj)
			.sort((a, b) => obj[b] - obj[a])
			.forEach((s) => (ret[s] = obj[s]));
		return ret;
	}

	interface Props {
		scores: any;
		question_results: Array<{
			username: string;
			answer: string;
			right: boolean;
			time_taken: number;
			score: number;
		}>;
		username: any;
	}

	let { scores = $bindable(), question_results, username }: Props = $props();
	let score_by_username = $state({});

	if (JSON.stringify(scores) === '{}') {
		for (const i of question_results) {
			scores[i.username] = 0;
		}
	}
	for (const i of question_results) {
		score_by_username[i.username] = i.score;
	}
	for (const username of Object.keys(score_by_username)) {
		scores[username] = (score_by_username[username] ?? 0) + (scores[username] ?? 0);
	}
	scores = scores;
	let sorted_scores = $derived(sortObjectbyValue(scores));
</script>

<div class="flex justify-center items-center h-screen font-vt p-4">
	<div class="mc-panel-dark p-8 max-w-md w-full shadow-2xl flex flex-col items-center text-center">
		<!-- Sparkles / Trophy Icon -->
		<div class="w-12 h-12 mb-3 flex items-center justify-center text-[#ffff55] animate-bounce">
			<SparklesIcon class="w-10 h-10" />
		</div>

		<h2 class="font-minecraft text-xl text-[#ffff55] mc-text-shadow-gold mb-4">
			ROUND COMPLETE
		</h2>

		<!-- XP Gained Box -->
		<div class="mc-slot-dark p-4 w-full mb-4 flex items-center justify-between">
			<span class="font-minecraft text-xs text-[#a0a0a0] uppercase">SCORE GAINED:</span>
			<span class="font-minecraft text-2xl text-[#55ff55] mc-text-shadow">
				+{score_by_username[username] ?? '0'} PTS
			</span>
		</div>

		<!-- Total Score Box -->
		<div class="mc-slot-dark p-4 w-full flex items-center justify-between">
			<span class="font-minecraft text-xs text-[#a0a0a0] uppercase">TOTAL SCORE:</span>
			<span class="font-minecraft text-2xl text-[#ffff55] mc-text-shadow-gold">
				{sorted_scores[username] ?? '0'}
			</span>
		</div>

		<p class="font-vt text-lg text-[#888888] mt-6">
			Waiting for Host...
		</p>
	</div>
</div>
