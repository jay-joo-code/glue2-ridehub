<script lang="ts">
	import Aside from '$lib/components/glue/Aside.svelte';
	import Main from '$lib/components/glue/Main.svelte';
	import PageContainer from '$lib/components/glue/PageContainer.svelte';
	import FeedRidesList from '$lib/components/FeedRidesList.svelte';
	import RideFilters from '$lib/components/RideFilters.svelte';
	import IconFilterList from '$lib/icons/glue/IconFilterList.svelte';
</script>

<PageContainer title="Home" layout="aside-main">
	<Aside>
		<div class="hidden md:block">
			<RideFilters />
		</div>
		<div class="block md:hidden">
			<!-- modal trigger -->
			<label for="modal-ride-filters" class="btn-primary btn-sm btn gap-2"
				><span class="text-xl"><IconFilterList /></span> Filters</label
			>

			<!-- modal content -->
			<input type="checkbox" id="modal-ride-filters" class="modal-toggle" />
			<label for="modal-ride-filters" class="modal cursor-pointer">
				<label class="modal-box relative" for="">
					<RideFilters />
					<label for="modal-ride-filters" class="btn-primary btn-block btn mt-4">Done</label>
				</label>
			</label>
		</div>
	</Aside>
	<Main>
		<FeedRidesList />
	</Main>
</PageContainer>
