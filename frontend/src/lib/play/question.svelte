<script lang="ts">
	import type { Question } from '$lib/quiz_types';
	import { QuizQuestionType } from '$lib/quiz_types';
	import { socket } from '$lib/socket';
	import Spinner from '../Spinner.svelte';
	import { getLocalization } from '$lib/i18n';
	import { kahoot_icons } from './kahoot_mode_assets/kahoot_icons';
	import { flip } from 'svelte/animate';
	import BrownButton from '$lib/components/buttons/brown.svelte';
	import MediaComponent from '$lib/editor/MediaComponent.svelte';
	import {
		ClockIcon,
		PlayIcon,
		GearIcon,
		HammerIcon,
		DashboardIcon,
		CheckIcon,
		ShapeTriangle,
		ShapeDiamond,
		ShapeCircle,
		ShapeSquare
	} from '$lib/components/icons';

	const { t } = getLocalization();

	interface Props {
		question: Question;
		game_mode: any;
		question_index: string | number;
		solution: any;
	}

	let {
		question = $bindable(),
		game_mode = $bindable(),
		question_index,
		solution
	}: Props = $props();

	if (question.type === undefined) {
		question.type = QuizQuestionType.ABCD;
	} else {
		question.type = QuizQuestionType[question.type];
	}

	let timer_res = $state(question.time);
	let selected_answer: string = $state();

	// Stop the timer if the question is answered
	const timer = (time: string) => {
		let seconds = Number(time);
		let timer_interval = setInterval(() => {
			if (timer_res === '0') {
				clearInterval(timer_interval);
				return;
			} else {
				seconds--;
			}

			timer_res = seconds.toString();
		}, 1000);
	};
	socket.on('everyone_answered', (_) => {
		timer_res = '0';
	});

	timer(question.time);

	$effect(() => {
		if (solution !== undefined) {
			timer_res = '0';
		}
	});

	const selectAnswer = (answer: string) => {
		selected_answer = answer;
		socket.emit('submit_answer', {
			question_index: question_index,
			answer: answer
		});
	};

	const select_complex_answer = (data) => {
		selected_answer = 'a';
		const new_array = [];
		for (let i = 0; i < data.length; i++) {
			new_array.push({ answer: data[i].answer });
		}
		socket.emit('submit_answer', {
			question_index: question_index,
			answer: 'a',
			complex_answer: new_array
		});
	};

	let text_input = $state('');

	let slider_value = $state([0]);
	if (question.type === QuizQuestionType.RANGE) {
		slider_value[0] = (question.answers.max - question.answers.min) / 2 + question.answers.min;
	}
	const set_answer_if_not_set_range = (time) => {
		if (question.type !== QuizQuestionType.RANGE) {
			return;
		}
		if (selected_answer === undefined && time === '0') {
			selected_answer = `${slider_value[0]}`;
			selectAnswer(selected_answer);
		}
	};

	if (question.type === QuizQuestionType.ORDER) {
		for (let i = 0; i < question.answers.length; i++) {
			question.answers[i] = { ...question.answers[i], id: i };
		}
	}

	const swapArrayElements = (arr, a: number, b: number) => {
		let _arr = [...arr];
		let temp = _arr[a];
		_arr[a] = _arr[b];
		_arr[b] = temp;
		return _arr;
	};
	$effect(() => {
		set_answer_if_not_set_range(timer_res);
	});

	let progress_percent = $derived.by(() => {
		try {
			return (parseInt(timer_res) / parseInt(question.time)) * 100;
		} catch {
			return 0;
		}
	});

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
</script>

