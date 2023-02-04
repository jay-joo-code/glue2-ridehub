<script lang="ts">
	import Aside from '$lib/components/glue/Aside.svelte';
	import Main from '$lib/components/glue/Main.svelte';
	import PageContainer from '$lib/components/glue/PageContainer.svelte';
	import FeedRidesList from '$lib/components/FeedRidesList.svelte';
</script>

<PageContainer title="Home" layout="aside-main">
	<Main>
		<FeedRidesList />
	</Main>
	<Aside>aside</Aside>
</PageContainer>
