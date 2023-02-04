<script lang="ts">
	import PageContainer from '$lib/components/glue/PageContainer.svelte';
	import RideItem from '$lib/components/RideItem.svelte';
	import { currentUser, pb } from '$lib/glue/pocketbase';

	import { onMount } from 'svelte';

	let rides: any[] = [];

	onMount(async () => {
		rides = await pb.collection('rides').getFullList(200, {
			filter: `user='${$currentUser?.id}'`,
			expand: 'user',
			sort: '-updated'
		});
	});

	// $: console.log('rides', rides);
</script>

<PageContainer>
	{#each rides as ride (ride?.id)}
		<RideItem {ride} />
	{/each}
</PageContainer>
