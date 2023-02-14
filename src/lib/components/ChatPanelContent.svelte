<script>
	import { currentUser, pb } from '$lib/glue/pocketbase';
	import IconRightArrowLong from '$lib/icons/glue/IconRightArrowLong.svelte';
	import getOtherUser from '$lib/util/glue/chat/getOtherUser';
	import { format } from 'date-fns';

	export let chatroom;

	$: console.log('chatroom?.expand?.post', chatroom?.expand?.post);

	let otherUser = null;

	const fetchOtherUser = async () => {
		const otherUserId = getOtherUser({
			chatroom,
			user: $currentUser
		});
		otherUser = await pb.collection('users').getOne(otherUserId);
	};

	$: {
		if (chatroom) {
			fetchOtherUser();
		}
	}
</script>

{#if chatroom}
	<div class="flex-1">
		<div class="ml-2">
			<div class="">
				<p class="text-lg font-bold">{otherUser?.name}</p>
			</div>
			<div class="flex items-center space-x-0.5 text-base-content/90">
				<p class="text-sm font-medium uppercase">{chatroom?.expand?.post?.origin}</p>
				<span class="text-xl">
					<IconRightArrowLong />
				</span>
				<p class="text-sm font-medium uppercase">{chatroom?.expand?.post?.destination}</p>
			</div>
			<div>
				<p class="text-xs text-base-content/90">
					{format(new Date(chatroom?.expand?.post?.date), 'MMM eo, iii')}
					{#if chatroom?.expand?.post?.isFlexible}
						<span> (flexible)</span>
					{/if}
				</p>
			</div>
		</div>
	</div>
{/if}
