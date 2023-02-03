<script>
    import Gun from "gun/gun";
    import SEA from "gun/sea";
  
    let gun = Gun({
      SEA: SEA
    });
    let user = gun.user();
    let username = "";
    let password = "";
    let error = "";
  
    async function signup() {
      try {
        
        let encrypted = await SEA.encrypt(password, SEA.pair());
        await user.create(username, encrypted);
        await user.auth(username, encrypted);
        error = "";
      } catch (e) {
        error = e.message;
      }
    }
  
    async function login() {
      try {
        
        let encrypted = await SEA.encrypt(password, SEA.pair());
        await user.auth(username, encrypted);
        error = "";
      } catch (e) {
        error = e.message;
      }
    }
  
    function logout() {
      user.leave();
    }
  </script>
  
  
  {#if user.is && user.alias}
  
    <p>Welcome, {user.alias}</p>
    <button on:click={logout}>Logout</button>
  {:else}
    <input type="text" bind:value={username} placeholder="Username" />
    <input type="password" bind:value={password} placeholder="Password" />
    <button on:click={signup}>Sign up</button>
    <button on:click={login}>Login</button>
    {#if error}
      <p style="color: red">{error}</p>
    {/if}
  {/if}