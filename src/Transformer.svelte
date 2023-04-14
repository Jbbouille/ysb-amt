<script lang="ts">
  import type {Question} from './types';
  import {isStopWord} from './service';
  import {franc} from 'franc-min'

  export let questions: Question[] = [];

  let loading = false;
  let donePhases = []
  const reasonIneligible = [
    'Name',
    'Surname / Family name',
    'email',
    'Please indicate your birthday*:',
    'Gender:',
    'What is your citizenship?',
    'What is your place of birth?',
    'Please mention the city where you were born.',
    'Which country are you currently living in?',
  ];

  function updateEligibleAcordingly(reasons: string, q: Question) {
    q.eligible = q.eligible + (reasons ? ` | ${reasons}` : '');
  }

  function missingInformation() {
    questions = questions.map(q => {
      q.eligible = '';
      const reasons = [];
      reasonIneligible.forEach(r => {
        if (!q[r]) {
          reasons.push(`Missing '${r}'`);
        }
      });
      updateEligibleAcordingly(reasons.join(' '), q);
      return q;
    })
  }

  function wrongLanguage() {
    questions = questions.map(q => {
      const e1 = q['E-1 What is your experience in youth participation and empowerment at local, national, international level?'];
      const lang = franc(e1);
      if (lang != 'eng') {
        updateEligibleAcordingly(`Wrong language '${lang}'`, q);
      }
      return q;
    })

  }

  function notEnoughWords(minWords: number) {
    questions = questions.map(q => {
      const e1 = q['E-1 What is your experience in youth participation and empowerment at local, national, international level?'];
      let size = e1.split(/\W+/).filter(w => !isStopWord(w)).length;
      if (size < minWords) {
        updateEligibleAcordingly('Not enough words in E1', q);
      }
      return q;
    });
  }

  function transform() {
    loading = true;

    donePhases = [...donePhases, 'Retirer les lignes vides.'];
    questions = questions.filter(q => q['Can you confirm that you meet the criteria listed above?'] === 'Yes, I meet the criteria listed above.');
    donePhases = [...donePhases, 'Retirer les lignes avec "No, I dont meet the criteria listed above".'];
    missingInformation();
    donePhases = [...donePhases, 'Rendre ineligible les candidatures avec informations manquantes.'];
    wrongLanguage();
    donePhases = [...donePhases, 'Rendre ineligible les candidatures avec la mauvaise langue.'];
    notEnoughWords(10);
    donePhases = [...donePhases, 'Rendre ineligible les candidatures avec moins de 10 mots.'];

    loading = false;
  }
</script>

<section>
  <h2>Transformation</h2>
  <div class="grid">
    <div>
      <button class="contrast" on:click={transform} disabled={!questions || !questions.length ? 'disabled' : null} aria-busy={loading ? 'true' : 'false'}>transformer</button>
    </div>
    <div>
      {#each donePhases as phase}
        <div>✅ {phase}</div>
      {/each}
    </div>
  </div>
</section>