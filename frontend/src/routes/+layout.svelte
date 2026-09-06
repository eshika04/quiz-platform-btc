<!--
SPDX-FileCopyrightText: 2023 Marlon W (Mawoka)

SPDX-License-Identifier: MPL-2.0
-->

<script lang="ts">
	import '../app.css';
	import Navbar from '$lib/navbar.svelte';
	import { pathname } from '$lib/stores';
	import { navbarVisible } from '$lib/stores.svelte';

	import { initLocalizationContext } from '$lib/i18n';
	import { browser } from '$app/environment';
	import CommandPalette from '$lib/components/commandpalette.svelte';
	import { plausible_data_url } from '$lib/config';
	interface Props {
		children?: import('svelte').Snippet;
	}

	let { children }: Props = $props();

	if (browser) {
		pathname.set(window.location.pathname);
		if (
			localStorage.theme === 'dark' ||
			(!('theme' in localStorage) &&
				window.matchMedia('(prefers-color-scheme: dark)').matches)
		) {
			document.documentElement.classList.add('dark');
		} else {
			document.documentElement.classList.remove('dark');
		}
	}
	let start_language = 'en';
	const rtl_languages = ['he', 'prs', 'ps'];
	if (browser) {
		start_language = localStorage.getItem('language') ?? 'en';
		document.documentElement.lang = start_language;
		document.documentElement.dir = rtl_languages.includes(start_language) ? 'rtl' : 'ltr';
	}
	initLocalizationContext(start_language);
</script>

<svelte:head>
	{#if plausible_data_url}
		<script
			defer
			data-domain={plausible_data_url}
			src="https://plausible.nexus.mawoka.eu/js/script.file-downloads.outbound-links.pageview-props.tagged-events.js"
		></script>
		<script>
			window.plausible =
				window.plausible ||
				function () {
					(window.plausible.q = window.plausible.q || []).push(arguments);
				};
		</script>
	{/if}
</svelte:head>

<div class="fixed inset-0 pointer-events-none overflow-hidden z-0 opacity-40">
	<div class="mc-cloud-layer"></div>
</div>

<div class="relative z-10 min-h-screen flex flex-col">
	{#if navbarVisible.visible}
		<Navbar />
		<div class="pt-16">
			<div class="z-40"></div>
		</div>
	{/if}
	{@render children?.()}
	<CommandPalette />
</div>

<style lang="scss">
	:global(html:not(.dark)) {
		background-color: #6da2f7;
		background-image: 
			radial-gradient(#89b8ff 15%, transparent 16%),
			linear-gradient(180deg, #5b95f7 0%, #87b5ff 60%, #b8d4ff 100%);
		background-size: 32px 32px, 100% 100%;
		min-height: 100vh;
		color: #1a1a1a;
	}

	:global(html.dark) {
		background-color: #141418;
		background-image: 
			radial-gradient(#2a2a35 20%, transparent 20%),
			radial-gradient(#1e1e24 20%, transparent 20%),
			linear-gradient(180deg, #0d0e12 0%, #17181f 60%, #22232c 100%);
		background-size: 24px 24px, 24px 24px, 100% 100%;
		background-position: 0 0, 12px 12px, 0 0;
		min-height: 100vh;
		color: #ffffff;

		:global(#pips-slider) {
			--pip: #55ff55;
			--pip-active: #ffffff;
		}
	}

	.mc-cloud-layer {
		position: absolute;
		top: 40px;
		left: 0;
		width: 200%;
		height: 120px;
		background: 
			linear-gradient(90deg, 
				rgba(255,255,255,0.85) 0%, rgba(255,255,255,0.85) 120px, 
				transparent 120px, transparent 180px,
				rgba(255,255,255,0.7) 180px, rgba(255,255,255,0.7) 340px,
				transparent 340px, transparent 420px,
				rgba(255,255,255,0.8) 420px, rgba(255,255,255,0.8) 600px,
				transparent 600px, transparent 800px
			);
		background-size: 800px 48px;
		animation: mc-clouds-drift 60s linear infinite;
	}

	@keyframes mc-clouds-drift {
		0% {
			transform: translateX(0);
		}
		100% {
			transform: translateX(-50%);
		}
	}
</style>
