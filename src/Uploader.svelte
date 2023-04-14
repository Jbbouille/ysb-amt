<script lang="ts">
  import {onMount} from 'svelte';
  import {read, utils} from 'xlsx';
  import type {Question} from './types';

  export let questions: Question[] = [];

  onMount(async () => {
    const input = document.getElementById('questioner');
    input.onchange = e => {
      const file = (e.target as HTMLInputElement).files[0];
      const reader = new FileReader();
      reader.readAsArrayBuffer(file);
      reader.onload = readerEvent => {
        const content = readerEvent.target.result;
        const wb = read(content, {dateNF: 'dd/mm/yyyy', cellDates: true});
        questions = utils.sheet_to_json(wb.Sheets[wb.SheetNames[0]], {dateNF: 'dd/mm/yyyy'});
      }
    }
  });
</script>

<div>
  <h2>Fichier</h2>
  <input type="file" id="questioner" name="questions" accept=".xlsx,.xls">
</div>
