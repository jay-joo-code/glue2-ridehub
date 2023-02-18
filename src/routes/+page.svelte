<script lang="ts">
	import Aside from '$lib/components/glue/Aside.svelte';
	import Main from '$lib/components/glue/Main.svelte';
	import PageContainer from '$lib/components/glue/PageContainer.svelte';
	import FeedRidesList from '$lib/components/FeedRidesList.svelte';
	import RideFilters from '$lib/components/RideFilters.svelte';
</script>

<PageContainer title="Home" layout="aside-main">
	<Aside>
		<RideFilters />
	</Aside>
	<Main>
		<FeedRidesList />
	</Main>
</PageContainer>
