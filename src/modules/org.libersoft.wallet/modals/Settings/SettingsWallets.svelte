<script>
	import { module } from '../../module.js';
	import { wallets, addAddress, addWallet, walletAddresses } from '../../wallet.ts';
	import ModalNewWallet from '../../modals/new-wallet.svelte';

	import Address from '../../components/settings-wallets-address.svelte';
	import ButtonBar from '@/core/components/Button/ButtonBar.svelte';
	import Button from '@/core/components/Button/Button.svelte';
	import Table from '@/core/components/Table/Table.svelte';
	import Thead from '@/core/components/Table/TableThead.svelte';
	import TheadTh from '@/core/components/Table/TableTheadTh.svelte';
	import Tbody from '@/core/components/Table/TableTbody.svelte';
	import TbodyTr from '@/core/components/Table/TableTbodyTr.svelte';
	import TbodyTd from '@/core/components/Table/TableTbodyTd.svelte';
	import TableActionItems from '@/core/components/Table/TableActionItems.svelte';
	import Icon from '@/core/components/Icon/Icon.svelte';
	import Accordion from '@/core/components/Accordion/Accordion.svelte';
	import Modal from '@/core/components/Modal/Modal.svelte';
	import { Mnemonic } from 'ethers';
	let showModalPhrase = false;
	let activeIndex = null;

	function showNewWalletModal() {
		showModalPhrase = true;
	}

	function afterAddWallet() {
		activeIndex = wallets.length - 1;
	}

	function recover() {
		let phrase = window.prompt('Enter your recovery phrase');
		if (!phrase) return;
		let mn = Mnemonic.fromPhrase(phrase);
		addWallet(mn, ' - recovered');
		afterAddWallet();
	}
	function addAddressWithIndex(wallet) {
		let index = window.prompt('Enter the index');
		if (!index) return;
		addAddress(wallet, index);
		wallet.addresses = [...wallet.addresses];
	}

	function renameAddress(wallet, address) {
		let name = window.prompt('Enter the new name');
		if (!name) return;
		address.name = name;
		wallet.addresses = [...wallet.addresses];
		wallets.update(ws =>
			ws.map(w =>
				w === wallet
					? {
							...w,
							addresses: (w.addresses || []).map(a => (a === address ? { ...a, name } : a)),
						}
					: w
			)
		);
	}

	function deleteAddress(w, address) {
		address.deleted = true;
		// wallet.addresses = [...wallet.addresses]
		wallets.update(ws =>
			ws.map(item =>
				item === w
					? {
							...item,
							addresses: [...w.addresses],
							selected_address_index: w.indexNum,
						}
					: item
			)
		);
	}
</script>

<style>
	.wallet {
		display: flex;
		flex-direction: column;
		gap: 16px;
		padding: 16px 10px;

		:global(&:has(tbody:empty)) {
			:global(table) {
				display: none !important;
			}
		}
	}
</style>

<ButtonBar>
	<Button text="Create wallet" onClick={showNewWalletModal} />
	<Button img="modules/{module.identifier}/img/recover.svg" text="Recover" onClick={recover} />
</ButtonBar>
{#if $wallets.length > 0}
	<div class="bold">My wallets:</div>
{/if}
{#if $wallets.length === 0}
	<div class="bold">No wallets found</div>
{/if}
<Accordion items={$wallets} bind:activeIndex>
	{#snippet content(walleta)}
		<div class="wallet">
			<ButtonBar>
				<Button text="Add a new address" onClick={() => addAddress(walleta)} />
				<Button text="Add a new address (by index)" onClick={() => addAddressWithIndex(walleta)} />
			</ButtonBar>
			<Table>
				<Thead>
					<TheadTh>Index</TheadTh>
					<TheadTh>Alias</TheadTh>
					<TheadTh>Address</TheadTh>
					<TheadTh>Action</TheadTh>
				</Thead>
				<Tbody>
					{#each walletAddresses(walleta) as address, index}
						<TbodyTr>
							<TbodyTd title="Index">{address.index}</TbodyTd>
							<TbodyTd title="Alias">{address.name}</TbodyTd>
							<TbodyTd title="Address"><Address address={address.address} /></TbodyTd>
							<TbodyTd title="Action">
								<TableActionItems>
									<Icon img="img/edit.svg" alt="Rename" colorVariable="--icon-blue" size="20px" padding="5" onClick={() => renameAddress(walleta, address)} />
									<Icon img="img/del.svg" alt="Hide" colorVariable="--icon-red" size="20px" padding="5" onClick={() => deleteAddress(walleta, address)} />
								</TableActionItems>
							</TbodyTd>
						</TbodyTr>
					{/each}
				</Tbody>
			</Table>
		</div>
	{/snippet}
</Accordion>
<Modal title="New wallet" body={ModalNewWallet} bind:show={showModalPhrase} />
