<script>
  let display = 0;
  let textValue = "";
  let perMinute = 0;
  let curText = "";
  let message = "";

  let words = [];
  let interval = null;
  let curIndex = 0;
  let progressIndex = 0;

  const setText = (index) => {
    clearInterval(interval);
    interval = null;

    curIndex = index;
    progressIndex = index;
    curText = words[index];
    if (index >= words.length) {
      display = 0;
      return;
    }

    interval = setTimeout(() => {
      if (curIndex !== progressIndex) {
        return;
      }

      setText(curIndex+1);
    }, curText.endsWith(".")?(60/perMinute*1000*2):(60/perMinute*1000));
  }
</script>

<main>
  {#if message !== ""}
  <div class="splash">
    <div class="message">
      <span>Warning</span>
      <span>{message}</span>
      <button on:click={() => {message="";}}>OK</button>
    </div>
  </div>
  {/if}
  {#if display == 0}
    <input type="value" name="text" id="text" bind:value={textValue} placeholder="Enter the text" required>
    <input type="number" name="seconds" id="seconds" placeholder="Words per minute" bind:value={perMinute} required>
    <button on:click={() => {
      if (textValue.length <= 1) {
        message = "Enter the text!";
        return;
      }
      if (perMinute <= 0) {
        message ="Enter the words per minute value";
        return;
      }

      display = 1;
      words = textValue.split(" ");
      
      setText(0);
    }}>Start</button>
  {:else}
    <h1>{curText}</h1>
    <div class="controlPanel">
      <button on:click={() => {
        setText(Math.max(curIndex-Math.ceil(perMinute/60), 0));
      }}>Rewind 1 second</button>
      <input type="range" min="0" max={words.length-1} bind:value={progressIndex} on:change={() => {
        setText(progressIndex);
      }}>
      <button on:click={() => {
        if (interval == null) {
          setText(curIndex);
        } else {
          clearInterval(interval);
        }
        interval = null;
      }}>{interval==null?"Resume":"Pause"}</button>
      <button class="cancel" on:click={() => {
        words = [];
        display = 0;
        clearInterval(interval);
        interval = null;
      }}>Cancel</button>
    </div>
  {/if}
</main>

<style>
  * {
    user-select: none;
    -moz-user-select: none;
    -webkit-user-select: none;
  }

  span, h1 {
    cursor: default;
  }

  .splash {
    box-sizing: border-box;
    display: block;
    position: fixed;
    width: 100%;
    height: 100%;
    left: 0;
    top: 0;
    background-color: rgba(255, 255, 255, 0.7);
    padding: 200px;
  }

  .message {
    gap: 10px;
    box-sizing: border-box;
    display: flex;
    flex-direction: column;
    width: 100%;
    background-color: white;
    border: 1px solid black;
    border-radius: 5px;
    padding: 5px;
  }

  .message > * {
    text-align: center;
    padding: 0;
    margin: 0;
  }

  .message > span:nth-child(1) {
    font-size: 24px;
    font-weight: bolder;
  }

  main {
    display: flex;
    flex-direction: column;
    gap: 5px;
  }

  h1 {
    text-align: center;
    width: 100%;
  }

  .controlPanel {
    display: flex;
    flex-direction: column;
    position: absolute;
    bottom: 10px;
    left: 0;
    width: 100%;
  }

  button {
    cursor: pointer;
  }

</style>
