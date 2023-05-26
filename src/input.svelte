<script>
    let showInput = false;
    
  </script>
  
  <button on:click={() => showInput = true}>Open Input</button>
  
  {#if showInput}
    <input type="text" placeholder="Enter something" />
  {/if}
  