<script lang="ts">
	import CodeMirror from 'svelte-codemirror-editor';
	import { javascript } from '@codemirror/lang-javascript';
	import * as acorn from 'acorn';

	var code = 'const a=1;\nlet b=a+1;\nb++';
	var output: { [key: string]: string | number } | string = {};

	function run(code: string) {
		const vars = new Array<string>();
		try {
			const res = acorn.parse(code, { ecmaVersion: 2020 });
			for (const r of res.body) {
				if (r.type === 'VariableDeclaration') {
					if (r.declarations[0].id.type === 'Identifier') {
						vars.push(r.declarations[0].id.name);
					}
				}
			}

			const fn = new Function(code + ';\nreturn {' + vars.join(',') + '}');
			try {
				return fn();
			} catch (err) {
				console.log('err');
				return '';
			}
		} catch (err) {
			console.log('acorn');
		}
	}

	$: output = run(code);
</script>

<h1>Offline Javascript Evaluation</h1>
<div class="flex-container">
	<div class="flex-child">
		<CodeMirror
			bind:value={code}
			lang={javascript()}
			styles={{
				'&': {
					width: '100%',
					xheight: '50rem',
					border: '1px solid black'
				},
				'.cm-gutters': {
					display: 'none'
				}
			}}
		/>
	</div>

	<div class="flex-child" style="padding: 5px 10px;">
		{#if typeof output === 'object'}
			{#each Object.keys(output) as key}
				<div>
					<b>{key}</b> = {output[key]}
				</div>
			{/each}
		{:else}
			{output}
		{/if}
	</div>
</div>

<style>
	.flex-container {
		display: flex;
	}

	.flex-child {
		flex: 0 0 auto;
	}

	.flex-child:first-child {
		width: 65%;
	}
</style>
