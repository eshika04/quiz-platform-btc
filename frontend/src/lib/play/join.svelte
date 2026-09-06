<!--
SPDX-FileCopyrightText: 2023 Marlon W (Mawoka)

SPDX-License-Identifier: MPL-2.0
-->

<script lang="ts">
	import { socket } from '$lib/socket';
	import { onDestroy, onMount } from 'svelte';
	import { browser } from '$app/environment';
	import * as Sentry from '@sentry/browser';
	import { getLocalization } from '$lib/i18n';
	import Cookies from 'js-cookie';
	import BrownButton from '$lib/components/buttons/brown.svelte';
	import { hcaptcha_site_key, recaptcha_key, sentry_dsn } from '$lib/config';
	import { CompassIcon, PlayIcon, CheckIcon } from '$lib/components/icons';

	const { t } = getLocalization();

	interface Props {
		game_pin: string;
		game_mode: any;
		username: any;
	}

	let {
		game_pin = $bindable(),
		game_mode = $bindable(),
		username = $bindable()
	}: Props = $props();
	let custom_field = $state();
	let custom_field_value = $state();
	let captcha_enabled = $state();

	let hcaptchaSitekey = hcaptcha_site_key;

	let hcaptcha = {
		execute: async (_a, _b) => ({ response: '' }), // eslint-disable-line @typescript-eslint/no-unused-vars
		// eslint-disable-next-line @typescript-eslint/no-empty-function
		render: (_a, _b) => {} // eslint-disable-line @typescript-eslint/no-unused-vars
	};
	let hcaptchaWidgetID;

	onMount(() => {
		if (browser) {
			prefetch_username();
			hcaptcha = window.hcaptcha;
			if (hcaptcha.render) {
				hcaptchaWidgetID = hcaptcha.render('hcaptcha', {
					sitekey: hcaptchaSitekey,
					size: 'invisible',
					theme: 'dark'
				});
			}
		}
	});

	onDestroy(() => {
		if (browser) {
			hcaptcha = {
				execute: async () => ({ response: '' }),
				// eslint-disable-next-line @typescript-eslint/no-empty-function
				render: () => {}
			};
		}
	});

	const prefetch_username = async () => {
		const res = await fetch('/api/v1/users/me');
		if (res.status !== 200) {
			return;
		}
		const json = await res.json();
		username = json.username;
	};

	const set_game_pin = async () => {
		let process_var;
		try {
			process_var = process;
		} catch {
			process_var = { env: { API_URL: undefined } };
		}

		const res = await fetch(
			`${process_var.env.API_URL ?? ''}/api/v1/quiz/play/check_captcha/${game_pin}`
		);
		const json = await res.json();
		game_mode = json.game_mode;
		if (res.status === 200) {
			captcha_enabled = json.enabled;
			custom_field = json.custom_field;
		}
		if (res.status === 404) {
			/*			alertModal.set({
                open: true,
                title: 'Game not found',
                body: 'The game pin you entered seems invalid.'
            });*/
			if (browser) {
				alert('Game not found');
			}
			game_pin = '';
			return;
		}
		if (res.status !== 200) {
			/*			alertModal.set({
                open: true,
                body: `Unknown error with response-code ${res.status}`,
                title: 'Unknown Error'
            });*/
			alert('Unknown error');
			return;
		}
	};

	$effect(() => {
		if (game_pin.length > 5) {
			set_game_pin();
		}
	});

	const setUsername = async (e: Event) => {
		e.preventDefault();
		if (username.length <= 3) {
			return;
		}
		let captcha_resp: string;
		if (Cookies.get('kicked')) {
			console.log("%cYou're Banned!", 'font-size:6rem');
			return;
		}

		if (captcha_enabled) {
			if (hcaptchaSitekey) {
				try {
					const { response } = await hcaptcha.execute(hcaptchaWidgetID, {
						async: true
					});
					captcha_resp = response;
					socket.emit('join_game', {
						username: username,
						game_pin: game_pin,
						captcha: captcha_resp,
						custom_field: custom_field ? custom_field_value : undefined
					});
				} catch (e) {
					if (sentry_dsn !== null) {
						Sentry.captureException(e);
					}
					/*					alertModal.set({
                        open: true,
                        body: "The captcha failed, which is normal, but most of the time it's fixed by reloading!",
                        title: 'Captcha failed'
                    });*/
					alert('Captcha failed!');
					window.location.reload();
				}
			} else if (recaptcha_key) {
				// eslint-disable-next-line no-undef
				grecaptcha.ready(() => {
					// eslint-disable-next-line no-undef
					grecaptcha.execute(recaptcha_key, { action: 'submit' }).then(function (token) {
						socket.emit('join_game', {
							username: username,
							game_pin: game_pin,
							captcha: token,
							custom_field: custom_field ? custom_field_value : undefined
						});
					});
				});
			}
		} else {
			socket.emit('join_game', {
				username: username,
				game_pin: game_pin,
				captcha: undefined,
				custom_field: custom_field ? custom_field_value : undefined
			});
		}
	};
	socket.on('game_not_found', () => {
		game_pin = '';
		if (browser) {
			alert('Game not found');
		}
	});
	$effect(() => {
		const cleaned = game_pin.replace(/\D/g, '');
		if (game_pin.replace(/\D/g, '') === game_pin) {
			return;
		}
		game_pin = cleaned;
	});
