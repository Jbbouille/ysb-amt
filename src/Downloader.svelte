<script lang="ts">
  import {utils, writeFile} from 'xlsx';
  import type {Question} from './types';

  export let questions: Question[] = [];
  export let chunks = 1;

  function splitToChunks<T>(array: T[], parts: number): T[][] {
    const result: T[][] = [];
    for (let i = parts; i > 0; i--) {
      result.push(array.splice(0, Math.ceil(array.length / i)));
    }
    return result;
  }

  function downloadFile() {
    if (chunks === 1) {
      const workSheet = utils.json_to_sheet<Question>(questions, {cellDates: true, dateNF: 'dd/mm/yyyy hh:mm:ss'});
      writeFile({Sheets: {'Content': workSheet}, bookType: 'xlsx', SheetNames: ['Content']}, 'out.xlsx');
      return;
    }
    const splitQuestions = splitToChunks(questions, chunks);
    for (let i = 0; i < splitQuestions.length; i++){
      const c = splitQuestions[i];
      const workSheet = utils.json_to_sheet<Question>(c, {cellDates: true, dateNF: 'dd/mm/yyyy hh:mm:ss'});
      writeFile({Sheets: {'Content': workSheet}, bookType: 'xlsx', SheetNames: ['Content']}, `out-${i + 1}.xlsx`);
    }
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

<div>
  <h2>Télécharger</h2>
  <button on:click={downloadFile} disabled={!questions || !questions.length ? 'disabled' : null} data-theme="green">Save</button>
</div>
