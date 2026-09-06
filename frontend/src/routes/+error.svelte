<!--
SPDX-FileCopyrightText: 2023 Marlon W (Mawoka)

SPDX-License-Identifier: MPL-2.0
-->

<script lang="ts">
	import { navbarVisible } from '$lib/stores.svelte.ts';
	import { page } from '$app/state';
	import { getLocalization } from '$lib/i18n';
	import { CompassIcon, PlayIcon } from '$lib/components/icons';

	navbarVisible.visible = true;
	let status = page.status;
	const { t } = getLocalization();
</script>

<svelte:head>
	<title>ClassQuiz - {$t('words.error')} {status}</title>
</svelte:head>

<div class="min-h-[80vh] flex flex-col justify-center items-center p-4 font-vt text-center">
	<div class="mc-panel-dark p-8 max-w-lg w-full shadow-2xl border-4">
		<h1 class="font-minecraft text-3xl sm:text-4xl text-[#ff5555] mc-text-shadow-redstone mb-4">
			{#if status === 404}
				PAGE NOT FOUND
			{:else}
				CONNECTION ERROR
			{/if}
		</h1>

		<p class="font-minecraft text-base text-[#ffff55] mb-4">
			Error Code: #{status}
		</p>

		{#if status === 404}
			<p class="font-vt text-xl text-[#dcdcdc] leading-relaxed mb-6">
				{$t('error_page.404_text')}
			</p>
		{:else}
			<p class="font-vt text-xl text-[#dcdcdc] leading-relaxed mb-6">
				{$t('error_page.unknown_error_text')}
			</p>
		{/if}

		<div class="flex flex-col gap-3">
			<a href="/" class="mc-btn-green mc-btn font-minecraft text-sm py-2.5 w-full uppercase flex items-center justify-center gap-2">
				<CompassIcon class="w-4 h-4" />
				<span>GO HOME</span>
			</a>
			<a href="/play" class="mc-btn font-minecraft text-sm py-2.5 w-full uppercase flex items-center justify-center gap-2">
				<PlayIcon class="w-4 h-4" />
				<span>PLAY QUIZ</span>
			</a>
		</div>
	</div>
</div>