</script>

<svelte:head>
	{#if captcha_enabled && hcaptchaSitekey}
		<script src="https://js.hcaptcha.com/1/api.js" async defer></script>
	{/if}
	{#if recaptcha_key && captcha_enabled}
		<script src="https://www.google.com/recaptcha/api.js?render={recaptcha_key}"></script>
	{/if}
</svelte:head>

{#if game_pin === '' || game_pin.length < 6}
	<div class="flex flex-col justify-center items-center w-screen h-screen p-4">
		<div class="mc-panel-dark p-8 max-w-md w-full shadow-2xl flex flex-col items-center">
			<div class="mb-6 text-center">
				<div class="w-12 h-12 mx-auto mb-2 flex items-center justify-center text-[#ffff55]">
					<CompassIcon class="w-10 h-10" />
				</div>
				<h1 class="font-minecraft text-xl sm:text-2xl text-[#ffff55] mc-text-shadow-gold">
					DIRECT CONNECT
				</h1>
				<p class="font-vt text-lg text-[#a0a0a0] mt-1">ClassQuiz Game</p>
			</div>

			<form class="flex flex-col w-full">
				<label for="game-pin-input" class="font-minecraft text-xs text-[#dcdcdc] mb-2 uppercase">
					{$t('words.game_pin')}:
				</label>
				<input
					id="game-pin-input"
					class="mc-input text-center text-3xl font-minecraft tracking-widest text-[#55ff55] py-3 w-full"
					bind:value={game_pin}
					maxlength="6"
					inputmode="numeric"
					placeholder="000000"
					autofocus
				/>

				<div class="mt-6 w-full">
					<BrownButton disabled={game_pin.length < 6}>
						<div class="flex items-center justify-center gap-2">
							<CheckIcon class="w-4 h-4" />
							<span>{$t('words.submit')}</span>
						</div>
					</BrownButton>
				</div>
			</form>
		</div>
	</div>
{:else}
	<div class="flex flex-col justify-center items-center w-screen h-screen p-4">
		<div class="mc-panel-dark p-8 max-w-md w-full shadow-2xl flex flex-col items-center">
			<div class="mb-6 text-center">
				<div class="w-16 h-16 mx-auto mb-3 bg-[#4a3b32] border-4 border-[#1e1e1e] shadow-[inset_2px_2px_0_#2b221c] flex items-center justify-center text-[#55ff55]">
					<PlayIcon class="w-8 h-8" />
				</div>
				<h1 class="font-minecraft text-xl sm:text-2xl text-[#ffff55] mc-text-shadow-gold">
					PLAYER PROFILE
				</h1>
				<p class="font-vt text-lg text-[#55ff55] mt-1">Game PIN: #{game_pin}</p>
			</div>

			<form onsubmit={setUsername} class="flex flex-col w-full">
				<label for="username-input" class="font-minecraft text-xs text-[#dcdcdc] mb-2 uppercase">
					{$t('words.username')}:
				</label>
				<input
					id="username-input"
					class="mc-input text-center text-2xl font-minecraft text-[#ffff55] py-2.5 w-full mb-4"
					bind:value={username}
					maxlength="17"
					placeholder="Player_One"
					autofocus
				/>

				{#if custom_field}
					<label for="custom-field-input" class="font-minecraft text-xs text-[#dcdcdc] mb-2 uppercase">
						{custom_field}:
					</label>
					<input
						id="custom-field-input"
						class="mc-input text-center text-xl font-vt text-[#ffffff] py-2 w-full mb-4"
						bind:value={custom_field_value}
					/>
				{/if}

				<div class="mt-4 w-full">
					<BrownButton disabled={username.length <= 3} onclick={setUsername}>
						<div class="flex items-center justify-center gap-2">
							<PlayIcon class="w-4 h-4" />
							<span>JOIN GAME</span>
						</div>
					</BrownButton>
				</div>
			</form>
		</div>
	</div>
{/if}
<div
	id="hcaptcha"
	class="h-captcha"
	data-sitekey={hcaptchaSitekey}
	data-size="invisible"
	data-theme="dark"
></div>
