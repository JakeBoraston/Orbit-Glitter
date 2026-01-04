<script lang="ts">
	let height = $state();
	let width = $state();
</script>

<main bind:clientHeight={height} bind:clientWidth={width}>

</main>

<style>
	main{
		height: 85%;
		width: 85%;
		background-color: #010101;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		border-radius: 4px;
		box-shadow: hsl(240, 30%, 10%) 0 0 50px;
	}
</style>
