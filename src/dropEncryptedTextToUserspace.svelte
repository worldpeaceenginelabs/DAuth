<script>
    import Gun from 'gun';
  
    let db = Gun(['http://localhost:8765/gun']).get('root'); //you need always a root node
  
    let text;
  
    function saveEncryptedText() {
        let encryptedText = Gun.SEA.encrypt(db.user(), text);
        db.user().get('encryptedText').put(encryptedText);
    }
  </script>
  
  <input placeholder="Encrypted User Space" type="text" bind:value={text}/>
  <button on:click={saveEncryptedText}>Save Text Encrypted in User Space</button>