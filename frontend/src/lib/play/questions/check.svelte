<script lang="ts">
	import type { Question } from '$lib/quiz_types';
	import { kahoot_icons } from '$lib/play/kahoot_mode_assets/kahoot_icons';
	import {
		CheckIcon,
		ShapeTriangle,
		ShapeDiamond,
		ShapeCircle,
		ShapeSquare
	} from '$lib/components/icons';

	const shape_components = [ShapeTriangle, ShapeDiamond, ShapeCircle, ShapeSquare];

	const ore_styles = [
		{
			bg: 'linear-gradient(180deg, #f04856 0%, #c41e2d 100%)',
			border: '#1e1e1e',
			shadow: 'inset 3px 3px 0px #f8969e, inset -3px -3px 0px #70111a',
			name: 'Red'
		},
		{
			bg: 'linear-gradient(180deg, #4eedf5 0%, #1eabb3 100%)',
			border: '#1e1e1e',
			shadow: 'inset 3px 3px 0px #b2f8fc, inset -3px -3px 0px #13666b',
			name: 'Blue'
		},
		{
			bg: 'linear-gradient(180deg, #fbb034 0%, #d98218 100%)',
			border: '#1e1e1e',
			shadow: 'inset 3px 3px 0px #fed98b, inset -3px -3px 0px #7c480a',
			name: 'Yellow'
		},
		{
			bg: 'linear-gradient(180deg, #2ecc71 0%, #1b9a4c 100%)',
			border: '#1e1e1e',
			shadow: 'inset 3px 3px 0px #82e8aa, inset -3px -3px 0px #0f592c',
			name: 'Green'
		}
	];

	interface Props {
		question: Question;
		selected_answer?: string;
		game_mode: any;
		timer_res: any;
		circular_progress: any;
	}

	let {
		question,
		selected_answer = $bindable(),
		game_mode
	}: Props = $props();
	let _selected_answers = $state([false, false, false, false]);

	const selectAnswer = (i: number) => {
		_selected_answers[i] = !_selected_answers[i];
		selected_answer = '';
		for (let i = 0; i < _selected_answers.length; i++) {
			if (_selected_answers[i]) {
				selected_answer += String(i);
			}
		}
		selected_answer = selected_answer;
	};
</script>

<div class="w-full h-full grid grid-cols-1 sm:grid-cols-2 gap-4 p-2">
	{#each question.answers as answer, i}
		{@const ore = ore_styles[i % 4]}
		{@const ShapeComp = shape_components[i % 4]}
		<button
			class="flex items-center justify-between p-4 border-4 transition-all cursor-pointer select-none"
			style="background: {ore.bg}; border-color: {ore.border}; box-shadow: {ore.shadow};"
			class:opacity-100={_selected_answers[i]}
			class:opacity-50={!_selected_answers[i]}
			class:outline={_selected_answers[i]}
			class:outline-4={_selected_answers[i]}
			class:outline-white={_selected_answers[i]}
			onclick={() => selectAnswer(i)}
		>
			<div class="flex items-center gap-3">
				<div class="w-8 h-8 flex items-center justify-center text-white/90 drop-shadow">
					<ShapeComp class="w-6 h-6" />
				</div>
				{#if game_mode === 'kahoot'}
					<img class="h-10 inline-block" alt="Icon" src={kahoot_icons[i]} />
				{:else}
					<span class="font-minecraft text-base sm:text-xl text-white mc-text-shadow text-left">
						{answer.answer}
					</span>
				{/if}
			</div>

			<div class="mc-slot-dark w-8 h-8 flex items-center justify-center font-minecraft text-sm text-[#55ff55]">
				{#if _selected_answers[i]}
					<CheckIcon class="w-5 h-5 text-[#55ff55]" />
				{/if}
			</div>
		</button>
	{/each}
</div>
