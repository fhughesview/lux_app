<script>

	import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();


  async function spaces_api(apiUrl, bearerToken, method, body = null) {
    let req = {
        method: method, // *GET, POST, PUT, DELETE, etc.
        headers: {
           'Content-Type': 'application/json',
                 'Authorization': bearerToken
        }
    }

    if (body) {
      req.body = JSON.stringify(body);
    }

    const res = await fetch(apiUrl, req);
    let dataJson = await res.json();
    let data = await dataJson.data;

    if (res.ok) {
      console.log('data', data);
      return data;
    } else {
      throw new Error();
    }
  };

  export async function get_spaces(apiUrl, bearerToken) {
    return spaces_api(`${apiUrl}/v1/devices/spaces`, bearerToken, 'GET')
  }

  export async function update_spaces(apiUrl, uniqueId, bearerToken, body) {
    return spaces_api(`${apiUrl}/v1/devices/spaces/${uniqueId}`, bearerToken, 'PATCH', body)
  }
</script>
