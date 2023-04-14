<script lang="ts">
  import type {Question} from './types';
  import type {ToCrypt} from './service';
  import {createKey, decode, decodeEncrypted, decrypt, encode, encodeEncrypted, encrypt, exportKey} from './service';
  import {importKey} from './service.js';

  export let questions: Question[] = [];

  let exportedKey = '';
  let exportedKeyToDecrypt = '';
  let loading = false;

  async function encryptData(): Promise<void> {
    loading = true;

    const key = await createKey();
    exportedKey = await exportKey(key);

    for (let question of questions) {
      await updateEncrypted(key, question, 'Name');
      await updateEncrypted(key, question, 'Surname / Family name');
      await updateEncrypted(key, question, 'email');
    }

    loading = false;
  }

  async function updateEncrypted(key: ToCrypt, question: Question, field: keyof Question) {
    const questionElement = question[field];
    if (!questionElement) {
      return;
    }
    const encryptedField = await encrypt(key, encode(questionElement));
    question[field] = encodeEncrypted(encryptedField);
  }

  async function updateDecrypted(key: ToCrypt, question: Question, field: keyof Question) {
    const questionElement = question[field];
    if (!questionElement) {
      return;
    }
    const decryptedField = await decrypt(key, decodeEncrypted(questionElement));
    question[field] = decode(decryptedField);
  }

  async function decryptData() {
    loading = true;

    const key = await importKey(exportedKeyToDecrypt);

    for (let question of questions) {
      await updateDecrypted(key, question, 'Name');
      await updateDecrypted(key, question, 'Surname / Family name');
      await updateDecrypted(key, question, 'email');
    }

    loading = false;
  }

  function copyKey() {
    navigator.clipboard.writeText(exportedKey)
  }
</script>

<style>
    [data-theme="orange"] {
        --primary: #e53935;
        --primary-hover: #d32f2f;
        --primary-focus: rgba(229, 57, 53, 0.125);
        --primary-inverse: #FFF;
    }
</style>

<section>
  <h2>Chiffrement</h2>
  <div class="grid">
    <button on:click={encryptData} role="button" disabled={!questions || !questions.length ? 'disabled' : null} aria-busy={loading ? 'true' : 'false'}>
      chiffrer
    </button>
    <input type="text" value={exportedKey} disabled>
    <button on:click={copyKey} disabled={!exportedKey ? 'disabled' : null} data-theme="orange">copier la clé</button>
  </div>
  <div class="grid">
    <button on:click={decryptData} role="button" class="secondary" disabled={!questions || !questions.length || !exportedKeyToDecrypt ? 'disabled' : null}
            aria-busy={loading ? 'true' : 'false'}>déchiffrer
    </button>
    <input type="text" bind:value={exportedKeyToDecrypt}>
  </div>
</section>
