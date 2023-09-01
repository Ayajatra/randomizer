<script lang="ts">
	import type { Action } from 'svelte/action';
	
	let itemsText = '';
	let numberOfItemsToBeChosenText = '1';
	let animationDurationText = '2.5';
	let chosenItems: string[] = [];
	let randomizeChosenItemsIntervalId = -1;
	let randomizeChosenItemsTimeoutId = -1;
	let isRandomizing = false;

	const randomInt = (min: number, max: number) => Math.floor(Math.random() * (max - min + 1)) + min;

	function randomizeChosenItems() {
		const items = itemsText.trim().split('\n');
		const numberOfItemsToBeChosen = parseInt(numberOfItemsToBeChosenText);
		const animationDuration = parseFloat(animationDurationText);

		window.clearInterval(randomizeChosenItemsIntervalId);
		window.clearTimeout(randomizeChosenItemsTimeoutId);
		isRandomizing = true;

		randomizeChosenItemsIntervalId = window.setInterval(() => {
			const chosenItemsIndex: number[] = [];
			for (let i = 0; i < numberOfItemsToBeChosen; i++) {
				let chosenItemIndex = randomInt(0, items.length - 1);
				while (chosenItemsIndex.includes(chosenItemIndex)) {
					chosenItemIndex = (chosenItemIndex + 1) % items.length;
				}
	
				chosenItemsIndex.push(chosenItemIndex);
			}

			chosenItems = chosenItemsIndex.map(x => items[x]);
		}, (animationDuration * 1000) <= 100 ? 0 : 100);

		randomizeChosenItemsTimeoutId = window.setTimeout(
			() => {
				window.clearInterval(randomizeChosenItemsIntervalId);
				isRandomizing = false;
			},
			animationDuration * 1000
		);
	}

	const editableNotEditable: Action = (node) => {
		node.setAttribute('contenteditable', 'true');
		node.spellcheck = false;
		node.oncut = () => false;
		node.onpaste = () => false;
		node.onkeydown = (event) => {
			if (event.metaKey || event.ctrlKey) return;
			event.preventDefault();
		}
	};
</script>

<div class="container mx-auto 2xl:px-4 min-h-screen pt-2">
	<h1 class="h1 text-center mb-6">Randomizer</h1>
	<div class="flex space-x-5 items-stretch">
		<div class="flex-1 space-y-4">
			<h2 class="h2">Configuration:</h2>
			<label class="label">
				<span>Item(s)</span>
				<textarea class="textarea" bind:value={itemsText} rows="10" placeholder="Item 1
Item 2
Item 3
..."></textarea>
			</label>
			<label class="label">
				<span>Number of item(s) to be chosen</span>
				<div class="input-group input-group-divider grid-cols-[1fr_auto]">
					<input type="text" bind:value={numberOfItemsToBeChosenText}>
					<div class="input-group-shim">item(s)</div>
				</div>
			</label>
			<label class="label">
				<span>Animation duration</span>
				<div class="input-group input-group-divider grid-cols-[1fr_auto]">
					<input type="text" bind:value={animationDurationText}>
					<div class="input-group-shim">second(s)</div>
				</div>
			</label>
			<button on:click={randomizeChosenItems} disabled={isRandomizing} class="btn variant-filled-primary w-full">
				<span>
					<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-5 h-5">
						<path fill-rule="evenodd" d="M15.312 11.424a5.5 5.5 0 01-9.201 2.466l-.312-.311h2.433a.75.75 0 000-1.5H3.989a.75.75 0 00-.75.75v4.242a.75.75 0 001.5 0v-2.43l.31.31a7 7 0 0011.712-3.138.75.75 0 00-1.449-.39zm1.23-3.723a.75.75 0 00.219-.53V2.929a.75.75 0 00-1.5 0V5.36l-.31-.31A7 7 0 003.239 8.188a.75.75 0 101.448.389A5.5 5.5 0 0113.89 6.11l.311.31h-2.432a.75.75 0 000 1.5h4.243a.75.75 0 00.53-.219z" clip-rule="evenodd" />
					</svg>							
				</span>
				<span>{isRandomizing ? 'Randomizing...' : 'Randomize'}</span>
			</button>
		</div>
		<div class="flex items-center">
			<span class="divider-vertical h-4/5"></span>
		</div>
		<div class="flex-1 space-y-4">
			<h2 class="h2">Result:</h2>
			<div class="label">
				<span class="cursor-default">Choosen Items</span>
				<div use:editableNotEditable class="form-textarea bg-surface-800 border-surface-600 rounded resize-y overflow-auto h-[488px]">
					{@html chosenItems.join('<br>')}
				</div>
			</div>
		</div>
	</div>
</div>
