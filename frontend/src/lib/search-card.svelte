<!--
SPDX-FileCopyrightText: 2023 Marlon W (Mawoka)

SPDX-License-Identifier: MPL-2.0
-->

<script lang="ts">
	import { getLocalization } from '$lib/i18n';

	const { t } = getLocalization();
	import ImportedOrNot from '$lib/view_quiz/imported_or_not.svelte';
	let { quiz } = $props();
</script>

<div class="flex justify-center font-vt">
	<a href="/view/{quiz.id}" class="h-max w-full max-w-md block">
		<div class="mc-panel-dark p-6 shadow-2xl transition-transform hover:-translate-y-1 hover:border-[#ffff55] border-4">
			<div>
				<div class="flex w-full items-center justify-between gap-2">
					<h2
						class="font-minecraft text-lg text-[#ffff55] mc-text-shadow-gold truncate"
					>
						{@html quiz.title}
					</h2>
					<span class="inline-block shrink-0">
						<ImportedOrNot imported={quiz.imported_from_kahoot} />
					</span>
				</div>
				<p
					class="mt-2 font-vt text-lg text-[#dcdcdc] break-all overflow-hidden text-ellipsis max-h-[4.5rem] block leading-relaxed"
				>
					{@html quiz.description ?? ''}
				</p>
			</div>
			<div class="flex mt-4 pt-2 border-t border-[#444444] font-minecraft text-xs text-[#55ff55]">
				<span>
					{#if quiz.imported_from_kahoot === true}
						{$t('explore_page.imported_by')}
					{:else}
						{$t('explore_page.made_by')}
					{/if}
					{@html quiz.user}
				</span>
			</div>
		</div>
	</a>
</div>
