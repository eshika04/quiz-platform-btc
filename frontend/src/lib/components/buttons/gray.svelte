<!--
SPDX-FileCopyrightText: 2023 Marlon W (Mawoka)

SPDX-License-Identifier: MPL-2.0
-->

<script lang="ts">
	interface Props {
		disabled?: boolean;
		flex?: boolean;
		href?: undefined | string;
		target?: undefined | string;
		children?: import('svelte').Snippet;
		onclick?: (event: MouseEvent) => void;
	}

	let {
		disabled = false,
		flex = false,
		href = undefined,
		target = '_self',
		children,
		onclick
	}: Props = $props();
</script>

{#if href}
	<a
		{href}
		{target}
		class="mc-btn w-full leading-5 text-center"
		class:opacity-50={disabled}
		class:cursor-not-allowed={disabled}
		class:pointer-events-none={disabled}
		{onclick}
		class:flex
		class:block={!flex}
		class:justify-center={flex}
	>
		{@render children?.()}
	</a>
{:else}
	<button
		{disabled}
		class="mc-btn w-full leading-5 text-center"
		{onclick}
		class:flex
		class:justify-center={flex}
	>
		{@render children?.()}
	</button>
{/if}
