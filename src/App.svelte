<script>
import CryptoJS from 'crypto-js';

let name;
let password;
// $: log = `${name}${password}`;

var encrypted = CryptoJS.AES.encrypt({password}, "Secret Passphrase");


</script>

<fieldset class="fieldset">
	
	<label>
		<input bind:value={name} placeholder="name" />
		Name
	</label>
	<label>
		<input bind:value={password} placeholder="password" />
		Password
	</label>
  <br>
{#if encrypted}
{encrypted}
{/if}

</fieldset>



<style>
.fieldset{position: absolute; top: 1em; left:1em; background-color: white;}
</style>