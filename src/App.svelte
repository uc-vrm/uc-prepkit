<script>
  import Header from './UI/Header.svelte';
  import Footer from './UI/Footer.svelte';
  let isTestBegin = false;
  let isTestNotStart = true;
  const beginTest = () =>{
    isTestNotStart = false;
    isTestBegin = true;
  }

</script>
<style>
  .start-test{
    padding: 20px 0px;
    width: 150px;
    text-align: center;
    background-color: #edf7f8;
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%,-50%);
    cursor: pointer;
    border-radius: 5px;
    user-select: none;
  }
  .start-test:active{
    background-color: rgb(188, 219, 219);
  }
</style>
<Header />
<main>
  {#if isTestNotStart}
    <div class="start-test" on:click="{beginTest}">Start Test</div>
  {:else}
    <div class="question-box" style="margin: 100px 50px;">
    </div>
  {/if}
</main>
{#if isTestBegin}
  <Footer {isTestBegin} />
{/if}