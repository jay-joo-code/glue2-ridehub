<script>
	import IconAlertCircle from '$lib/icons/glue/IconAlertCircle.svelte';
	import IconCar from '$lib/icons/glue/IconCar.svelte';
	import IconPassenger from '$lib/icons/glue/IconPassenger.svelte';
	import IconPerson from '$lib/icons/glue/IconPerson.svelte';
	import IconRightArrowLong from '$lib/icons/glue/IconRightArrowLong.svelte';
	import IconSeat from '$lib/icons/glue/IconSeat.svelte';
	import dynamicAgo from '$lib/util/glue/dynamicAgo';
	import { format } from 'date-fns';

	export let ride;
</script>

<div class="space-y-4 border-b border-base-300 px-2 py-6">
	<div class="space-y-1">
		{#if ride?.variant === 'driver'}
			<div class="badge-warning badge gap-2">
				<IconCar />
				Driver
			</div>
		{:else if ride?.variant === 'rider'}
			<div class="badge-info badge gap-2">
				<IconPassenger />
				Ride request
			</div>
		{/if}
		<div class="flex items-center space-x-2">
			<p class="text-2xl font-bold">{ride?.origin}</p>
			<div class="mt-1 text-3xl">
				<IconRightArrowLong />
			</div>
			<p class="text-2xl font-bold">{ride?.destination}</p>
		</div>
		<p>
			<span class="font-semibold">{format(new Date(ride?.date), 'iii, MMM eo')}</span>
			{ride?.isFlexible && ' (flexible)'}
		</p>
	</div>
	<div class="space-y-1">
		<div class="flex items-center space-x-2">
			<div class="text-xl">
				{#if ride?.variant === 'driver'}
					<IconSeat />
				{:else}
					<IconPerson />
				{/if}
			</div>
			<p>
				{ride?.seatCount}
				{ride?.variant === 'driver' ? 'open seats' : 'passengers'}
			</p>
		</div>
		{#if ride?.isSplitGas}
			<div class="flex items-center space-x-2">
				<div class="text-xl">
					<IconAlertCircle />
				</div>
				<p>
					{ride?.variant === 'driver' ? 'Passengers must split gas' : 'Willing to split gas'}
				</p>
			</div>
		{/if}
	</div>
	<div class="space-y-2">
		<p class="text-sm text-base-content">
			{ride?.desc}
		</p>
		<p class="text-sm text-base-content/70">
			{dynamicAgo({
				date: ride?.updated
			})}
		</p>
	</div>
</div>
