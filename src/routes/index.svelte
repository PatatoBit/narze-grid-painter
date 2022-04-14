<script lang="ts">
	import { onMount } from 'svelte';
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

	onMount(() => {
		paintCache();
	});

	let pixels = [];
	let command = '!p';
	let color = '000000';
</script>

<main>
	<div class="flex flex-col justify-center items-center w-screen h-full m-2">
		<div class="flex">
			<label class="relative block">
				<span class="text-lg">Color Hex</span>

				<input
					class="placeholder:italic placeholder:text-slate-400 block bg-white w-full border border-slate-300 rounded-md py-2 pl-9 pr-3 shadow-sm focus:outline-none focus:border-sky-500 focus:ring-sky-500 focus:ring-1 sm:text-6xl"
					placeholder="Color Hex"
					type="text"
					name="search"
					bind:value={color}
				/>
			</label>
			<label class="relative block">
				<span class="text-base">Command</span>
				<input
					class="placeholder:italic placeholder:text-slate-400 block bg-white w-full border border-slate-300 rounded-md py-2 pl-9 pr-3 shadow-sm focus:outline-none focus:border-sky-500 focus:ring-sky-500 focus:ring-1 sm:text-6xl"
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
							class="flex flex-row border h-12 w-12 border-gray-100"
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

		<button
			class="text-7xl border border-black rounded-lg p-5"
			on:click={() => {
				if (pixels.length > 0) {
					pixels.forEach((pixel) => {
						console.log(`${command} ${pixel[0]} ${pixel[1]} ${pixel[2]}`);
					});
				} else {
					alert("you didn't paint anything bro");
				}
			}}>Download .txt</button
		>
	</div>
</main>

<style>
	:global(html, body) {
		background-color: rgba(0, 0, 0, 0) !important;
	}
</style>
