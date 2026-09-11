<script lang="ts">
	import { QuizQuestionType } from '$lib/quiz_types';
	import type { QuizData } from '$lib/quiz_types';
	import CircularTimer from '$lib/play/circular_progress.svelte';
	import MediaComponent from '$lib/editor/MediaComponent.svelte';
	import { getLocalization } from '$lib/i18n';
	import {
		ShapeTriangle,
		ShapeDiamond,
		ShapeCircle,
		ShapeSquare,
		CheckIcon
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
		quiz_data: QuizData;
		selected_question: number;
		timer_res: string;
		answer_count: number;
		default_colors: string[];
	}

	let {
		quiz_data,
		selected_question,
		timer_res = $bindable(),
		answer_count
	}: Props = $props();

	const { t } = getLocalization();

	let circular_progress = $derived.by(() => {
		try {
			return (
				1 -
				((100 / parseInt(quiz_data.questions[selected_question].time)) *
					parseInt(timer_res)) /
					100
			);
		} catch {
			return 0;
		}
	});
</script>

<div class="flex flex-col justify-center w-screen h-1/6">
	<h1 class="text-4xl sm:text-6xl font-bungee text-center px-4 leading-tight">
		{@html quiz_data.questions[selected_question].question}
	</h1>
	<!--			<span class='text-center py-2 text-lg'>{$t('admin_page.time_left')}: {timer_res}</span>-->
	<div class="grid grid-cols-3 my-2">
		<span></span>
		<div class="m-auto">
			<CircularTimer text={timer_res} progress={circular_progress} color="#ef4444" />
		</div>
		<p class="m-auto font-bungee text-2xl sm:text-3xl text-[#ffff55] mc-text-shadow">
			{$t('admin_page.answers_submitted', { answer_count: answer_count })}
		</p>
	</div>
</div>
{#if quiz_data.questions[selected_question].image !== null}
	<div class="flex w-full">
		<MediaComponent
			src={quiz_data.questions[selected_question].image}
			muted={false}
			css_classes="max-h-[20vh] object-cover mx-auto mb-8 w-auto"
		/>
	</div>
{/if}
{#if quiz_data.questions[selected_question].type === QuizQuestionType.ABCD || quiz_data.questions[selected_question].type === QuizQuestionType.VOTING || quiz_data.questions[selected_question].type === QuizQuestionType.CHECK}
	<div class="grid grid-cols-1 sm:grid-cols-2 gap-4 w-full p-4 max-w-7xl mx-auto">
		{#each quiz_data.questions[selected_question].answers as answer, i}
			{@const ore = ore_styles[i % 4]}
			{@const ShapeComp = shape_components[i % 4]}
			<div
				class="flex items-center justify-between p-4 sm:p-5 border-4 transition-all rounded-sm"
				style="background: {ore.bg}; border-color: {ore.border}; box-shadow: {ore.shadow};"
				class:opacity-40={!answer.right &&
					timer_res === '0' &&
					quiz_data.questions[selected_question].type === QuizQuestionType.ABCD}
				class:ring-4={answer.right &&
					timer_res === '0' &&
					quiz_data.questions[selected_question].type === QuizQuestionType.ABCD}
				class:ring-white={answer.right &&
					timer_res === '0' &&
					quiz_data.questions[selected_question].type === QuizQuestionType.ABCD}
			>
				<div class="flex items-center gap-3.5">
					<div class="w-9 h-9 flex items-center justify-center text-white drop-shadow">
						<ShapeComp class="w-7 h-7" />
					</div>
					<span
						class="text-left font-bungee text-xl sm:text-2xl px-2 py-2 text-white mc-text-shadow tracking-wide leading-snug"
					>
						{answer.answer}
					</span>
				</div>
				<div class="flex items-center gap-2">
					{#if answer.right && timer_res === '0' && quiz_data.questions[selected_question].type === QuizQuestionType.ABCD}
						<div class="mc-slot-dark w-9 h-9 flex items-center justify-center font-bungee text-sm text-[#55ff55]">
							<CheckIcon class="w-6 h-6 text-[#55ff55]" />
						</div>
					{/if}
					<span class="font-bungee text-xs text-white/80 uppercase tracking-widest hidden sm:inline">
						[{ore.name}]
					</span>
				</div>
			</div>
		{/each}
	</div>
{:else if quiz_data.questions[selected_question].type === QuizQuestionType.TEXT}
	{#if timer_res === '0'}
		<div class="grid grid-cols-1 sm:grid-cols-2 gap-4 w-full p-4 max-w-7xl mx-auto">
			{#each quiz_data.questions[selected_question].answers as answer, i}
				{@const ore = ore_styles[i % 4]}
				<div
					class="flex items-center justify-center p-4 sm:p-5 border-4 rounded-sm"
					style="background: {ore.bg}; border-color: {ore.border}; box-shadow: {ore.shadow};"
				>
					<span class="text-center font-bungee text-xl sm:text-2xl px-2 py-2 text-white mc-text-shadow tracking-wide"
						>{answer.answer}</span
					>
				</div>
			{/each}
		</div>
	{:else}
		<div class="flex justify-center p-4">
			<p class="font-bungee text-2xl text-[#ffff55] mc-text-shadow">{$t('admin_page.enter_answer_into_field')}</p>
		</div>
	{/if}
{/if}
