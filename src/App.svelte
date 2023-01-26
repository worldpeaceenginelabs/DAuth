<script>
	import QR from 'svelte-qr';
	import { keccak512 } from 'js-sha3';
	
	// name and pass
	let name = '';
  	let pass = '';

	// salt name, salt pass
	let salt1 = "v->#Mg3@mXjR$\}<WJtu[m~m<=\/88";
	let salt2 = "Jtu[m~m<Mg3@mXjR$\}<W";
	let salt3 = "=\/88v->#Jtu[mv->#Mg3@mXjR$\}#Mg3@mXjR$\}<W"

	// combine name + salt1, combine pass + salt2
	$: saltedName = `${name}${salt1}`;
	$: saltedPass = `${pass}${salt2}`;

	// hash salted name and pass
	$: hashName = keccak512(saltedName);
	$: hashPass = keccak512(saltedPass)
	
	// combine hashName, hashpass and salt3
	$: combination = `${hashPass}${salt3}${hashName}`;
	
	// hash from combination
	$: hash = keccak512(combination);

	// later there is a QR Code generated from this hash. The QR Code can be reversed into the hash without encryption.
	// Only the owner of the right credentials (name+pass)can authenticate himself to be the owner of the QR-Code.
	// (Zero-Knowledge-Proof)
</script>



<!-- Markup-->
<fieldset class="fieldset" align="center">
	
	<label>
	<input id="name" bind:value={name} placeholder="name" />
	</label>

	<label>
	<input bind:value={pass} placeholder="passphrase" />
	</label>

  {#if combination}
  <div>
  Generate QR from:<br>
  		<div style:color="red">

		"{combination}" <!--shows the content of let combination-->

  		</div>
  </div>
  {/if}

	{#key `${hash}`}
	<div class="qr">
		<QR text={hash} version={-1} /> <!--Generates a QR Code from let hash-->
  	</div>
	{/key}

</fieldset>



<style>
.fieldset{position: absolute; top: 1em; left:1em; background-color: white;}
.qr{margin: 5%;}
</style>