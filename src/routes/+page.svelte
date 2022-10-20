<script>
	import { marked } from 'marked';

	let textInput =
		'Write some text here (or upload a text file) and click the Convert button to make it more readable! Use the Export button to download the result as a markdown file.';

	let boldText = '';

	let converted = false;

	let files = [];

	function handleUpload() {
		if (files) {
			const fileText = files[0].text();
			fileText.then((filetext) => {
				textInput = filetext;
			});
			files = [];
		}
	}

	function download(filename, text) {
		var element = document.createElement('a');
		element.setAttribute('href', 'data:text/plain;charset=utf-8,' + encodeURIComponent(text));
		element.setAttribute('download', filename);

		element.style.display = 'none';
		document.body.appendChild(element);

		element.click();

		document.body.removeChild(element);
	}

	function handleExport() {
		if (boldText != '') {
			download('export.md', boldText);
		}
	}

	function handleConvert() {
		if (textInput.length == 0) return;

		boldText = '';

		let wordBeg = null,
			wordEnd = null;
		for (var i = 0; i < textInput.length; i++) {
			if (textInput[i].toLowerCase() != textInput[i].toUpperCase() && wordBeg == null) {
				wordBeg = i;
			} else if (
				!(textInput[i].toLowerCase() != textInput[i].toUpperCase()) ||
				i == textInput.length - 1
			) {
				if (wordBeg !== null) {
					wordEnd = i - 1;
					if (wordBeg == wordEnd) {
						boldText += '**' + textInput[wordEnd] + '**';
					} else {
						let word = textInput.substring(wordBeg, wordEnd + 1);
						let half1 = word.substring(0, word.length / 2);
						let half2 = word.substring(word.length / 2);
						boldText += '**' + half1 + '**' + half2;
					}
				}
				wordBeg = null;
				wordEnd = null;
				boldText += textInput[i];
			}
		}
		converted = true;
	}
</script>

<title>Half Bold</title>

<svelte:head>
	<link
		href="https://cdn.jsdelivr.net/npm/bootstrap@5.2.0-beta1/dist/css/bootstrap.min.css"
		rel="stylesheet"
		integrity="sha384-0evHe/X+R7YkIZDRvuzKMRqM+OrBnVFBL6DOitfPri4tjfHxaWutUpFmBp4vmVor"
		crossorigin="anonymous"
	/>
	<style>
		html,
		body {
			width: 100%;
			height: 100%;
			margin: 0;
			overflow: hidden;
		}
	</style>
</svelte:head>

<div class="container mt-1">
	<div class="textArea">
		<div class="h4">Write Here</div>
		<textarea style="resize:none; height: 100%;" class="form-control" bind:value={textInput} />
		<div class="mt-3 row g-2 g-lg-3">
			<input class="col form-control" type="file" bind:files accept=".txt,.md" />
			<button class="col col-lg-2 btn btn-primary" on:click={handleUpload}>Upload</button>
		</div>
	</div>
	<div class="buttons">
		<button class="btn btn-primary" on:click={handleConvert}>Convert</button>
		<button class="btn  {converted ? 'btn-success' : 'btn-secondary'}" on:click={handleExport}
			>Export</button
		>
	</div>
	<div class="display">
		<div class="h4">Result</div>
		<div class="border rounded p-2" style="overflow:scroll; max-height:100%;">
			<div>
				{@html marked(boldText)}
			</div>
		</div>
	</div>
</div>

<footer class="page-footer font-small blue fixed-bottom">
	<div class="footer-copyright text-center py-3">
		Created by:
		<a target="_blank" href="https://twitter.com/bradyjhenry"> Brady Henry</a>
	</div>
</footer>

<style>
	.container {
		max-width: 100%;
		height: 70%;
		display: flex;
		flex-direction: row;
		align-items: center;
	}
	.buttons {
		display: flex;
		height: 30%;
		flex-direction: column;
		justify-content: space-evenly;
		align-items: center;
	}
	.textArea {
		margin: 5%;
		max-width: 45%;
		height: 100%;
		width: 100%;
	}
	.display {
		margin: 5%;
		max-width: 45%;
		height: 100%;
		width: 100%;
		word-wrap: break-word;
	}
</style>
