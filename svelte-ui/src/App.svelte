<script>
	// import Map from "https://deno.land/x/svelte_map/Maps.svelte"

	import { ethers } from "ethers";
	import QRCode from "qrcode";

	let walletInfos = [];
	let amountOfWallets = 1;
	let counter = 0;
	let ready = false;
	let resultOfCreateRandom;
	let walletFromPrivateKey;

	async function generateOne() {
		let walletInfo = {};
		resultOfCreateRandom = ethers.Wallet.createRandom();
		walletFromPrivateKey = new ethers.Wallet(
			resultOfCreateRandom.chainCode
		);

		walletInfo.publicKey = walletFromPrivateKey.address;
		walletInfo.privateKey = walletFromPrivateKey.privateKey;
		walletInfo.mnemonic = resultOfCreateRandom.mnemonic.phrase;
		walletInfo.canvasIDPublicKey = `canvasAddress${counter}`;
		walletInfo.canvasIDPrivateKey = "canvasPrivateKey" + counter;

		return walletInfo;
	}

	function prepareQRCodes(data) {
		for (const walletInfo of data) {
			QRCode.toCanvas(
				document.getElementById(walletInfo.canvasIDPublicKey),
				walletInfo.publicKey,
				{ errorCorrectionLevel: "M", version: 4 },
				function (error) {
					if (error) console.error(error);
					console.log("qr code addresssuccess!");
				}
			);
			QRCode.toCanvas(
				document.getElementById(walletInfo.canvasIDPrivateKey),
				walletInfo.privateKey,
				{ errorCorrectionLevel: "M", version: 5 },
				function (error) {
					if (error) console.error(error);
					console.log("qr code private key success!");
				}
			);
		}
	}
	async function generateAll() {
		walletInfos = [];

		while (counter < amountOfWallets) {
			counter++;
			let walletInfo = await generateOne();
			walletInfos.push(walletInfo);
		}
		ready = true;

		setTimeout(() => {
			prepareQRCodes(walletInfos);
		}, 1 * 200);
	}
</script>

<main>
	{#if !ready}
		<div class="noprint">
			<h1>Paperwallet Generator</h1>

			How many wallets do you want to generate?
			<p />
			<div class="center">
				<input type="number" bind:value={amountOfWallets} />
				<button on:click={generateAll}>generate</button>
			</div>
		</div>
	{/if}

	<div class="center">
		{#if ready}
			{#each walletInfos as wi, index}
				<div
					class={index % 2 === 0 && index > 1 ? "pageBreak" : "relax"}
				>
					<a href="https://cultmagazine.org" target="_blank">
						<h4>cultmagazine.org</h4>
					</a>
					<div class="small">
						<b> Public Key (Share): </b>
						{wi.publicKey} <br />
						<canvas id={wi.canvasIDPublicKey} />
						<p />
						<b> Private Key (do not share): </b>
						{wi.privateKey} <br />
						<b> Mnemonic (do not share): </b>
						{wi.mnemonic}
						<br />

						<canvas id={wi.canvasIDPrivateKey} />
					</div>
				</div>
			{/each}
		{/if}
		<div class="noprint">
			{#if walletInfos.length === amountOfWallets}
				<div class="noprint">
					<p></p>
					<button class="b1" onclick="window.print()">Print Paperwallets</button>
					<p />
					<button class="b1" onclick="window.location.reload()"
						>Reload Page</button
					>
				</div>

				<p><br /></p>
				Generated public wallet addresses:
				<p />
				{#each walletInfos as wiForList}
					{wiForList.publicKey} <br>
				{/each}
			{/if}
			<p><br></p>
		</div>
	</div>
</main>

<style>
	main {
		text-align: center;
		padding: 0em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		/* color: #ff3e00; */
		color: rgb(10, 156, 235);
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	.small {
		margin-left: 0;
		padding-left: 0;
		font-size: 9px;
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

		.pageBreak {
			page-break-before: always;
		} /* page-break-after works, as well */
	}
</style>
