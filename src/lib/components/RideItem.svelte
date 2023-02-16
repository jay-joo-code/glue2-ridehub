<script>
	import { goto } from '$app/navigation';
	import { currentUser, pb } from '$lib/glue/pocketbase';
	import IconAlertCircle from '$lib/icons/glue/IconAlertCircle.svelte';
	import IconCar from '$lib/icons/glue/IconCar.svelte';
	import IconMessage from '$lib/icons/glue/IconMessage.svelte';
	import IconPassenger from '$lib/icons/glue/IconPassenger.svelte';
	import IconPerson from '$lib/icons/glue/IconPerson.svelte';
	import IconRightArrowLong from '$lib/icons/glue/IconRightArrowLong.svelte';
	import IconSeat from '$lib/icons/glue/IconSeat.svelte';
	import dynamicAgo from '$lib/util/glue/dynamicAgo';
	import { format } from 'date-fns';
	import RequireAuthButton from './glue/RequireAuthButton.svelte';

	export let ride;

	let isChatLoading = false;

	const handleChatClick = async () => {
		isChatLoading = true;

		try {
			const existingChatroom = await pb
				.collection('chatrooms')
				.getFirstListItem(
					`post="${ride?.id}"&&author="${ride?.user}"&&searcher="${$currentUser?.id}"`
				);

			goto(`/chatrooms/${existingChatroom?.id}`);
		} catch (error) {
			if (error?.status === 404) {
				const newChatroom = await pb.collection('chatrooms').create({
					post: ride?.id,
					author: ride?.user,
					searcher: $currentUser?.id
				});
				goto(`/chatrooms/${newChatroom?.id}`);
			}
		}

		isChatLoading = false;
	};
</script>

<div class="space-y-4 border-b border-base-300 px-2 py-6">
	<div class="space-y-1">
		<div class="flex justify-between">
			<div class="space-y-1">
				<!-- ride variant badge -->
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

				<!-- origin, destination -->
				<div class="flex items-center space-x-2">
					<p class="text-2xl font-bold">{ride?.origin}</p>
					<div class="mt-1 text-3xl">
						<IconRightArrowLong />
					</div>
					<p class="text-2xl font-bold">{ride?.destination}</p>
				</div>
			</div>

			<!-- message button -->
			<div
				class="tooltip tooltip-bottom before:right-auto before:left-[-0.5rem] before:content-[attr(data-tip)]"
				data-tip="Send a message"
			>
				<RequireAuthButton
					class={`btn-primary btn-circle btn text-2xl ${isChatLoading && 'loading'}`}
					on:click={handleChatClick}
				>
					{#if !isChatLoading}
						<IconMessage />
					{/if}
				</RequireAuthButton>
			</div>
		</div>
		<p>
			<span class="font-semibold">{format(new Date(ride?.date), 'MMM eo, iii')}</span>
			{ride?.isFlexible && ' (flexible)'}
		</p>
	</div>

	<!-- avatar -->
	<div class="flex items-center space-x-3">
		<div class="avatar">
			<div class="w-6 rounded-full">
				<img src={ride?.expand?.user?.avatarUrl} />
			</div>
		</div>
		<div>
			<p class="text-md font-medium">{ride?.expand?.user?.name}</p>
		</div>
	</div>

	<div class="space-y-1">
		<!-- seats, isSplitGas -->
		<p class="text-sm text-base-content">
			{ride?.seatCount}
			{ride?.variant === 'driver' ? 'open seats' : 'passengers'} • {ride?.variant === 'driver'
				? 'Passengers must split gas'
				: 'Willing to split gas'}
		</p>

		<!-- desc -->
		<p class="text-sm text-base-content">
			{ride?.desc}
		</p>
	</div>

	<!-- updated timestamp -->
	<div class="space-y-2">
		<p class="text-sm text-base-content/70">
			{dynamicAgo({
				date: ride?.updated
			})}
		</p>
	</div>
</div>
