<script lang="ts">
  import type {Question} from './types';
  import Chooser from './Chooser.svelte';
  import Line from './Line.svelte';

  let questions: Question[] = [];
</script>

<main class="container">
  <h1>Sorter</h1>
  <Chooser bind:questions />
  {#each questions as line}
    <Line bind:line />
  {/each}
</main>
