<script>
    export let value;
</script>

{#if value === 1}
<button width="12" height="30">1</button>
{:else if value === 2}
<button width="20" height="31">2</button>
{:else if value === 3}
<button width="21" height="32">3</button>
{:else if value === 4}
<button width="24" height="30">4</button>
{:else if value === 5}
<button width="21" height="31">5</button>
{:else if value === 6}
<button width="22" height="32">6</button>
{:else if value === 7}
<button width="20" height="30">7</button>
{:else if value === 8}
<button width="22" height="32">8</button>
{:else if value === 9}
<button width="23" height="32">9</button>
{/if}