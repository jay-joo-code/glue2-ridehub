<script lang="ts">
	import { onMount } from 'svelte';
	import { currentUser, pb } from '$lib/glue/pocketbase';
	import RideItem from './RideItem.svelte';

	let rides: any[] = [];

	onMount(async () => {
		rides = await pb.collection('rides').getFullList(200, {
			expand: 'user',
			sort: '-updated'
		});
	});
</script>

{#each rides as ride (ride?.id)}
	<RideItem {ride} />
{/each}
