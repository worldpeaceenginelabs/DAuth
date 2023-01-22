<script>
	import QR from 'svelte-qr';
	let name = '';
  let pass = '';
  $: namepass = `${name}${pass}`;
</script>

<fieldset class="fieldset">
	
	<label>
	<input id="name" bind:value={name} placeholder="name" />
	</label>

	<label>
	<input bind:value={pass} placeholder="passphrase" />
	</label>

  {#if namepass}
  <div>
  {namepass}
  </div>
  {/if}

	{#key `${namepass}`}
	<div class="qr">
		<QR text={namepass} />
  	</div>
	{/key}

</fieldset>



<style>
.fieldset{position: absolute; top: 1em; left:1em; background-color: white;}
.qr{margin: 5%; width: 250;}
</style>