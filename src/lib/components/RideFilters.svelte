<script>
	import { page } from '$app/stores';
	import updateQuery from '$lib/util/glue/updateQuery';
	import { sub } from 'date-fns';
	import debounce from 'just-debounce-it';
	import Checkbox from './glue/Checkbox.svelte';
	import DatePicker from './glue/DatePicker.svelte';
	import TextInput from './glue/TextInput.svelte';

	let originFilter = '';
	let destinationFilter = '';
	let dateFilter = sub(new Date(), { days: 5 });
	let isShowRiders = true;
	let isShowDrivers = true;

	const debouncedUpdateQuery = debounce(({ key, value }) => {
		updateQuery({
			key,
			value,
			$page
		});
	}, 500);

	$: debouncedUpdateQuery({
		key: 'origin',
		value: originFilter
	});

	$: debouncedUpdateQuery({
		key: 'destination',
		value: destinationFilter
	});

	$: updateQuery({
		key: 'date',
		value: dateFilter.toISOString(),
		$page
	});

	$: updateQuery({
		key: 'is-show-drivers',
		value: String(isShowDrivers),
		$page
	});

	$: updateQuery({
		key: 'is-show-riders',
		value: String(isShowRiders),
		$page
	});
</script>

<div class="prose">
	<h3 class="mb-1">Filters</h3>
	<TextInput label="Leaving from" bind:value={originFilter} />
	<TextInput label="Arriving at" bind:value={destinationFilter} />
	<DatePicker label="Departing at" bind:value={dateFilter} />
	<div class="mt-2">
		<Checkbox label="Show drivers" bind:checked={isShowDrivers} />
		<Checkbox label="Show riders" bind:checked={isShowRiders} />
	</div>
</div>
