<script lang="ts">
  import "./app.css";
  import { Alert } from "flowbite-svelte";

  import * as Colyseus from "colyseus.js"; // not necessary if included via <script> tag.

  import svelteLogo from "./assets/svelte.svg";
  import viteLogo from "/vite.svg";

  var client = new Colyseus.Client("ws://localhost:2567");

  var currentRoom: Colyseus.Room;

  const connect = () => {
    client
      .joinOrCreate("game_room")
      .then((room) => {
        console.log(room.sessionId, "joined", room.name);

        currentRoom = room;

        room.onStateChange((state) => {
          console.log(room.name, "has new state:", state);
        });

        room.onMessage("message_type", (message) => {
          console.log(room.sessionId, "received on", room.name, message);
        });

        room.onError((code, message) => {
          console.log(room.sessionId, "couldn't join", room.name);
        });
      })
      .catch((e) => {
        console.log("JOIN ERROR", e);
      });
  };

  const sendMessage = () => {
    if (currentRoom) {
      currentRoom.send("hello", { message: "Hello!" });
    }
  };
</script>

<main>
  <div class="p-8">
    <Alert>
      <span class="font-medium">Info alert!</span>
      Change a few things up and try submitting again.
    </Alert>
  </div>
  <div>
    <a href="https://vitejs.dev" target="_blank" rel="noreferrer">
      <img src={viteLogo} class="logo" alt="Vite Logo" />
    </a>
    <a href="https://svelte.dev" target="_blank" rel="noreferrer">
      <img src={svelteLogo} class="logo svelte" alt="Svelte Logo" />
    </a>
  </div>
  <h1>Vite + Svelte</h1>

  {#if !currentRoom}
    <div class="card">
      <button on:click={connect}> Connect to server </button>
    </div>
  {:else}
    <div class="card">
      <h2>Room: {currentRoom.name}</h2>
      <p>Session ID: {currentRoom.sessionId}</p>
      <p>State: {JSON.stringify(currentRoom.state)}</p>
      <button on:click={sendMessage}> Send message </button>
    </div>
  {/if}

  <p>
    Check out <a
      href="https://github.com/sveltejs/kit#readme"
      target="_blank"
      rel="noreferrer">SvelteKit</a
    >, the official Svelte app framework powered by Vite!
  </p>

  <p class="read-the-docs">Click on the Vite and Svelte logos to learn more</p>
</main>