<div class="h-screen w-screen flex flex-col font-vt select-none p-3 max-w-7xl mx-auto justify-between">
	<!-- Top Bar: Boss Health Timer / Question index / Level Counter -->
	<div class="w-full flex flex-col gap-1 pt-1 z-30">
		<div class="flex items-center justify-between px-2 font-minecraft text-xs text-[#ffff55] mc-text-shadow">
			<span class="flex items-center gap-1.5">
				<ClockIcon class="w-3.5 h-3.5" />
				<span>TIMER</span>
			</span>
			<span class="mc-xp-level text-lg">{timer_res}s</span>
			<span>Q #{question_index}</span>
		</div>

		<!-- Countdown Progress Bar -->
		<div class="mc-boss-bar-track w-full">
			<div
				class="mc-boss-bar-fill"
				style="width: {progress_percent}%;"
			></div>
		</div>
	</div>

	<!-- Question Prompt Display -->
	{#if game_mode === 'normal'}
		<div class="mc-panel-dark p-4 my-2 flex flex-col items-center justify-center text-center shadow-xl">
			<h1 class="font-minecraft text-lg sm:text-2xl md:text-3xl text-[#ffffff] mc-text-shadow leading-relaxed">
				{@html question.question}
			</h1>
			{#if question.image !== null && game_mode !== 'kahoot'}
				<div class="max-h-[30vh] mt-2 border-2 border-[#555555]">
					<MediaComponent
						src={question.image}
						css_classes="object-contain max-h-[28vh] mx-auto"
					/>
				</div>
			{/if}
		</div>
	{/if}

	<!-- Answers Arena -->
	{#if timer_res !== '0'}
		{#if question.type === QuizQuestionType.ABCD || question.type === QuizQuestionType.VOTING}
			<div class="grid grid-cols-1 sm:grid-cols-2 gap-4 w-full flex-1 pb-4">
				{#each question.answers as answer, i}
					{@const ore = ore_styles[i % 4]}
					{@const ShapeComp = shape_components[i % 4]}
					<button
						class="flex items-center justify-between p-4 border-4 transition-transform active:scale-95 disabled:opacity-60 cursor-pointer"
						style="background: {ore.bg}; border-color: {ore.border}; box-shadow: {ore.shadow};"
						disabled={selected_answer !== undefined}
						onclick={() => selectAnswer(answer.answer)}
					>
						<div class="flex items-center gap-3">
							<div class="w-8 h-8 flex items-center justify-center text-white/90 drop-shadow">
								<ShapeComp class="w-6 h-6" />
							</div>
							{#if game_mode === 'kahoot'}
								<img
									class="h-10 inline-block"
									alt="Icon"
									src={kahoot_icons[i]}
								/>
							{:else}
								<span class="font-minecraft text-base sm:text-xl text-white mc-text-shadow text-left">
									{answer.answer}
								</span>
							{/if}
						</div>

						<span class="font-minecraft text-xs text-white/70 uppercase tracking-widest hidden sm:inline">
							[{ore.name}]
						</span>
					</button>
				{/each}
			</div>
		{:else if question.type === QuizQuestionType.RANGE}
			{#await import('svelte-range-slider-pips')}
				<Spinner />
			{:then c}
				<div class="mc-panel-dark p-6 my-auto max-w-2xl mx-auto w-full shadow-2xl">
					<h2 class="font-minecraft text-center text-lg text-[#ffff55] mb-4 flex items-center justify-center gap-2">
						<GearIcon class="w-5 h-5" />
						<span>SELECT RANGE VALUE</span>
					</h2>
					<div class:pointer-events-none={selected_answer !== undefined} class="my-6">
						<c.default
							bind:values={slider_value}
							bind:min={question.answers.min}
							bind:max={question.answers.max}
							id="pips-slider"
							pips
							float
							all="label"
						/>
					</div>
					<div class="flex justify-center mt-6">
						<div class="w-2/3">
							<BrownButton onclick={() => selectAnswer(slider_value[0])}>
								<div class="flex items-center justify-center gap-2">
									<CheckIcon class="w-4 h-4" />
									<span>{$t('words.submit')} ({slider_value[0]})</span>
								</div>
							</BrownButton>
						</div>
					</div>
				</div>
			{/await}
		{:else if question.type === QuizQuestionType.TEXT}
			<div class="mc-panel-dark p-6 my-auto max-w-xl mx-auto w-full shadow-2xl flex flex-col items-center">
				<div class="w-10 h-10 mb-2 flex items-center justify-center text-[#ffff55]">
					<HammerIcon class="w-8 h-8" />
				</div>
				<h2 class="font-minecraft text-xl text-[#ffff55] mb-4 mc-text-shadow-gold">
					ENTER ANSWER
				</h2>
				<input
					type="text"
					bind:value={text_input}
					disabled={selected_answer !== undefined}
					placeholder="Type answer here..."
					class="mc-input w-full text-center text-2xl font-minecraft text-[#55ff55] py-3 mb-6"
				/>
				<div class="w-2/3">
					<BrownButton
						type="button"
						disabled={!text_input || text_input.length === 0}
						onclick={() => {
							selectAnswer(text_input);
						}}
					>
						<div class="flex items-center justify-center gap-2">
							<CheckIcon class="w-4 h-4" />
							<span>{$t('words.submit')}</span>
						</div>
					</BrownButton>
				</div>
			</div>
		{:else if question.type === QuizQuestionType.ORDER}
			<div class="mc-panel-dark p-4 my-auto max-w-2xl mx-auto w-full shadow-2xl flex flex-col gap-3">
				<h2 class="font-minecraft text-center text-sm sm:text-base text-[#ffff55] flex items-center justify-center gap-2">
					<DashboardIcon class="w-4 h-4" />
					<span>ORDER ITEMS</span>
				</h2>
				{#each question.answers as answer, i (answer.id)}
					<div
						class="mc-slot-dark p-2 flex items-center justify-between border-2"
						animate:flip={{ duration: 100 }}
					>
						<button
							onclick={() => {
								question.answers = swapArrayElements(question.answers, i, i - 1);
							}}
							class="mc-btn p-2 text-sm disabled:opacity-30"
							type="button"
							aria-label="Move item up"
							disabled={i === 0 || Boolean(selected_answer)}
						>
							▲
						</button>
						<p class="font-minecraft text-base sm:text-lg text-[#ffffff] px-2 text-center">
							{answer.answer}
						</p>
						<button
							onclick={() => {
								question.answers = swapArrayElements(question.answers, i, i + 1);
							}}
							class="mc-btn p-2 text-sm disabled:opacity-30"
							type="button"
							aria-label="Move item down"
							disabled={i === question.answers.length - 1 || Boolean(selected_answer)}
						>
							▼
						</button>
					</div>
				{/each}
				<div class="mt-4">
					<BrownButton
						type="button"
						disabled={Boolean(selected_answer)}
						onclick={() => {
							select_complex_answer(question.answers);
						}}
					>
						<div class="flex items-center justify-center gap-2">
							<CheckIcon class="w-4 h-4" />
							<span>{$t('words.submit')}</span>
						</div>
					</BrownButton>
				</div>
			</div>
		{:else if question.type === QuizQuestionType.CHECK}
			{#await import('./questions/check.svelte')}
				<Spinner />
			{:then c}
				<c.default
					{question}
					bind:selected_answer
					{game_mode}
					{timer_res}
					circular_progress={1 - progress_percent / 100}
				/>
				<div class="flex justify-center mt-4">
					<div class="w-1/2">
						<BrownButton
							type="button"
							disabled={selected_answer === undefined}
							onclick={() => selectAnswer(selected_answer)}
						>
							<div class="flex items-center justify-center gap-2">
								<CheckIcon class="w-4 h-4" />
								<span>{$t('words.submit')}</span>
							</div>
						</BrownButton>
					</div>
				</div>
			{/await}
		{/if}
	{:else}
		<!-- Time's Up Screen -->
		<div class="mc-panel-dark p-8 m-auto text-center shadow-2xl max-w-md">
			<div class="w-12 h-12 mx-auto mb-2 flex items-center justify-center text-[#ff5555]">
				<ClockIcon class="w-10 h-10" />
			</div>
			<h2 class="font-minecraft text-2xl text-[#ff5555] mc-text-shadow-redstone">
				TIME'S UP!
			</h2>
			<p class="font-vt text-xl text-[#dcdcdc] mt-2">Calculating results...</p>
		</div>
	{/if}
</div>
