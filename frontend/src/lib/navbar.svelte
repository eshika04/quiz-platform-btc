<!--
SPDX-FileCopyrightText: 2023 Marlon W (Mawoka)

SPDX-License-Identifier: MPL-2.0
-->

<script lang="ts">
	import { getLocalization } from '$lib/i18n';
	import { signedIn, pathname } from '$lib/stores';
	import { createTippy } from 'svelte-tippy';
	import { browser } from '$app/environment';
	import { beforeNavigate } from '$app/navigation';
	import { slide } from 'svelte/transition';
	import { registration_disabled } from './config';
	import {
		PlayIcon,
		CompassIcon,
		SearchIcon,
		DashboardIcon,
		DocsIcon,
		LogoutIcon,
		LoginIcon,
		SunIcon,
		MoonIcon,
		MenuIcon,
		CloseIcon,
		HeartIcon
	} from '$lib/components/icons';

	const tippy = createTippy({
		arrow: true,
		animation: 'perspective-subtle',
		placement: 'bottom'
	});

	const { t } = getLocalization();

	let menuIsClosed = $state(true);
	const toggleMenu = () => {
		menuIsClosed = !menuIsClosed;
	};

	beforeNavigate(() => {
		menuIsClosed = true; // Closes menu to let the user see the page beneath
	});

	let darkMode = $state(false);
	if (browser) {
		darkMode = localStorage.theme === 'dark';
	}

	const switchDarkMode = () => {
		!darkMode ? localStorage.setItem('theme', 'dark') : localStorage.setItem('theme', 'light');
		window.location.reload();
	};
</script>

