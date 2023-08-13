<script>
	export let name;

	// import Map from "https://deno.land/x/svelte_map/Maps.svelte"

	import { ethers } from "ethers";
	import QRCode from "qrcode";

	let amountOfWallets = 1;
	let counter = 0
	let resultOfCreateRandom;
	let walletFromPrivateKey;
	let canvasAddress = "canvasAddress"
	let canvasPrivateKey = "canvasPrivateKey"

	async function generateOne() {

		resultOfCreateRandom = ethers.Wallet.createRandom();
		walletFromPrivateKey = new ethers.Wallet(
			resultOfCreateRandom.chainCode
		);

		QRCode.toCanvas(
			document.getElementById(canvasAddress+counter),
			walletFromPrivateKey.address,
			function (error) {
				if (error) console.error(error);
				console.log("qr code addresssuccess!");
			}
		);
		QRCode.toCanvas(
			document.getElementById(canvasPrivateKey+counter),
			walletFromPrivateKey.privateKey,
			function (error) {
				if (error) console.error(error);
				console.log("qr code private key success!");
			}
		);
	}
	async function generateAll() {

		while (counter < amountOfWallets){
			await generateOne()
			counter++
		}
		
		counter = 0
	}
</script>

<main>
	<div class="noprint">
		<h1>Paperwallet Generator</h1>

		How many wallets do you want to generate?
		<p />
		<div class="center">
			<input type="number" bind:value={amountOfWallets} />
			<button on:click={generateAll}>generate</button>
		</div>
	</div>

	{#if walletFromPrivateKey}
		<h2>Public Key (Share)</h2>
		{walletFromPrivateKey.address} <br />
	{/if}
	<canvas id={canvasAddress+counter}/>
	<br />
	{#if walletFromPrivateKey}
		<h2>Private (Do not share)</h2>
		<b> Key: </b>
		{walletFromPrivateKey.privateKey} <br />
		<br />
	{/if}
	<canvas id={canvasPrivateKey+counter} />
	<p />
	{#if resultOfCreateRandom}
		<b> Mnemonic: </b>
		{resultOfCreateRandom.mnemonic.phrase}

		<p><br /></p>
		<button class="b1" onclick="window.print()">Print</button>
	{/if}
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}

	@media print {
		.noprint {
			display: none;
		}
	}
</style>
