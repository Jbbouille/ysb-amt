<script lang="ts">
  import type {Question} from './types';
  import Uploader from './Uploader.svelte';
  import Downloader from './Downloader.svelte';
  import Encryptor from './Encryptor.svelte';
  import Transformer from './Transformer.svelte';
  import Splitter from './Splitter.svelte';

  let questions: Question[] = [];
  let chunks = 1;
</script>

<main class="container">
  <h1>YSB Applications Management Tool</h1>
  <Uploader bind:questions />
  <Encryptor bind:questions />
  <Transformer bind:questions />
  <Downloader bind:questions bind:chunks />
</main>
