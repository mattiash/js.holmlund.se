<script lang="ts">
	import CodeMirror from 'svelte-codemirror-editor';
	import { javascript } from '@codemirror/lang-javascript';

	var code = 'test';
	var output = '';

	function run() {
		const fn = new Function(code);
		output = fn();
	}
</script>

<h1>Welcome to SvelteKit</h1>
<p>Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p>

<div>
	<button on:click={run}>Run</button>
</div>

<CodeMirror
	bind:value={code}
	lang={javascript()}
	styles={{
		'&': {
			width: '50%',
			xheight: '50rem',
			border: '1px solid black'
		},
		'.cm-gutters': {
			display: 'none'
		}
	}}
/>

{output}