<nav class="w-screen px-4 lg:px-8 py-2 fixed z-40 top-0 bg-[#2b2b2b]/95 backdrop-blur-md border-b-4 border-[#101010] shadow-[0_4px_0_rgba(0,0,0,0.5)]">
	<!-- Desktop navbar -->
	<div class="hidden lg:flex lg:items-center lg:flex-row lg:justify-between">
		<div class="lg:flex lg:items-center lg:flex-row gap-2">
			<a
				href="/"
				class="flex items-center gap-2 font-minecraft text-xl tracking-wider text-[#ffff55] mc-text-shadow-gold px-3 py-1 bg-[#1e1e1e] border-2 border-[#555555] shadow-[inset_2px_2px_0px_#000000] hover:border-[#ffff55] transition-colors"
			>
				<span class="inline-block w-5 h-5 bg-[#5b8e34] border border-[#1e1e1e] shadow-[inset_0_2px_0_#72ac35,inset_0_-2px_0_#866043]"></span>
				<span>CLASSQUIZ</span>
			</a>

			<a class="mc-btn font-minecraft text-xs py-1.5 px-3 uppercase tracking-wider flex items-center gap-1.5" href="/play">
				<PlayIcon class="w-3.5 h-3.5" />
				<span>{$t('words.play')}</span>
			</a>
			<a class="btn-nav hover:text-[#ffff55] transition-colors flex items-center gap-1.5" href="/explore">
				<CompassIcon class="w-4 h-4" />
				<span>{$t('words.explore')}</span>
			</a>
			<a class="btn-nav hover:text-[#ffff55] transition-colors flex items-center gap-1.5" href="/search">
				<SearchIcon class="w-4 h-4" />
				<span>{$t('words.search')}</span>
			</a>
			{#if $signedIn}
				<a class="btn-nav hover:text-[#ffff55] transition-colors flex items-center gap-1.5" href="/dashboard">
					<DashboardIcon class="w-4 h-4" />
					<span>{$t('words.dashboard')}</span>
				</a>
			{:else}
				<a class="btn-nav hover:text-[#ffff55] transition-colors flex items-center gap-1.5" href="/docs">
					<DocsIcon class="w-4 h-4" />
					<span>{$t('words.docs')}</span>
				</a>
				<a
					target="_blank"
					class="btn-nav flex items-center gap-1.5 hover:text-[#ffff55] transition-colors"
					href="https://github.com/mawoka-myblock/ClassQuiz"
				>
					<span>GitHub</span>
					<svg
						xmlns="http://www.w3.org/2000/svg"
						width="14"
						height="14"
						viewBox="0 0 24 24"
						fill="none"
						stroke="currentColor"
						stroke-width="2"
						stroke-linecap="round"
						stroke-linejoin="round"
					>
						<path d="M18 13v6a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V8a2 2 0 0 1 2-2h6" />
						<polyline points="15 3 21 3 21 9" />
						<line x1="10" x2="21" y1="14" y2="3" />
					</svg>
				</a>
			{/if}
		</div>

		<div class="lg:flex lg:items-center lg:flex-row gap-3">
			{#if $signedIn}
				<a class="btn-nav text-[#ff5555] hover:text-[#ff9999] flex items-center gap-1.5" href="/api/v1/users/logout">
					<LogoutIcon class="w-4 h-4" />
					<span>{$t('words.logout')}</span>
				</a>
			{:else}
				{#if registration_disabled}
					<a class="btn-nav text-white/90 hover:text-[#55ff55]" href="/account/register">
						{$t('words.register')}
					</a>
				{/if}

				<a
					class="btn-nav text-white/90 hover:text-[#55ff55] flex items-center gap-1.5"
					href="/account/login?returnTo={$pathname}"
				>
					<LoginIcon class="w-4 h-4" />
					<span>{$t('words.login')}</span>
				</a>
			{/if}

			<div class="flex items-center gap-2">
				<a
					href="https://mawoka.eu/donate"
					target="_blank"
					class="mc-btn-gold mc-btn font-minecraft text-xs py-1.5 px-3 uppercase tracking-wider flex items-center gap-1.5"
				>
					<HeartIcon class="w-3.5 h-3.5 text-red-500" />
					<span>{$t('navbar.donate')}</span>
				</a>

				<button
					onclick={switchDarkMode}
					class="mc-btn font-minecraft text-xs p-1.5 flex items-center justify-center min-w-[38px] min-h-[38px]"
					aria-label={darkMode ? 'Switch light mode on' : 'Switch dark mode on'}
					use:tippy={{ content: darkMode ? 'Switch to Light Mode' : 'Switch to Dark Mode' }}
				>
					{#if darkMode}
						<SunIcon class="w-4 h-4 text-[#ffff55]" />
					{:else}
						<MoonIcon class="w-4 h-4 text-[#4eedf5]" />
					{/if}
				</button>
			</div>
		</div>
	</div>

	<!-- Mobile navbar -->
	<div class="lg:hidden">
		<!-- Navbar header -->
		<div class="flex items-center justify-between">
			<a
				href="/"
				class="flex items-center gap-1.5 font-minecraft text-lg text-[#ffff55] mc-text-shadow-gold"
			>
				<span class="inline-block w-4 h-4 bg-[#5b8e34] border border-[#1e1e1e]"></span>
				<span>CLASSQUIZ</span>
			</a>

			<div class="flex items-center gap-2">
				<a class="mc-btn font-minecraft text-xs py-1 px-2 uppercase flex items-center gap-1" href="/play">
					<PlayIcon class="w-3 h-3" />
					<span>{$t('words.play')}</span>
				</a>

				<button
					onclick={switchDarkMode}
					class="mc-btn text-xs p-1 min-w-[32px] min-h-[32px] flex items-center justify-center"
					aria-label={darkMode ? 'Switch light mode on' : 'Switch dark mode on'}
				>
					{#if darkMode}
						<SunIcon class="w-3.5 h-3.5 text-[#ffff55]" />
					{:else}
						<MoonIcon class="w-3.5 h-3.5 text-[#4eedf5]" />
					{/if}
				</button>

				<button
					id="open-menu"
					onclick={toggleMenu}
					aria-label="Toggle navigation menu"
					class="mc-btn font-minecraft text-sm p-1.5 flex items-center justify-center min-w-[32px] min-h-[32px]"
				>
					{#if menuIsClosed}
						<MenuIcon class="w-4 h-4" />
					{:else}
						<CloseIcon class="w-4 h-4" />
					{/if}
				</button>
			</div>
		</div>

		<!-- Mobile menu drawer -->
		{#if !menuIsClosed}
			<div
				class="flex flex-col gap-2 mt-3 pt-3 border-t-2 border-[#555555] bg-[#222222] p-3 shadow-inner"
				transition:slide|global={{ duration: 250 }}
			>
				<a class="btn-nav py-2 flex items-center gap-2" href="/explore">
					<CompassIcon class="w-4 h-4" />
					<span>{$t('words.explore')}</span>
				</a>
				<a class="btn-nav py-2 flex items-center gap-2" href="/search">
					<SearchIcon class="w-4 h-4" />
					<span>{$t('words.search')}</span>
				</a>
				{#if $signedIn}
					<a class="btn-nav py-2 flex items-center gap-2" href="/dashboard">
						<DashboardIcon class="w-4 h-4" />
						<span>{$t('words.dashboard')}</span>
					</a>
					<a class="btn-nav py-2 text-[#ff5555] flex items-center gap-2" href="/api/v1/users/logout">
						<LogoutIcon class="w-4 h-4" />
						<span>{$t('words.logout')}</span>
					</a>
				{:else}
					<a class="btn-nav py-2 flex items-center gap-2" href="/docs">
						<DocsIcon class="w-4 h-4" />
						<span>{$t('words.docs')}</span>
					</a>
					{#if registration_disabled}
						<a class="btn-nav py-2 text-[#55ff55]" href="/account/register">{$t('words.register')}</a>
					{/if}
					<a class="btn-nav py-2 text-[#55ff55] flex items-center gap-2" href="/account/login?returnTo={$pathname}">
						<LoginIcon class="w-4 h-4" />
						<span>{$t('words.login')}</span>
					</a>
				{/if}

				<div class="pt-2 border-t border-[#444444]">
					<a
						href="https://mawoka.eu/donate"
						target="_blank"
						class="mc-btn-gold mc-btn font-minecraft text-xs py-2 w-full text-center flex items-center justify-center gap-2"
					>
						<HeartIcon class="w-3.5 h-3.5 text-red-500" />
						<span>{$t('navbar.donate')}</span>
					</a>
				</div>
			</div>
		{/if}
	</div>
</nav>
