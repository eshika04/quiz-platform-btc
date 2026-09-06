<!--
SPDX-FileCopyrightText: 2023 Marlon W (Mawoka)

SPDX-License-Identifier: MPL-2.0
-->

<script lang="ts">
	import LanguageToggle from './language-toggle.svelte';
	import { getLocalization } from '$lib/i18n';
	import { HeartIcon } from '$lib/components/icons';

	const { t } = getLocalization();
</script>

<footer class="text-center text-[#dcdcdc] w-screen bg-[#151518] border-t-4 border-[#3a3a3a] shadow-[inset_0_4px_0_#0a0a0c] font-vt text-xl">
	<div class="grid grid-cols-12 w-full pt-6 pb-4 px-4 bg-mc-bedrock">
		<p class="col-start-2 col-end-12 lg:col-start-3 lg:col-end-11 text-center w-full leading-relaxed">
			{@html $t('footer.self_ads', {
				mawoka_link:
					'<a href="https://mawoka.eu?utm_source=ClassQuiz&utm_medium=footer" target="_blank" class="underline text-[#4eedf5] hover:text-[#7af2f8] transition font-pixel text-sm">Mawoka</a>',
				others_link: `<a href='/docs/attribution' class='underline text-[#4eedf5] hover:text-[#7af2f8] transition font-pixel text-sm'>${$t(
					'words.other',
					{ count: 2 }
				)}</a>`,
				interpolation: { escapeValue: false }
			})}
			<br />

			{@html $t('footer.donate', {
				donate_link: `<a href='https://mawoka.eu/donate' target='_blank' class='underline text-[#ffff55] hover:text-[#ffffaa] transition font-pixel text-sm'>${$t(
					'words.donating'
				)}</a>`,
				interpolation: { escapeValue: false }
			})}
			<a
				href="https://mawoka.eu/donate"
				target="_blank"
				class="underline text-[#ffff55] hover:text-[#ffffaa] transition font-pixel text-sm"
				>{$t('footer.more_details_here')}.</a
			>
		</p>
		<div
			class="flex flex-col items-center min-h-full h-full md:block hidden col-start-11 col-end-12"
		>
			<div class="my-auto mr-16">
				<LanguageToggle />
			</div>
		</div>
	</div>
	<div class="md:hidden block">
		<div class="self-end flex justify-center min-h-full h-full bg-[#151518] pb-3">
			<div class="m-auto">
				<LanguageToggle />
			</div>
		</div>
	</div>

	<div class="bg-[#0e0e11] py-3 border-t border-[#26262b] text-sm font-pixel text-[#888888]">
		<p class="flex items-center justify-center gap-2">
			<span class="flex items-center gap-1.5">
				<HeartIcon class="w-3.5 h-3.5 text-red-500 inline-block" />
				<span>Made for Classrooms & Friends</span>
			</span>
			<span>•</span>
			<a
				href="https://fosstodon.org/@classquiz"
				rel="me"
				class="text-[#4eedf5] hover:underline"
				>@classquiz@fosstodon.org</a
			>
		</p>
	</div>
</footer>

<!--
<style>
	.bg-animated {
		background-size: 100% 100%;

		-webkit-animation: ColorRunner 13s ease infinite;
		-moz-animation: ColorRunner 13s ease infinite;
		-o-animation: ColorRunner 13s ease infinite;
		animation: ColorRunner 13s ease infinite;
	}

	@-webkit-keyframes ColorRunner {
		0% {
			background-position: 0% 50%;
		}
		50% {
			background-position: 100% 50%;
		}
		100% {
			background-position: 0% 50%;
		}
	}

	@-moz-keyframes ColorRunner {
		0% {
			background-position: 0% 50%;
		}
		50% {
			background-position: 100% 50%;
		}
		100% {
			background-position: 0% 50%;
		}
	}

	@-o-keyframes ColorRunner {
		0% {
			background-position: 0% 50%;
		}
		50% {
			background-position: 100% 50%;
		}
		100% {
			background-position: 0% 50%;
		}
	}

	@keyframes ColorRunner {
		0% {
			background-position: 0% 50%;
		}
		50% {
			background-position: 100% 50%;
		}
		100% {
			background-position: 0% 50%;
		}
	}
</style>
-->
