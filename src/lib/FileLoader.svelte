<script>
	import {loadedFile} from "../App.svelte";

</script>

<section class="panel">
	<label for="avatar">Upload a DAE file:</label>
	<input
		accept=".dae"
		bind:files={$loadedFile}
		id="collada-selector"
		name="collada"
		type="file"
	/>
</section>

<style>
	section {
		grid-row: 2;
		grid-column: 1;
	
		/* display: flex;
		flex-direction: column;
		/* align-items: center; 
		justify-content: center; */
	}

	label {
		width: 100%;
		text-align: center;
		display: block;
		font-weight: bold;
	}
</style>