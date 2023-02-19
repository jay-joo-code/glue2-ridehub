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
	let isShowAvailableOnly = false;
	let isShowDrivers = true;
	let isShowRiders = true;

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

	$: debouncedUpdateQuery({
		key: 'date',
		value: dateFilter.toISOString()
	});
</script>

<div class="prose">
	<h3 class="mb-1">Filters</h3>
	<TextInput label="Leaving from" bind:value={originFilter} />
	<TextInput label="Arriving at" bind:value={destinationFilter} />
	<DatePicker label="Departing at" bind:value={dateFilter} />
	<div class="mt-2">
		<Checkbox
			label="Only show available rides"
			bind:checked={isShowAvailableOnly}
			on:change={(event) => {
				updateQuery({
					key: 'is-show-available-only',
					value: String(event?.target?.checked),
					$page
				});
			}}
		/>
		<Checkbox
			label="Show drivers"
			bind:checked={isShowDrivers}
			on:change={(event) => {
				updateQuery({
					key: 'is-show-drivers',
					value: String(event?.target?.checked),
					$page
				});
			}}
		/>
		<Checkbox
			label="Show riders"
			bind:checked={isShowRiders}
			on:change={(event) => {
				updateQuery({
					key: 'is-show-riders',
					value: String(event?.target?.checked),
					$page
				});
			}}
		/>
	</div>
</div>
