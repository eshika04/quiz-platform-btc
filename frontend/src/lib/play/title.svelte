<!--
SPDX-FileCopyrightText: 2023 Marlon W (Mawoka)

SPDX-License-Identifier: MPL-2.0
-->

<script lang="ts">
	import { DocsIcon } from '$lib/components/icons';

	interface Props {
		title: string;
		description: string;
		cover_image: string | undefined;
	}

	let { title, description, cover_image }: Props = $props();
</script>

<div class="flex flex-col justify-center items-center w-screen h-screen p-4 font-vt">
	<div class="mc-panel-dark p-8 max-w-2xl w-full shadow-2xl text-center">
		<div class="w-12 h-12 mx-auto mb-2 flex items-center justify-center text-[#ffff55]">
			<DocsIcon class="w-10 h-10" />
		</div>
		<h1 class="font-minecraft text-2xl sm:text-4xl md:text-5xl text-[#ffff55] mc-text-shadow-gold leading-tight">
			{@html title}
		</h1>
		{#if description}
			<p class="font-vt text-xl sm:text-2xl text-[#dcdcdc] pt-4 leading-relaxed">
				{@html description}
			</p>
		{/if}
		{#if cover_image}
			<div class="flex justify-center items-center mt-6">
				<div class="mc-slot-dark p-2 max-h-[35vh]">
					<img
						class="max-h-[30vh] max-w-full block border border-[#555555]"
						src="/api/v1/storage/download/{cover_image}"
						alt="Cover"
					/>
				</div>
			</div>
		{/if}
		<div class="mt-6 font-minecraft text-xs text-[#55ff55] animate-pulse">
			▶ PREPARING GAME...
		</div>
	</div>
</div>
