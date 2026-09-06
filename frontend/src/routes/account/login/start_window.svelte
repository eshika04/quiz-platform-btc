<!--
SPDX-FileCopyrightText: 2023 Marlon W (Mawoka)

SPDX-License-Identifier: MPL-2.0
-->

<script lang="ts">
	import { getLocalization } from '$lib/i18n';
	import OAuthBlock from './oauth_block.svelte';
	import { LoginIcon, ClockIcon } from '$lib/components/icons';

	let { session_data = $bindable({}), step = $bindable() } = $props();

	const { t } = getLocalization();
	let email = $state('');
	let emailEmpty = $derived(email === '');
	let isSubmitting = $state(false);

	const start_login = async (e: Event): Promise<void> => {
		e.preventDefault();
		if (emailEmpty) {
			return;
		}
		isSubmitting = true;

		const res = await fetch('/api/v1/login/start', {
			method: 'post',
			headers: {
				'Content-Type': 'application/json'
			},
			body: JSON.stringify({ email: email })
		});
		session_data = await res.json();
		step = 1;
	};
</script>

<div class="p-2 font-vt">
	<div class="text-center mb-4">
		<div class="w-10 h-10 mx-auto mb-1 flex items-center justify-center text-[#ffff55]">
			<LoginIcon class="w-8 h-8" />
		</div>
		<h2 class="font-minecraft text-xl text-[#ffff55] mc-text-shadow-gold">ClassQuiz</h2>
		<h3 class="font-minecraft text-xs text-[#55ff55] mt-1">
			{$t('login_page.welcome_back')}
		</h3>
		<p class="font-vt text-base text-[#a0a0a0] mt-1">
			{$t('login_page.login_or_create_account')}
		</p>
	</div>

	<form onsubmit={start_login}>
		<div class="w-full flex flex-col gap-3">
			<div>
				<label for="email" class="font-minecraft text-xs text-[#dcdcdc] block mb-1">
					{$t('login_page.email_or_username')}:
				</label>
				<input
					id="email"
					bind:value={email}
					name="email"
					type="text"
					class="mc-input w-full text-lg py-2 px-3"
					placeholder="player@example.com"
					autocomplete="email"
					autofocus
				/>
			</div>

			<div class="flex items-center justify-between mt-2">
				<a
					href="/account/reset-password"
					class="font-vt text-base text-[#4eedf5] hover:underline"
				>
					{$t('register_page.forgot_password?')}
				</a>

				<button
					class="mc-btn-green mc-btn font-minecraft text-xs py-2 px-4"
					disabled={emailEmpty}
					type="submit"
				>
					{#if isSubmitting}
						<ClockIcon class="w-4 h-4 animate-spin" />
					{:else}
						{$t('words.continue')} ▶
					{/if}
				</button>
			</div>
			<OAuthBlock />
		</div>
	</form>

	<div class="mt-4 pt-3 border-t border-[#444444] text-center font-vt text-base">
		<span class="text-[#a0a0a0]">
			{$t('login_page.already_have_account')}
		</span>
		<a
			href="/account/register"
			class="font-minecraft text-xs text-[#55ff55] hover:underline ml-2"
		>
			{$t('words.register')}
		</a>
	</div>
</div>
