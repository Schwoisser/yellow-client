<script>
	import { identifier } from '../../messages.js';
	import Emojis from '../Emoji/Emojis.svelte';
	import Stickers from '../Stickers/Stickers.svelte';
	import GifSet from '../GifSet/GifSet.svelte';
	import ExpressionsItem from './ExpressionsItem.svelte';
	import ExpressionsSettings from './ExpressionsSettings.svelte';
	import { onMount, tick } from 'svelte';
	export let height;

	let expression = 'emojis';
	let elExpression;

	const expressions = {
		emojis: Emojis,
		stickers: Stickers,
		gifs: GifSet,
		settings: ExpressionsSettings,
	};

	export async function setCategory(e, name) {
		expression = name;
		e?.stopPropagation();
		e?.preventDefault();
		await currentTabOnShow();
	}

	onMount(async () => {
		await currentTabOnShow();
	});

	async function currentTabOnShow() {
		await tick();
		console.log('currentTabOnShow:', expression);
		elExpression?.onShow?.();
	}
</script>

<style>
	.expressions {
		overflow: hidden;
	}

	.categories {
		display: flex;
		align-items: center;
		justify-content: space-between;
		background-color: var(--color-primary-background);
		height: 45px;
		max-height: 45px;
	}
</style>

<div class="expressions" style="height: {height}">
	<div class="categories" role="none">
		<ExpressionsItem label="Emojis" icon={'modules/' + identifier + '/img/emoji.svg'} active={expression === 'emojis'} onClick={e => setCategory(e, 'emojis')} />
		<ExpressionsItem label="Stickers" icon={'modules/' + identifier + '/img/sticker.svg'} active={expression === 'stickers'} onClick={e => setCategory(e, 'stickers')} />
		<ExpressionsItem label="GIFs" icon={'modules/' + identifier + '/img/gif.svg'} active={expression === 'gifs'} onClick={e => setCategory(e, 'gifs')} />
		<ExpressionsItem label="Settings" icon={'img/settings.svg'} active={expression === 'settings'} onClick={e => setCategory(e, 'settings')} />
	</div>
	<svelte:component this={expressions[expression]} bind:this={elExpression} />
</div>
