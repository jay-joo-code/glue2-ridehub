<script>
	import { goto } from '$app/navigation';
	import { currentUser, pb } from '$lib/glue/pocketbase';
	import IconAlertCircle from '$lib/icons/glue/IconAlertCircle.svelte';
	import IconCar from '$lib/icons/glue/IconCar.svelte';
	import IconCheckOutlined from '$lib/icons/glue/IconCheckOutlined.svelte';
	import IconDelete from '$lib/icons/glue/IconDelete.svelte';
	import IconEditPen from '$lib/icons/glue/IconEditPen.svelte';
	import IconMessage from '$lib/icons/glue/IconMessage.svelte';
	import IconPassenger from '$lib/icons/glue/IconPassenger.svelte';
	import IconRefresh from '$lib/icons/glue/IconRefresh.svelte';
	import IconRightArrowLong from '$lib/icons/glue/IconRightArrowLong.svelte';
	import dynamicAgo from '$lib/util/glue/dynamicAgo';
	import { format } from 'date-fns';
	import ComingSoonButton from './glue/ComingSoonButton.svelte';
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

	const handleToggleIsUnavailable = async () => {
		pb.collection('rides').update(ride?.id, {
			isUnavailable: !ride?.isUnavailable
		});
		ride = {
			...ride,
			isUnavailable: !ride?.isUnavailable
		};
	};
</script>

<div class="space-y-4 border-b border-base-300 px-2 py-6">
	<div class="flex justify-between">
		<div>
			<div class="flex items-center space-x-2">
				<!-- isUnavailable badge -->
				{#if ride?.isUnavailable}
					<div class="badge badge-error gap-2">
						<IconAlertCircle />
						Found riders
					</div>
				{/if}

				<!-- ride variant badge -->
				<div class={`${ride?.isUnavailable && 'opacity-50'}`}>
					{#if ride?.variant === 'driver'}
						<div class="badge badge-warning gap-2">
							<IconCar />
							Driver
						</div>
					{:else if ride?.variant === 'rider'}
						<div class="badge badge-info gap-2">
							<IconPassenger />
							Ride request
						</div>
					{/if}
				</div>
			</div>

			<!-- origin, destination -->
			<div class={`${ride?.isUnavailable && 'opacity-50'}`}>
				<p class="mb-1 break-all text-2xl font-bold uppercase">
					{ride?.origin}
					<span class="mt-1 inline text-3xl [&_svg]:inline">
						<IconRightArrowLong />
					</span>
					{ride?.destination}
				</p>
			</div>

			<!-- departure date -->
			<div class={`${ride?.isUnavailable && 'opacity-50'}`}>
				<p class="mb-4">
					<span class="font-semibold">{format(new Date(ride?.date), 'MMM eo, iii')}</span>
					{ride?.isFlexible && ' (flexible)'}
				</p>
			</div>

			<!-- avatar -->
			<div class={`${ride?.isUnavailable && 'opacity-50'}`}>
				<div class="flex items-center space-x-3">
					<div class="avatar">
						<div class="w-7 rounded-full">
							<img src={ride?.expand?.user?.avatarUrl} />
						</div>
					</div>
					<div>
						<div class="flex items-center space-x-1">
							<p class="text-sm leading-5">{ride?.expand?.user?.name}</p>
							<span class="text-xs text-green-300">
								<IconCheckOutlined />
							</span>
						</div>
						<p class="text-xs text-base-content/70">Cornell email verified</p>
					</div>
				</div>
			</div>
		</div>

		<!-- action buttions: message, edit, mark unavailable -->
		<div>
			{#if ride?.user === $currentUser?.id}
				<div class="space-y-3">
					<div>
						<ComingSoonButton
							variant="click-edit-ride"
							context={{
								ride,
								user: $currentUser
							}}
							class="btn-primary btn-circle btn text-xl"
							infoParagraphs={[
								'For now, to make changes to a ride, mark it as unavailable (with the red trash bin button) and create a new ride.',
								"Sorry about the inconvenience. We'll get this feature out ASAP"
							]}
						>
							<IconEditPen />
						</ComingSoonButton>
					</div>
					<div>
						{#if ride?.isUnavailable}
							<div
								class="tooltip tooltip-bottom before:right-auto before:left-[-0.5rem] before:content-[attr(data-tip)]"
								data-tip="Mark available"
							>
								<button
									class="btn-success btn-circle btn text-xl"
									on:click={handleToggleIsUnavailable}
								>
									<IconRefresh />
								</button>
							</div>
						{:else}
							<div
								class="tooltip tooltip-bottom before:right-auto before:left-[-0.5rem] before:content-[attr(data-tip)]"
								data-tip="Mark unavailable"
							>
								<button
									class="btn-error btn-circle btn text-xl"
									on:click={handleToggleIsUnavailable}
								>
									<IconDelete />
								</button>
							</div>
						{/if}
					</div>
				</div>
			{:else}
				<div class={`${ride?.isUnavailable && 'opacity-50'}`}>
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
			{/if}
		</div>
	</div>

	<div class={`${ride?.isUnavailable && 'opacity-50'}`}>
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
