<!--
SPDX-FileCopyrightText: 2023 Marlon W (Mawoka)

SPDX-License-Identifier: MPL-2.0
-->

<script lang="ts">
	import { DocsIcon, CloseIcon, SparklesIcon } from '$lib/components/icons';

	interface Props {
		open: boolean;
	}

	let { open = $bindable() }: Props = $props();

	const closeThing = () => {
		open = false;
		localStorage.setItem('newsletter', 'closed');
	};
</script>

<form method="post" action="https://newsletter.mawoka.eu/subscription/form" class="font-vt text-white">
	<div class="flex items-center justify-between mb-2 pb-1 border-b border-[#555555]">
		<h3 class="font-minecraft text-xs text-[#ffff55] mc-text-shadow-gold flex items-center gap-1.5">
			<DocsIcon class="w-3.5 h-3.5" />
			<span>NEWSLETTER</span>
		</h3>
		<button type="button" onclick={closeThing} class="font-minecraft text-xs text-[#a0a0a0] hover:text-white p-1" aria-label="Close newsletter">
			<CloseIcon class="w-3 h-3" />
		</button>
	</div>
	<div class="flex flex-col gap-2">
		<input type="hidden" name="nonce" />
		<input
			type="email"
			name="email"
			required
			placeholder="E-mail"
			class="mc-input text-base py-1 px-2 w-full"
		/>
		<input
			type="text"
			name="name"
			placeholder="Name (optional)"
			class="mc-input text-base py-1 px-2 w-full"
		/>

		<p class="hidden">
			<input
				id="fec55"
				type="checkbox"
				name="l"
				checked
				value="fec5595e-9af1-497a-b7eb-1edd7ad2e21c"
			/>
			<label for="fec55">ClassQuiz-News</label>
		</p>
		<button type="submit" class="mc-btn-green mc-btn font-minecraft text-xs py-1.5 w-full mt-1 flex items-center justify-center gap-1.5">
			<SparklesIcon class="w-3.5 h-3.5" />
			<span>Subscribe</span>
		</button>
	</div>
</form>
