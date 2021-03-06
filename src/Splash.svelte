<script>
  import { onMount } from 'svelte';
  import { fly } from 'svelte/transition';
  import { _ } from 'svelte-i18n';

  import { getBoards } from './api.js';
  import { Icons } from './data.js';

  import BoardTable from './components/BoardTable.svelte';
  import LocaleSelect from './components/LocaleSelect.svelte';
  import Alert from './components/Alert.svelte';
  import CreateForm from './components/CreateForm.svelte';
  import Button from './components/Button.svelte';

  export let nav;
  export let errorAlertVisible = false;
  export let errorAlertMessage = 'error.network';

  let boards = [];
  let errorClearTimeout;

  async function doGetBoards() {
    // If the getBoards request fails, just silently omit the board list
    try {
      boards = await getBoards();
    } catch {
      boards = [];
    }
  }

  function error(message, err) {
    if (err) console.error(err);
    errorAlertVisible = true;
    errorAlertMessage = message;

    if (errorClearTimeout) clearTimeout(errorClearTimeout);
    errorClearTimeout = setTimeout(() => (errorAlertVisible = false), 3000);
  }

  function handleError({ detail: { message, err } }) {
    error(message, err);
  }

  onMount(async () => {
    await doGetBoards();
  });
</script>

<style>
  .scroll {
    overflow: auto;
  }

  .icon {
    display: inline-block;
    width: 1em;
    height: 1em;
    margin-top: -2px;
  }

  .links > a {
    color: #000;
  }

  .links > a:hover {
    text-decoration: none;
  }

  .card-text {
    opacity: 0.7;
  }

  .card {
    flex-basis: 10em;
    max-width: 20em;
    flex-grow: 1;
    flex-shrink: 1;
    margin: 1em;
    background-color: #0000;
    border: 0;
  }

  .card-top {
    height: 3em;
    width: 3em;
    margin: 0 auto;
  }

  .card-title {
    text-align: center;
  }

  .top-section {
    width: 40em;
  }

  .mid-section {
    flex-basis: 100px;
    flex-shrink: 0;
  }
</style>

<svelte:head>
  <meta property="og:url" content="https://retro.tools" />
</svelte:head>

<div class="d-flex flex-column scroll bg-primary h-100">
  <div class="px-2 pt-1 pb-5 bg-light">
    <div class="d-flex justify-content-between align-items-center">
      <h3 class="text-primary text-uppercase font-weight-bold p-0 m-0">
        retro.tools
      </h3>
      <div class="links text-right flex-grow-1 small text-nowrap">
        <Button
          color="light"
          href="https://github.com/d0x2f/retrograde.js"
          target="_blank"
          class="mr-1">
          <div class="icon">
            <Icons.github />
          </div>
          GitHub
        </Button>
        <Button
          color="light"
          class="mr-1"
          href="https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&amp;hosted_button_id=FJMVB9QFZQ79J&amp;source=url"
          target="_blank">
          <div class="icon text-danger">
            <Icons.heart />
          </div>
          {$_('general.donate')}
        </Button>
      </div>
      <LocaleSelect />
    </div>
    <hr class="mt-1 mb-4" />
    <div class="d-flex justify-content-center">
      <div class="top-section">
        <h1 class="text-center mb-5 text-dark" style="margin-top: 100px;">
          {$_('splash.hero_text')}
        </h1>
        <div class="d-flex flex-column justify-content-center">
          <CreateForm
            on:error={handleError}
            on:created={({ detail: boardId }) => nav.navigate(`/${boardId}`)} />
          <BoardTable
            {boards}
            on:click={({ detail: boardId }) => nav.navigate(`/${boardId}`)}
            on:error={handleError}
            on:deleted={doGetBoards} />
        </div>
      </div>
    </div>
  </div>
  <div class="bg-light flex-grow-1 mid-section" />
  <div class="d-flex justify-content-center py-5 bg-primary text-white">
    <div class="d-flex flex-column">
      <div class="d-flex justify-content-around py-4 flex-wrap">
        <div class="card">
          <div class="card-top">
            <Icons.anonymous />
          </div>
          <div class="card-body">
            <h5 class="card-title">Anonymous</h5>
            <p class="card-text">
              No personal information is stored and you're free to delete boards
              for absolute certainty.
              <br />
              <br />
              <a
                class="text-white"
                target="_blank"
                href="https://github.com/d0x2f/retrograde.js">
                Our code
              </a>
              is publicly available for anyone to verify.
              <br />
            </p>
          </div>
        </div>
        <div class="card">
          <div class="card-top">
            <Icons.phone />
          </div>
          <div class="card-body">
            <h5 class="card-title">Simple, Clean & Intuitive</h5>
            <p class="card-text">
              Our primary design goal is simplicity, we believe that tools
              should be easy to understand and require no manual.
            </p>
          </div>
        </div>
        <div class="card">
          <div class="card-top">
            <Icons.code />
          </div>
          <div class="card-body">
            <h5 class="card-title">Free & Open Source</h5>
            <p class="card-text">
              Retro.tools is free to use and the source code is available on
              <a
                class="text-white"
                target="_blank"
                href="https://github.com/d0x2f/retrograde.js">
                GitHub.
              </a>
            </p>
          </div>
        </div>
        <div class="card">
          <div class="card-top">
            <Icons.login />
          </div>
          <div class="card-body">
            <h5 class="card-title">No Logins!</h5>
            <p class="card-text">
              To stay anonymous and convenient, we won't ask you to create an
              account or to provide any other information, simply click and go!
              <br />
              <br />
            </p>
          </div>
        </div>
        <div class="card">
          <div class="card-top">
            <Icons.lock />
          </div>
          <div class="card-body">
            <h5 class="card-title">End to End Encryption</h5>
            <p class="card-text">
              Board encryption is optionally available using a password.
              <br />
              <br />
              All encryption is handled by the browser and our backend sees
              nothing but encrypted data!
            </p>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>

{#if errorAlertVisible}
  <div
    class="fixed-bottom"
    in:fly={{ y: 100, duration: 200 }}
    out:fly={{ y: 100, duration: 200 }}>
    <Alert class="fixed-bottom mb-0 py-1" color="danger" isOpen={true}>
      {$_(errorAlertMessage)}
    </Alert>
  </div>
{/if}
