<!--
SPDX-FileCopyrightText: 2023 Marlon W (Mawoka)

SPDX-License-Identifier: MPL-2.0
-->

<!--
This should be okay, right?
-->
<script lang="ts">
	import { onMount } from 'svelte';
	import { tinykeys } from '$lib/tinykeys';
	import { fade } from 'svelte/transition';
	import MiniSearch from 'minisearch';

	let open = $state(false);
	let input = $state('');
	let bg_text = $state('');
	let title_ms: MiniSearch;
	let command_ms: MiniSearch;
	let selected: null | number = $state(null);

	// eslint-disable-next-line no-unused-vars
	type ActionFunction = (args: string[]) => void;
	const actions: {
		id: number;
		title: string;
		description?: string;
		command?: string;
		args?: string[];
		action: ActionFunction;
	}[] = [
		{
			id: 0,
			title: 'Close CommandPalette',
			description: 'Closes CommandPalette',
			command: 'close',
			action: () => close_cp(undefined)
		},
		{
			id: 1,
			title: 'Create Quiz',
			description: 'Opens editor to create a new quiz',
			command: 'newquiz',
			args: ['title'],
			action: (args) => window.location.assign(`/create?title=${args.join(' ')}`)
		},
		{
			id: 2,
			title: 'Import a Quiz',
			description: 'Opens the import page',
			command: 'import',
			args: ['url'],
			action: (args) => window.location.assign(`/import?url=${args?.[0] ?? ''}`)
		},
		{
			id: 3,
			title: 'Create Quiztivity',
			description: 'Opens the editor for quiztivities',
			command: 'newquiztivity',
			args: ['title'],
			action: (args) => window.location.assign(`/quiztivity/create?title=${args.join(' ')}`)
		},
		{
			id: 4,
			title: 'View Results',
			description: 'Opens the Results viewer',
			command: 'results',
			action: () => window.location.assign('/results')
		},
		{
			id: 5,
			title: 'Explore Quizzes',
			description: 'Opens the Explore-page',
			command: 'explore',
			action: () => window.location.assign('/explore')
		},
		{
			id: 6,
			title: 'Dashboard',
			description: 'Go to Dashboard',
			command: 'dash',
			action: () => window.location.assign('/dashboard')
		},
		{
			id: 7,
			title: 'Docs',
			description: 'Go to documentation',
			command: 'docs',
			action: () => window.location.assign('/docs')
		},
		{
			id: 8,
			title: 'Settings',
			description: 'Opens the Settings page',
			command: 'settings',
			action: () => window.location.assign('/account/settings')
		}
	];
	let visible_items = $state(actions);

	const toggle_open = (e: KeyboardEvent | undefined) => {
		e.preventDefault();
		open = !open;
	};

	const close_cp = (e: KeyboardEvent | undefined) => {
		if (e) {
			e.preventDefault();
		}
		open = false;
	};

	const close_on_outside = (e: Event) => {
		if (e.target == e.currentTarget) {
			open = false;
		}
	};

	const execute_action = () => {
		let args = [];
		const entry = visible_items[selected];
		if (input.startsWith('/')) {
			const tokens = input.split(' ');
			args = tokens.slice(1);
		}
		console.log(args);
		entry.action(args);
	};

	const search = (term: string) => {
		if (!command_ms || !title_ms) {
			return;
		}
		if (term === '' || term === '/') {
			selected = 0;
			visible_items = actions;
			bg_text = '';
			return;
		}
		let suggestions;
		let res;
		if (term.startsWith('/')) {
			term = term.substring(1);
			suggestions = command_ms.autoSuggest(term, { boost: { command: 2 }, prefix: true });
			res = command_ms.search(term, { boost: { command: 2 }, prefix: true });
			bg_text = suggestions[0]?.suggestion;
			bg_text ??= '';
			bg_text = `/${bg_text}`;
		} else {
			suggestions = title_ms.autoSuggest(term, { boost: { command: 2 }, prefix: true });
			res = title_ms.search(term, { boost: { command: 2 }, prefix: true });
			bg_text = suggestions[0]?.suggestion;
			bg_text ??= '';
		}

		visible_items = [];

		for (const quiz_data of res) {
			visible_items.push(actions[quiz_data.id]);
		}
		visible_items = visible_items;
		if (visible_items.length === 1) {
			selected = 0;
		}
		if (visible_items.length === 0) {
			selected = null;
		}
	};

	const autocomplete_on_tab = (e: KeyboardEvent) => {
		e.preventDefault();
		input = bg_text;
	};

	const on_arrow_down = (e: KeyboardEvent) => {
		e.preventDefault();
		if (visible_items.length < 1) {
			return;
		}
		if (selected + 1 === visible_items.length) {
			return;
		}
		selected += 1;
	};
	const on_arrow_up = (e: KeyboardEvent) => {
		e.preventDefault();
		if (visible_items.length < 1) {
			return;
		}
		if (selected === 0) {
			return;
		}
		selected -= 1;
	};

	const on_enter = (e: KeyboardEvent) => {
		if (selected === null) {
			return;
		}
		execute_action();
		input = '';
	};

	onMount(async () => {
		tinykeys(window, {
			'$mod+k': toggle_open,
			Escape: close_cp,
			Tab: autocomplete_on_tab,
			ArrowDown: on_arrow_down,
			ArrowUp: on_arrow_up,
			Enter: on_enter
		});
		title_ms = new MiniSearch<any>({
			fields: ['title'],
			storeFields: ['id']
		});
		title_ms.addAll(actions);
		command_ms = new MiniSearch<any>({
			fields: ['command'],
			storeFields: ['id']
		});
		command_ms.addAll(actions);
	});
