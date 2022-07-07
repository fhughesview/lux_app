<script>
	import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();

  export let loggedIn = false;
  export let bearerToken;
  export let apiUrl = 'https://api-demo.viewcorp.xyz/api';
  let userEmail = '';
  let userPass = '';
  let userRole = 'VBM';

  function login() {
    let data = {
        "email": userEmail,
        "password": userPass,
        "role": userRole
    };

    fetch(apiUrl + '/v1/auth/login',
       {
           method: 'POST', // *GET, POST, PUT, DELETE, etc.
           headers: {'Content-Type': 'application/json'},
           body: JSON.stringify(data) // body data type must match "Content-Type" header
        })
           .then((response) => response.json())
           .then((rspJson) => {
               let token = rspJson.data.access_token;
               bearerToken = 'Bearer ' + token;
               loggedIn = !loggedIn;
               console.log(bearerToken);
           })
    console.log(apiUrl, bearerToken)
  };

</script>



<p>
  <label>env:
  <select name="apiUrl" id="apiUrl" bind:value={apiUrl}>
    <option value="https://api-demo.viewcorp.xyz/api">demo</option>
  </select>
  </label>
</p>

<label>role:
<select name="userRole" id="userRole" bind:value={userRole}>
  <option value="admin">admin</option>
  <option value="APP">APP</option>
  <option value="VBM">VBM</option>
  <option value="VSP">VSP</option>
</select>
</label>

<p>
  <label>email:
  <input bind:value={userEmail}>
  </label>
</p>

<p>
  <label>pass:
  <input bind:value={userPass}>
  </label>
</p>


<button on:click={login}>
  Log in
</button>
