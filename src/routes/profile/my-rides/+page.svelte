<script lang="ts">
	import PageContainer from '$lib/components/glue/PageContainer.svelte';
	import RideItem from '$lib/components/RideItem.svelte';
	import { currentUser, pb } from '$lib/glue/pocketbase';
	import LoadingRides from '$lib/components/LoadingRides.svelte';

	import { onMount } from 'svelte';

	let rides: any[] = [];

	let isLoading = false;

	onMount(async () => {
		isLoading = true;
		rides = await pb.collection('rides').getFullList(200, {
			filter: `user='${$currentUser?.id}'`,
			expand: 'user',
			sort: '-updated'
		});
		isLoading = false;
	});

	// $: console.log('rides', rides);
</script>

<PageContainer>
	<div class="prose mt-4">
		<h2>My rides</h2>
	</div>
	{#each rides as ride (ride?.id)}
		<RideItem {ride} />
	{/each}
</PageContainer>

<if isLoading>
	<LoadingRides />
</if>
