<script lang="ts">
	import { browser } from '$app/environment';
	import { page } from '$app/stores';
	import { pb } from '$lib/glue/pocketbase';
	import { sub } from 'date-fns';
	import RideItem from './RideItem.svelte';

	let rides: any[] = [];
	let filters = [];

	$: {
		if (browser) {
			(async () => {
				// filter: location
				const origin = $page.url?.searchParams?.get('origin');
				const originFilter = origin ? `origin~"${origin?.toLowerCase()}"` : '';
				const destination = $page.url?.searchParams?.get('destination');
				const destinationFilter = destination ? `destination~"${destination?.toLowerCase()}"` : '';

				// filter: date (departure date)
				const date = $page.url?.searchParams?.get('date');
				// subtract 2 days, to get rides in nearby dates
				const dateFilter = date ? `date>="${sub(new Date(date), { days: 2 })?.toISOString()}"` : '';

				// filter: variant
				const isShowDrivers = $page.url?.searchParams?.get('is-show-drivers') === 'true';
				const isShowRiders = $page.url?.searchParams?.get('is-show-riders') === 'true';
				let variantFilter = '';
				if (isShowDrivers && !isShowRiders) {
					variantFilter = 'variant="driver"';
				} else if (!isShowDrivers && isShowRiders) {
					variantFilter = 'variant="rider"';
				}

				// filter: available only
				const isShowAvailableOnly =
					$page.url?.searchParams?.get('is-show-available-only') === 'true';
				const showAvailableOnlyFilter = isShowAvailableOnly ? 'isUnavailable=false' : '';

				filters = [
					originFilter,
					destinationFilter,
					dateFilter,
					variantFilter,
					showAvailableOnlyFilter
				];
			})();
		}
	}

	const fetchRides = async (filters) => {
		if (browser) {
			rides = await pb.collection('rides').getFullList(200, {
				filter: filters?.filter((filter) => filter)?.join('&&'),
				expand: 'user',
				sort: '-updated'
			});
		}
	};

	$: fetchRides(filters);
</script>

<div class="prose mt-4">
	<h2>All rides ({rides?.length})</h2>
</div>
{#each rides as ride (ride?.id)}
	<RideItem {ride} />
{/each}
