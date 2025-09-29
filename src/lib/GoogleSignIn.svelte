<script context="module" lang="ts">
	declare const google: any;
</script>

<script lang="ts">
	import { onMount } from 'svelte';
	import { api } from '$lib/api';

	let googleButtonWrapper: HTMLElement;

	onMount(async () => {
		let response = await api.getNonce();
		let nonce = response.message;
		const script = document.createElement('script');
		script.src = 'https://accounts.google.com/gsi/client';
		script.async = true;
		script.defer = true;
		script.onload = () => {
			google.accounts.id.initialize({
				client_id: '988343938519-vle7kps2l5f6cdnjluibda25o66h2jpn.apps.googleusercontent.com',
				callback: handleCredentialResponse,
				nonce: nonce
			});
			google.accounts.id.renderButton(
				googleButtonWrapper,
				{ theme: 'outline', size: 'large' } // Customization options
			);
			google.accounts.id.prompt(); // Also display the One Tap prompt
		};
		document.head.appendChild(script);
	});

	async function handleCredentialResponse(response: any) {
		console.log(response);
	}
</script>

<div bind:this={googleButtonWrapper}></div>
