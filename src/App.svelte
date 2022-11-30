<script context="module">
	import { writable } from 'svelte/store';

	export let loadedFile = writable();
	export let fileContents = writable();
	export let semantics = writable([]);
	export let foundIssues = writable(0);
</script>

<script>
	import FileLoader from "./lib/FileLoader.svelte"
	import Analyzer from "./lib/Analyzer.svelte"
	import Exporter from "./lib/Exporter.svelte"

	import ReynLogo from "../public/Reyn_Fix_Official_Logo.png"
</script>

<main>
	<header>
		<img src={ReynLogo}/>
		<h1>Reyn Fix</h1>
	</header>
	<FileLoader/>
	<Analyzer/>
	<Exporter/>
</main>

<style>
	main {
		display: grid;
		grid-template-columns: 1fr 3fr;
		grid-template-rows: 4em repeat(3, 1fr);
		grid-column-gap: 0px;
		grid-row-gap: 0px;

		height: 100vh;
	}

	header {
		grid-column: 1 / end;
		grid-row: 1;
		background: lime;
		position: relative;

		/* Add a drop shadow to the header bar */
		box-shadow: 0 0 10px 0 lightgreen;
	}

	header img {
		height: 80%;
		margin-left: 10px;
		display: inline-block;
		position: absolute;
		top: 50%;
  		transform: translate(0, -50%);
		/* border-radius: 50%; */
	}

	h1 {
		padding: 0;
		margin: 0 90px;
		display: inline-block;
		position: absolute;
		top: 50%;
  		transform: translate(0, -50%);
	}
</style>
