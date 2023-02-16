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
		<div class="">
			<div class="flex items-center">
				<p class="text-md truncate font-medium">
					{otherUser?.name} •
					<span class="uppercase text-base-content/80">{chatroom?.expand?.post?.origin}</span>
					<span class="inline text-xl text-base-content/80 [&_svg]:inline">
						<IconRightArrowLong />
					</span>
					<span class="uppercase text-base-content/80">
						{chatroom?.expand?.post?.destination}
					</span>
				</p>
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
