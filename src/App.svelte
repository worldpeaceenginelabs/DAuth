<script>
  let user = {};
  let storageLimit = 1048576; // 1 MB in bytes
  const hashAlgorithm = 'SHA-256';
  const curve = 'P-256';
  const signingAlgorithm = {
    name: 'ECDSA',
    hash: { name: hashAlgorithm },
  };

  async function hashPassword(password, salt) {
    const encoder = new TextEncoder();
    const data = encoder.encode(password + salt);
    const hash = await window.crypto.subtle.digest(hashAlgorithm, data);
    return Array.from(new Uint8Array(hash))
      .map(b => b.toString(16).padStart(2, '0'))
      .join('');
  }

  async function createKeyPair() {
    const keyPair = await window.crypto.subtle.generateKey(
      { name: 'ECDSA', namedCurve: curve },
      true,
      ['sign', 'verify']
    );
    const publicKey = await window.crypto.subtle.exportKey('raw', keyPair.publicKey);
    const privateKey = await window.crypto.subtle.exportKey('jwk', keyPair.privateKey);
    return { publicKey, privateKey };
  }

  async function signPublicKey(privateKey, publicKey) {
    const signature = await window.crypto.subtle.sign(
      signingAlgorithm,
      privateKey,
      publicKey
    );
    return Array.from(new Uint8Array(signature))
      .map(b => b.toString(16).padStart(2, '0'))
      .join('');
  }

  async function storeKeys(user, publicKey, signature) {
    let storageSize = JSON.stringify(localStorage).length;
    if (storageSize + JSON.stringify({ user, publicKey, signature }).length > storageLimit) {
      console.log('Local storage size limit reached');
      return;
    }
    localStorage.setItem(user, JSON.stringify({ publicKey, signature }));
    console.log('Stored keys:', localStorage);
  }

  function handleSubmit(e) {
    user = { ...e.target.elements };
    let salt = 'random_salt';
    hashPassword(user.password, salt).then(hashedPassword => {
      user.password = hashedPassword;
      createKeyPair().then(({ publicKey, privateKey }) => {
        signPublicKey(privateKey, publicKey).then(signature => {
          storeKeys(user.username, publicKey, signature);
        });
      });
    });
  }

  function emptyTemplate() {
    localStorage.clear();
  }
</script>

{#if Object.keys(user).length}
  <p>Welcome to the main app!</p>
{:else}
  <form on:submit|preventDefault={handleSubmit}>
    <input type="text" name="username" placeholder="Username">
    <input type="password" name="password" placeholder="Password">
    <button type="submit">Login</button>
  </form>
{/if}

<style></style>