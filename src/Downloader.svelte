<script lang="ts">
  import {utils, writeFile} from 'xlsx';
  import type {Question} from './types';

  export let questions: Question[] = [];

  function downloadFile() {
    const workSheet = utils.json_to_sheet<Question>(questions, {cellDates: true, dateNF: 'dd/mm/yyyy'});
    writeFile({Sheets: {'Content': workSheet}, bookType: 'xlsx', SheetNames: ['Content']}, 'out.xlsx');
  }
</script>

<style>
    [data-theme="green"] {
        --primary: #43a047;
        --primary-hover: #388e3c;
        --primary-focus: rgba(67, 160, 71, 0.125);
        --primary-inverse: #FFF;
    }
</style>

<section>
  <h2>Télécharger</h2>
  <button on:click={downloadFile} disabled={!questions || !questions.length ? 'disabled' : null} data-theme="green">Save</button>
</section>
