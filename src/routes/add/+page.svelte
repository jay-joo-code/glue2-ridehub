<script lang="ts">
	import { goto } from '$app/navigation';
	import Checkbox from '$lib/components/glue/Checkbox.svelte';
	import DatePicker from '$lib/components/glue/DatePicker.svelte';
	import PageContainer from '$lib/components/glue/PageContainer.svelte';
	import Textarea from '$lib/components/glue/Textarea.svelte';
	import TextInput from '$lib/components/glue/TextInput.svelte';
	import { currentUser, pb } from '$lib/glue/pocketbase';
	import IconCar from '$lib/icons/glue/IconCar.svelte';
	import IconLock from '$lib/icons/glue/IconLock.svelte';
	import IconPassenger from '$lib/icons/glue/IconPassenger.svelte';

	let variant = '';
	let origin = '';
	let destination = '';
	let date = new Date();
	let isFlexible = true;
	let isSplitGas = true;
	let desc = '';
	let seatCount = 1;
	let errors;

	$: console.log('errors?.length', errors, Object.keys(errors || {})?.length);

	$: {
		if (variant === 'driver') isSplitGas = false;
		else isSplitGas = true;
	}

	let isSaving = false;

	const handleSave = async () => {
		isSaving = true;
		try {
			await pb.collection('rides').create({
				variant,
				origin: origin?.toLowerCase(),
				destination: destination?.toLowerCase(),
				date,
				isFlexible,
				isSplitGas,
				desc,
				user: $currentUser?.id,
				seatCount
			});
			goto('/profile/my-rides');
		} catch (error) {
			errors = error?.data?.data;
		}
		isSaving = false;
	};
</script>

<PageContainer title="Add a new ride">
	<div class="prose space-y-4">
		<h2>Add a new ride</h2>
		<div>
			<h3>I am a ...</h3>
			<div class="not-prose flex space-x-4 [&>div]:flex-1 ">
				<div class="max-w-[14rem] space-y-2">
					<button
						class={`btn-outline btn-block btn-lg btn gap-2 ${variant === 'driver' && 'btn-active'}`}
						on:click={() => {
							variant = 'driver';
						}}
					>
						<div class="text-2xl">
							<IconCar />
						</div>
						driver
					</button>
					<p class="mb-0 text-left text-sm leading-5">Looking for passengers in my car.</p>
				</div>
				<div class="max-w-[14rem] space-y-2">
					<button
						class={`btn-outline btn-block btn-lg btn flex gap-2 ${
							variant === 'rider' && 'btn-active'
						}`}
						on:click={() => {
							variant = 'rider';
						}}
					>
						<div class="text-2xl">
							<IconPassenger />
						</div>
						rider
					</button>
					<p class="mb-0 text-left text-sm leading-5">
						Looking for a driver or someone to split an Uber.
					</p>
				</div>
			</div>
			{#if errors?.variant}
				<p class="text-sm text-error">{errors?.variant?.message}</p>
			{/if}
		</div>

		<div class="alert shadow-sm">
			<div class="items-start">
				<div class="mt-1.5 flex-shrink-0">
					<IconLock />
				</div>
				<span>Only users signed in with an @cornell.edu email will be able to message you. </span>
			</div>
		</div>
	</div>

	<div class="divider" />

	<div class="space-y-2 py-6">
		<TextInput label="Departing from" bind:value={origin} error={errors?.origin?.message} />
		<TextInput label="Arriving at" bind:value={destination} error={errors?.destination?.message} />

		<DatePicker label="Departing on" bind:value={date} />
		<Checkbox label="I'm flexible with the depature date" bind:checked={isFlexible} />
	</div>
	<div class="divider" />
	<div class="space-y-2 py-6">
		<TextInput
			type="number"
			bind:value={seatCount}
			label={variant === 'driver' ? 'Seats available' : 'Number of passengers'}
			error={errors?.seatCount?.message}
		/>
		<Textarea bind:value={desc} label="Trip details" />
		<Checkbox
			label={variant === 'driver' ? 'The passenger must split gas' : "I'm willing to split gas"}
			bind:checked={isSplitGas}
		/>
	</div>
	<button class={`btn-primary btn ${isSaving && 'loading'}`} on:click={handleSave}>Add ride</button>
	{#if errors && Object.keys(errors)?.length > 0}
		<p class="mt-2 text-sm text-error">There were errors with your input</p>
	{/if}
</PageContainer>
