<script lang="ts">
	import { afterNavigate } from '$app/navigation';
	import { api } from '$lib/api';
	import { goto } from '$app/navigation';
	import type { Profile } from '$lib/profile';

	let profile: Profile | null = null;
	afterNavigate(async function () {
		let response = await api.getProfile();
		if (response.response_type == 'Error') {
			goto('/login');
		} else {
			profile = JSON.parse(response.message);
		}
	});
</script>

{#if profile}
	<h1 class="text-3xl">Welcome {profile.username}</h1>
{/if}
