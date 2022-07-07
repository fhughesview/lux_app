<script>
  import logo from './assets/svelte.png'
  import Login from './lib/Login.svelte'
  import Spaces from './lib/Spaces.svelte'

  const colors = [
        '#203060',
        '#404c74',
        '#606987',
        '#80859b',
        '#a0a1af',
        '#c0bec2',
        '#dfdad6',
        '#e3ddd8',
        '#e8e1db',
        '#ece4dd',
        '#f0e7e0',
        '#f4ebe2',
        '#f8eee5',
        '#f9eedc',
        '#faeed3',
        '#faeecb',
        '#fbeec2',
        '#fceeba',
        '#fdeeb1',
        '#fded9a',
        '#feec83',
        '#feeb6b',
        '#ffea54',
        '#ffe93d',
        '#ffe926'
  ]
  const getColor = (cel_val) => {
    let ind  = parseInt(Math.max(0, Math.min(cel_val / 3000, 1))*(colors.length-1));
    return colors[ind]
	}

  const update = (e) => {
    let event = e.type;
    let leftWidth = e.detail.leftWidth;
    let rightWidth = e.detail.rightWidth;
    let leftColumn = e.detail.leftColumn;
    let rightColumn = e.detail.rightColumn;
  }

  function handleMessage(event) {
    alert(event.detail.text);
    console.log('?', loggedIn);
  }

  function onSave(event) {
    for (const [uniqueId, spc] of Object.entries(modDict)) {
      let body = {
        'lux_target': spc.curr_lux_target,
        'curr_lux_target': spc.curr_lux_target
      };
      spaces.update_spaces(apiUrl, uniqueId, bearerToken, body);
      spc.lux_target = spc.curr_lux_target;
    }
  }

  function onLogin(loggedIn) {
    if (!loggedIn) {
      return Promise.resolve();
    } else {
      return spaces.get_spaces(apiUrl, bearerToken);
    }
  }

  function onEdit(index, uniqueId) {
    function inner() {
      if (!(uniqueId in modDict)) {
        modDict[uniqueId] = index;
      }
    }

    return inner
    }

  let loggedIn;
  let bearerToken;
  let apiUrl;
  let spaces;
  const modDict = {};
  $: spacesData = onLogin(loggedIn);

</script>

<main>
  <Spaces bind:this={spaces}/>
  {#if !loggedIn}
    <Login bind:loggedIn bind:apiUrl bind:bearerToken on:message={handleMessage}/>
  {:else}
    <div>
    <button class='flex-child' on:click={onSave}>Save</button>
    <!-- <button on:click={() => spaces.toggle()}>Do Stuff</button> -->
    {#await spacesData}
    {:then sdata}
      <table class='styled-table flex-child'>
        <thead>
          <tr>
            <th>Space</th>
            <th>Prev Target Lux</th>
            <th>New Target Lux</th>
          </tr>
        </thead>
        <tbody>
        {#each sdata.map((e, i) => [i, e])  as [ind, spc]}
          <tr>
            <td>{spc.name}</td>
            <td style={`background-color: ${getColor(spc.lux_target)}`}>
              {spc.lux_target}
            </td>
            <td contenteditable="true"
              style={`background-color: ${getColor(spc.curr_lux_target)}`}
              bind:innerHTML={spc.curr_lux_target}
              on:input={onEdit(spc, spc.uniqueId)}/>
          </tr>
          {/each}
        </tbody>
      </table>
    {:catch error}
  	     <p style="color: red">{error.message}</p>
    {/await}
    </div>
  {/if}
</main>

<style>
  :root {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen,
      Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  }

  main {
    text-align: center;
    padding: 1em;
    margin: 0 auto;
  }

  p {
    text-align: left;
    max-width: 14rem;
    margin: 1rem auto;
    line-height: 1.35;
  }

  .styled-table {
    border-collapse: collapse;
    margin: 25px 0;
    font-size: 0.9em;
    font-family: sans-serif;
    min-width: 400px;
    box-shadow: 0 0 20px rgba(0, 0, 0, 0.15);
  }

  .styled-table thead tr {
      background-color: #787c9c;
      color: #ffffff;
      text-align: left;
  }

  .styled-table th,
  .styled-table td {
      padding: 12px 15px;
  }

  .styled-table tbody tr {
    border-bottom: 1px solid #dddddd;
  }

  .styled-table tbody tr:nth-of-type(even) {
      background-color: #f3f3f3;
  }

  .styled-table tbody tr:last-of-type {
      border-bottom: 2px solid #009879;
  }

  /* .styled-table tbody tr.active-row {
      font-weight: bold;
      color: #009879;
  } */

  @media (min-width: 480px) {

    p {
      max-width: none;
    }
  }
  /* .flex-container {
      display: flex;
  } */
  .flex-child {
    flex: 1;
  }
</style>