</script>

{#if open}
	<div
		class="fixed top-0 left-0 w-screen h-screen flex bg-black/80 z-50 p-4 font-vt text-white"
		onclick={close_on_outside}
		onkeyup={close_on_outside}
		role="button"
		aria-label="Close"
		tabindex="0"
		transition:fade|global={{ duration: 60 }}
	>
		<div class="m-auto w-full max-w-xl max-h-[80vh] mc-panel-dark flex flex-col shadow-2xl border-4">
			<div class="flex items-center gap-2 p-3 bg-[#181818] border-b-2 border-[#555555]">
				<span class="font-minecraft text-lg text-[#ffff55]">/</span>
				<div class="relative flex-1">
					<p class="absolute inset-0 font-vt text-xl text-[#666666] pointer-events-none">
						{bg_text}
					</p>
					<input
						type="text"
						class="w-full bg-transparent outline-none font-vt text-xl text-[#55ff55]"
						placeholder="Type a realm command..."
						bind:value={input}
						oninput={() => search(input)}
						autofocus
					/>
				</div>
			</div>
			<div class="flex flex-col p-3 gap-2 overflow-y-auto max-h-[60vh]">
				{#each visible_items as vi, i}
					<div
						transition:fade={{ duration: 60 }}
						class="p-2 transition-all cursor-pointer mc-slot-dark border-2"
						class:outline={selected === i}
						class:outline-2={selected === i}
						class:outline-[#ffff55]={selected === i}
						onmouseenter={() => (selected = i)}
						onmousedown={execute_action}
						tabindex="-2"
						role="button"
					>
						<div class="flex items-center justify-between">
							<h3 class="font-minecraft text-sm text-[#ffff55]">{vi.title}</h3>
							<span class="font-minecraft text-xs bg-[#000000] text-[#55ff55] px-2 py-0.5 border border-[#444444]">
								/{vi.command}
								{#if vi.args}
									{#each vi.args as arg}
										&lbrace;<span class="text-[#4eedf5]">{arg}</span
										>&rbrace;{/each}
								{/if}
							</span>
						</div>
						<p class="font-vt text-base text-[#a0a0a0] mt-1">{vi.description}</p>
					</div>
				{/each}
			</div>
		</div>
	</div>
{/if}
