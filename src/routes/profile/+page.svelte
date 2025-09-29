<script lang="ts">
	import { afterNavigate } from '$app/navigation';
	import { api } from '$lib/api';
	import { goto } from '$app/navigation';
	import type { Profile } from '$lib/profile';

	let email = '';
	let code = '';
	let loading = false;
	let error = '';

	async function verifyEmail() {
		loading = true;
		let result = await api.verifyEmail({
			email: email,
			code: code
		});

		if (result.response_type == 'Error') {
			error = result.message;
		} else {
			error = '';
			goto('/profile');
		}
		loading = false;
	}

	let profile: Profile | null = null;
	afterNavigate(async function () {
		let response = await api.getProfile();
		if (response.response_type == 'Error') {
			goto('/login');
		} else {
			profile = JSON.parse(response.message);
			if (profile?.email) {
				email = profile.email;
			}
		}
	});
</script>

{#if profile}
	<h1 class="text-2xl">Welcome {profile.username}</h1>
	{#if profile.auth_level == 'unverified'}
		<p>Email: {email} <span class="text-red-500">&times; Unverified</span></p>
		<form on:submit|preventDefault={verifyEmail}>
			<input
				class="border border-blue-200"
				type="text"
				name="code"
				placeholder="Enter verification code"
				bind:value={code}
			/>
			<input type="hidden" bind:value={email} name="email" />
			<button class="rounded-2xl bg-blue-400 p-1 text-white">Verify Email</button>
		</form>

		{#if error}
			<div class="text-sm text-red-600">{error}</div>
		{/if}
	{:else}
		<p>Email: {email} <span class="text-green-500">&check; Verified</span></p>
		<p>Registration date: {new Date(profile.registration_ts * 1000)}</p>
	{/if}
{/if}
