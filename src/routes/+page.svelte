<script lang="ts">
	import CodeMirror from 'svelte-codemirror-editor';
	import { javascript } from '@codemirror/lang-javascript';
	import * as acorn from 'acorn';

	var code = 'const a=1;\nlet b=a+1;\nb++';
	var output: { [key: string]: string | number } | string = {};
	let error = '';

	function run(code: string) {
		const vars = new Array<string>();
		try {
			error = '';
			const res = acorn.parse(code, { ecmaVersion: 2020, allowReturnOutsideFunction: true });
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
				console.log('err', err);
				if (err instanceof Error) {
					error = err.message;
				}
				return '';
			}
		} catch (err) {
			if (err instanceof Error) {
				error = err.message;
			}
			console.log('acorn', err);
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
		{#if error}
			<div style="background-color: pink; padding: 5px;">
				{error}
			</div>
		{:else if typeof output === 'object'}
			<!-- prettier-ignore -->
			<pre style="margin-top:0;">
{#each Object.keys(output) as key}<b>{key}</b> = {JSON.stringify(output[key], undefined, 2)}
{/each}&nbsp;
			</pre>
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
