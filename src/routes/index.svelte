<script lang="ts">
	import fs from 'fs';
	import { onMount } from 'svelte';
	import { text } from 'svelte/internal';
	const height = 32;
	const width = 32;
	export let data: { place: Array<[number, number, string]> };

	const board: string[][] = Array(height)
		.fill(0)
		.map(() => Array(width).fill('FFF'));
	function paint(x, y, colorHex) {
		board[y][x] = colorHex;
	}
	function paintCache() {
		data.place.forEach(([x, y, colorHex]) => paint(x, y, colorHex));
	}

	function checkArr(arr, arrtocheck) {
		var array = arrtocheck,
			prizes = arr,
			includes = prizes.some((a) => array.every((v, i) => v === a[i]));

		return includes;
	}

	function downloadFile(filename, text) {
		var element = document.createElement('a');
		element.setAttribute('href', 'data:text/plain;charset=utf-8,' + encodeURIComponent(text));
		element.setAttribute('download', filename);

		element.style.display = 'none';
		document.body.appendChild(element);

		element.click();

		document.body.removeChild(element);
	}

	function downloadJson(text, name) {
		const a = document.createElement('a');
		const type = name.split('.').pop();
		a.href = URL.createObjectURL(
			new Blob([text], { type: `text/${type === 'txt' ? 'plain' : type}` })
		);
		a.download = name;
		a.click();
	}

	onMount(() => {
		paintCache();
	});

	let pixels = [];
	let command = '!p';
	let color = '000000';
	let textstring = '';
</script>

<svelte:head>
	<title>Grid Painter</title>
</svelte:head>

<main>
	<div class="flex flex-col justify-center items-center w-screen h-full m-2">
		<div class="flex">
			<label class="relative block">
				<span class="text-lg">Color Hex</span>

				<input
					class="placeholder:italic placeholder:text-slate-400 block bg-white w-full border border-slate-300 rounded-md py-2 pl-9 pr-3 shadow-sm focus:outline-none focus:border-sky-500 focus:ring-sky-500 focus:ring-1 sm:text-lg"
					placeholder="Color Hex"
					type="text"
					name="search"
					bind:value={color}
				/>
			</label>
			<label class="relative block">
				<span class="text-lg">Command</span>
				<input
					class="placeholder:italic placeholder:text-slate-400 block bg-white w-full border border-slate-300 rounded-md py-2 pl-9 pr-3 shadow-sm focus:outline-none focus:border-sky-500 focus:ring-sky-500 focus:ring-1 sm:text-lg"
					placeholder="Command"
					type="text"
					name="search"
					bind:value={command}
				/>
			</label>
		</div>
		<div class="border border-black">
			{#each board as row, rowIndex}
				<div class="flex">
					{#each row as cell, cellIndex}
						<div
							class="flex flex-row border h-6 w-6 border-gray-100"
							style={`background-color: #${cell}; border-color: #${cell}`}
							on:click={() => {
								paint(cellIndex, rowIndex, color);

								if (checkArr(pixels, [cellIndex + 1, rowIndex + 1, color])) {
									console.log('Pixel exists, not adding');
								} else {
									pixels.push([cellIndex + 1, rowIndex + 1, color]);
								}
							}}
						>
							<!-- {cell} -->
						</div>
					{/each}
				</div>
			{/each}
		</div>
		<div class="flex">
			<button
				class="text-lg border border-black rounded-lg p-5"
				on:click={() => {
					if (pixels.length > 0) {
						pixels.forEach((pixel) => {
							textstring += `${command} ${pixel[0]} ${pixel[1]} ${pixel[2]} \n`;
						});
						downloadFile('commands.txt', textstring);
						textstring = '';
					} else {
						alert("you didn't paint anything bro");
					}
				}}>Download .txt</button
			>
			<button
				class="text-lg border border-black rounded-lg p-5"
				on:click={() => {
					if (pixels.length > 0) {
						downloadJson(JSON.stringify(pixels), 'commands.json');
					} else {
						alert("you didn't paint anything bro");
					}
				}}>Download .json</button
			>
		</div>
	</div>
</main>

<style>
	:global(html, body) {
		background-color: rgba(0, 0, 0, 0) !important;
	}
</style>
