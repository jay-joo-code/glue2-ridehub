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
				const isShowDrivers = $page.url?.searchParams?.get('is-show-drivers') !== 'false';
				const isShowRiders = $page.url?.searchParams?.get('is-show-riders') !== 'false';
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
	<h2>All rides</h2>
</div>

{#each rides as ride (ride?.id)}
	<RideItem {ride} />
{/each}

<div class="relative">
	<div class="relative">
		<div
			class="absolute inset-0 z-10 flex items-center justify-center bg-base-200/20 drop-shadow-2xl"
		>
			<button class="loading btn-primary btn">loading rides</button>
		</div>

		<div
			class="h-64 space-y-4 rounded-lg border border-b border-base-300 border-blue-300 p-4 px-2 py-6 shadow"
		>
			<div class="flex animate-pulse space-x-4">
				<div class="h-10 w-10 rounded-full bg-slate-200" />
				<div class="flex-1 space-y-6 py-1">
					<div class="h-2 rounded bg-slate-200" />
					<div class="space-y-3">
						<div class="grid grid-cols-3 gap-4">
							<div class="col-span-2 h-2 rounded bg-slate-200" />
							<div class="col-span-1 h-2 rounded bg-slate-200" />
						</div>
						<div class="h-2 rounded bg-slate-200" />
						<div class="h-2 rounded bg-slate-200" />
						<div class="h-2 rounded bg-slate-200" />
						<div class="h-2 rounded bg-slate-200" />
						<div class="h-2 rounded bg-slate-200" />
						<div class="grid grid-cols-3 gap-4">
							<div class="col-span-2 h-2 rounded bg-slate-200" />
							<div class="col-span-1 h-2 rounded bg-slate-200" />
						</div>
						<div class="h-2 rounded bg-slate-200" />
						<div class="h-2 rounded bg-slate-200" />
					</div>
				</div>
			</div>
		</div>
	</div>
	<div class="relative">
		<div
			class="absolute inset-0 z-10 flex items-center justify-center bg-base-200/20 drop-shadow-2xl"
		>
			<button class="loading btn-primary btn">loading rides</button>
		</div>

		<div
			class="my-2 h-64 space-y-4 rounded-lg border border-b border-base-300 border-blue-300 p-4 px-2 py-6 shadow"
		>
			<div class="flex animate-pulse space-x-4">
				<div class="h-10 w-10 rounded-full bg-slate-200" />
				<div class="flex-1 space-y-6 py-1">
					<div class="h-2 rounded bg-slate-200" />
					<div class="space-y-3">
						<div class="grid grid-cols-3 gap-4">
							<div class="col-span-2 h-2 rounded bg-slate-200" />
							<div class="col-span-1 h-2 rounded bg-slate-200" />
						</div>
						<div class="h-2 rounded bg-slate-200" />
						<div class="h-2 rounded bg-slate-200" />
						<div class="h-2 rounded bg-slate-200" />
						<div class="h-2 rounded bg-slate-200" />
						<div class="h-2 rounded bg-slate-200" />
						<div class="grid grid-cols-3 gap-4">
							<div class="col-span-2 h-2 rounded bg-slate-200" />
							<div class="col-span-1 h-2 rounded bg-slate-200" />
						</div>
						<div class="h-2 rounded bg-slate-200" />
						<div class="h-2 rounded bg-slate-200" />
					</div>
				</div>
			</div>
		</div>
	</div>

	<div class="relative">
		<div
			class="absolute inset-0 z-10 flex items-center justify-center bg-base-200/20 drop-shadow-2xl"
		>
			<button class="loading btn-primary btn">loading rides</button>
		</div>
		<div
			class="my-2 h-64 space-y-4 rounded-lg border border-b border-base-300 border-blue-300 p-4 px-2 py-6 shadow"
		>
			<div class="flex animate-pulse space-x-4">
				<div class="h-10 w-10 rounded-full bg-slate-200" />
				<div class="flex-1 space-y-6 py-1">
					<div class="h-2 rounded bg-slate-200" />
					<div class="space-y-3">
						<div class="grid grid-cols-3 gap-4">
							<div class="col-span-2 h-2 rounded bg-slate-200" />
							<div class="col-span-1 h-2 rounded bg-slate-200" />
						</div>
						<div class="h-2 rounded bg-slate-200" />
						<div class="h-2 rounded bg-slate-200" />
						<div class="h-2 rounded bg-slate-200" />
						<div class="h-2 rounded bg-slate-200" />
						<div class="h-2 rounded bg-slate-200" />
						<div class="grid grid-cols-3 gap-4">
							<div class="col-span-2 h-2 rounded bg-slate-200" />
							<div class="col-span-1 h-2 rounded bg-slate-200" />
						</div>
						<div class="h-2 rounded bg-slate-200" />
						<div class="h-2 rounded bg-slate-200" />
					</div>
				</div>
			</div>
		</div>
	</div>
</div>
