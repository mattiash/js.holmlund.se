<script lang="ts">
	import CodeMirror from 'svelte-codemirror-editor';
	import { javascript } from '@codemirror/lang-javascript';
	import * as acorn from 'acorn';

	var code = 'test';
	var output: { [key: string]: string | number } | string = {};

	function run() {
		const vars = new Array<string>();
		const res = acorn.parse(code, { ecmaVersion: 2020 });
		console.log(res);
		for (const r of res.body) {
			if (r.type === 'VariableDeclaration') {
				if (r.declarations[0].id.type === 'Identifier') {
					vars.push(r.declarations[0].id.name);
				}
			}
		}

		const fn = new Function(code + ';\nreturn {' + vars.join(',') + '}');
		try {
			output = fn();
		} catch (err) {
			console.log('err');
		}
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

{#if typeof output === 'object'}
	{#each Object.keys(output) as key}
		{key}: {output[key]}
	{/each}
{:else}
	{output}
{/if}
