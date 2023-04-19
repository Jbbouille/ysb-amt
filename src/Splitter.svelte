<script lang="ts">
  import type {Question} from './types';
  import {isStopWord} from './service';
  import {franc} from 'franc-min'

  export let questions: Question[] = [];
  export let chunks = 1;
  $: aproximateNumber = Math.floor(questions.length / chunks);
</script>

<div>
  <h2>Découpage</h2>
  <div class="grid">
    <div>
      <input type="number" min="1" max="10" bind:value={chunks}>
    </div>
    <span>~{aproximateNumber} lines per file</span>
  </div>
</div>
