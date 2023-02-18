<script lang="ts">
	import { page } from '$app/stores';
	import { pb } from '$lib/glue/pocketbase';
	import { sub } from 'date-fns';
	import RideItem from './RideItem.svelte';

	let rides: any[] = [];

	$: {
		(async () => {
			// filter: variant
			const isShowDrivers = $page.url?.searchParams?.get('is-show-drivers') === 'true';
			const isShowRiders = $page.url?.searchParams?.get('is-show-riders') === 'true';
			let variantFilter = '';
			if (isShowDrivers && !isShowRiders) {
				variantFilter = 'variant="driver"';
			} else if (!isShowDrivers && isShowRiders) {
				variantFilter = 'variant="rider"';
			}

			// filter: location
			const origin = $page.url?.searchParams?.get('origin');
			const originFilter = origin ? `origin~"${origin?.toLowerCase()}"` : '';
			const destination = $page.url?.searchParams?.get('destination');
			const destinationFilter = destination ? `destination~"${destination?.toLowerCase()}"` : '';

			// filter: date (departure date)
			const date = $page.url?.searchParams?.get('date');
			const dateFilter = date ? `date>"${sub(new Date(date), { days: 2 })}"` : '';

			const filters = [variantFilter, originFilter, destinationFilter, dateFilter];

			rides = await pb.collection('rides').getFullList(200, {
				filter: filters?.filter((filter) => filter)?.join('&&'),
				expand: 'user',
				sort: '-updated'
			});
		})();
	}
</script>

{#each rides as ride (ride?.id)}
	<RideItem {ride} />
{/each}
