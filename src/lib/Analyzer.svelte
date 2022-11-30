<script>

	import {loadedFile, fileContents, semantics, foundIssues} from "../App.svelte";

	const parser = new DOMParser();

	let brokenTextureCode = []; // Contains an array of broken texture code to show to the user

	// Current state of the analyzer
	let status = "Waiting";

	let fileName = "";

	async function loadFile(file)
	{
		status = "Parsing...";
		fileName = file.name;

		let xmlString = await file.text();

		$fileContents = parser.parseFromString(xmlString, "text/xml");
	}

	async function analyzeFile(contents)
	{
		// Find broken textures
		status = "Finding Broken Textures..."

		brokenTextureCode = [];
		for (let texture of contents.getElementsByTagName("texture"))
		{
			if (texture.getAttribute("texcoord") == undefined)
			{
				brokenTextureCode.push(texture.outerHTML);
			}
		}
		$foundIssues = brokenTextureCode.length;

		// Find semantics
		status = "Finding Semantics...";

		let foundSemantics = [];
		for (let input of contents.getElementsByTagName("input"))
		{
			let semantic = input.getAttribute("semantic");
			if (!foundSemantics.includes(semantic))
			{
				foundSemantics.push(semantic);
			}
		}
		$semantics = foundSemantics;

		status = "Completed";
	}

	loadedFile.subscribe(value => {
		if (value != null)
		{
			loadFile(value[0]);
		}

		else
		{
			status = "Waiting";
		}
	});

	fileContents.subscribe(value => {
		if (value != null)
		{
			analyzeFile(value);
		}
	});

</script>

<article class="panel">
	<header>
		{#if status=="Waiting"}
			<h1>Please Select a File</h1>
		{:else if status=="Completed"}
			<h1>{fileName}</h1>
		{:else}
			<h1>{status}</h1>
		{/if}
	</header>

	{#if status=="Completed"}
		<section>
			<figure>
				<figcaption>{brokenTextureCode.length} Broken Textures</figcaption>
				<output>
					{#each brokenTextureCode as brokenTex}
						<span>{brokenTex}</span>
					{/each}
				</output>
			</figure>

			<figure>
				<figcaption>{$semantics.length} Semantics</figcaption>
				<output>
					{#each $semantics as semantic}
						<span>{semantic}</span>
					{/each}
				</output>
			</figure>
		</section>
	{/if}
</article>

<style>

	article {
		grid-row: 2 / 5;
		grid-column: 2;
		max-width: 100%;
  		overflow: hidden;
	}

	h1 {
		width: 100%;
		text-align: center;
	}

	figure {
		display: flex;
		flex-direction: column;
		align-items: stretch;
		justify-content: center;
		width: 100%;
		margin: 5px;
		padding: 0;
		overflow: hidden;
	}

	figcaption {
		width: 100%;
		text-align: center;
		font-size: 1em;
		flex-grow: 0;
		margin: 5px;
	}

	section {
		width: 100%;
		display: flex;
		flex-direction: row;
		justify-content: space-around;
		align-items: stretch;
	}

	output {
		background: lightgray;
		font-family: Consolas, Courier New, monospace;
		display: block;
		padding: 1em;
		flex-grow: 1;

		border: 2px solid darkgray;

		overflow: auto;
	}

	output span {
		display: block;
		white-space: nowrap;
	}

</style>