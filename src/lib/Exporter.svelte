<script>
	import {loadedFile, fileContents, semantics, foundIssues} from "../App.svelte";

	const serializer = new XMLSerializer();

	let selectedSemantic;

	semantics.subscribe(value => {
		if (value.length == 0)
		{
			selectedSemantic = null;
			return;
		}

		if (selectedSemantic == null)
		{
			if (value.includes("TEXCOORD"))
			{
				selectedSemantic = "TEXCOORD";
			}
		}
	});

	function fixModel()
	{
		for (let texture of $fileContents.getElementsByTagName("texture"))
		{
			if (texture.getAttribute("texcoord") == null)
			{
				texture.setAttribute("texcoord", selectedSemantic);
			}
		}

		// Tell svelte to update it on the Analyzer
		$fileContents = $fileContents;
	}

	function exportModel()
	{
		let exportedModel = serializer.serializeToString($fileContents);
		let blob = new Blob([exportedModel], { type: "text/plain;charset=utf-8" });
		saveFile(blob);
	}

	function saveFile(blob)
	{
		const link = document.createElement("a");
		link.href = URL.createObjectURL(blob);
		link.download = `${$loadedFile[0].name}`;
		link.click();
		URL.revokeObjectURL(link.href);
	}
</script>

<section class="panel">
	<div>
		<label for="semantic-selector">UV Map Semantic:</label>
		<select bind:value={selectedSemantic} name="semantic-selector" id="semantic-selector">
			{#each $semantics as semantic}
				<option value="{semantic}">{semantic}</option>
			{/each}
		</select> 
	</div>
	<aside>
		<button on:click={fixModel} disabled={($foundIssues == 0) || ($fileContents == null) || ($semantics.length == 0) || (selectedSemantic == "")}>Fix Model</button>
		<button on:click={exportModel} disabled={($foundIssues > 0) || ($fileContents == null)}>Export</button>
	</aside>
</section>

<style>
	section {
		grid-row: 3;
		grid-column: 1;
		display: flex;
		flex-direction: column;
		align-items: space-between;
		justify-content: space-between;
	}

	button {
		width: 45%;
	}

	section aside {
		display: flex;
		flex-direction: row;
		justify-content: space-around;
		align-items: center;
	}

	label {
		width: 100%;
		text-align: center;
		display: block;
	}

	select {
		width: 100%;
	}
</style>