<script>
    import SeaAuth from "./SeaAuth.svelte";
  
    
    let publicKey, privateKey, signature, verified, publicKeyForm, privateKeyForm;
   
    // Generate key pair 
  
    async function handleGenerate() {
      
      var ecdsaKeyPair = await window.crypto.subtle.generateKey(
        {
          name: "ECDSA",
          namedCurve: "P-256",
        },
        true,
        ["sign", "verify"]
      );
      publicKey = await window.crypto.subtle.exportKey("jwk", ecdsaKeyPair.publicKey);
      privateKey = await window.crypto.subtle.exportKey("jwk", ecdsaKeyPair.privateKey);
  
      // sign public key with private key
  
      signature = await window.crypto.subtle.sign(
        {
          name: "ECDSA",
          hash: {name: "SHA-256"},
        },
        ecdsaKeyPair.privateKey,
        new TextEncoder().encode(JSON.stringify(publicKey))
      );
      
      // verify the authenticity of the public key
      verified = await window.crypto.subtle.verify(
        {
          name: "ECDSA",
          hash: {name: "SHA-256"},
        },
        ecdsaKeyPair.publicKey,
        signature,
        new TextEncoder().encode(JSON.stringify(publicKey))
      );
  
      if (verified) {
        console.log("The public key is authentic.");
      } else {
        console.log("The public key is not authentic.");
      }
    }
  
  
  
    async function handleSubmit(event) {
      event.preventDefault();
  
      publicKey = await window.crypto.subtle.importKey("jwk", publicKeyForm, {
      name: "ECDSA",
      namedCurve: "P-256"
    }, true, ["verify"]);
  
    privateKey = await window.crypto.subtle.importKey("jwk", privateKeyForm, {
      name: "ECDSA",
      namedCurve: "P-256"
    }, true, ["sign"]);
  
      
      // sign public key with private key
  
      signature = await window.crypto.subtle.sign(
        {
          name: "ECDSA",
          hash: {name: "SHA-256"},
        },
        privateKey,
        new TextEncoder().encode(JSON.stringify(publicKey))
      );
      
      // verify the authenticity of the public key
      verified = await window.crypto.subtle.verify(
        {
          name: "ECDSA",
          hash: {name: "SHA-256"},
        },
        publicKey,
        signature,
        new TextEncoder().encode(JSON.stringify(publicKey))
      );
  
      if (verified) {
        console.log("The public key is authentic.");
      } else {
        console.log("The public key is not authentic.");
      }
    };
  
  </script>
  
  
  <!--Start Screen-->
  <fieldset>
  <div>
    <h1>Start Screen</h1>
    <button on:click={handleGenerate}>Sign Up</button>
  </div>
  </fieldset>
  
  <!--Login Screen-->
  <fieldset>
    <div>
      <form>
      <h1>Login Screen</h1>
      <input bind:value={publicKeyForm} name="public-key">
      <input bind:value={privateKeyForm} name="private-key">
      <button on:click={handleSubmit}>Login</button>
    </form>
    </div>
    </fieldset>
  
  <!--Welcome Screen-->
  <fieldset>
      <div>
      <h1>Welcome Screen</h1>
      <p>Your public-key: {JSON.stringify(publicKey)} <br> Your private-key: {JSON.stringify(privateKey)}</p>
    </div>
  </fieldset>