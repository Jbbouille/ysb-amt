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

  function updateEligibleAccordingly(reason: string, q: Question) {
    if (!q.eligible && reason) {
      q.eligible = reason;
      return;
    }
    if (q.eligible && reason) {
      q.eligible = `${q.eligible} | ${reason}`;
    }
  }

  function missingInformation() {
    questions = questions.map(q => {
      const reasons = [];
      reasonIneligible.forEach(r => {
        if (!q[r]) {
          reasons.push(`Missing information in '${r}'`);
        }
      });
      updateEligibleAccordingly(reasons.join(' '), q);
      return q;
    })
  }

  function wrongLanguage() {
    questions = questions.map(q => {
      const e1 = q['E-1 What is your experience in youth participation and empowerment at local, national, international level?'];
      const lang = franc(e1);
      if (lang != 'eng') {
        updateEligibleAccordingly(`Wrong language: ${lang}`, q);
      }
      return q;
    })

  }

  function notEnoughWords(minWords: number) {
    questions = questions.map(q => {
      const e1 = q['E-1 What is your experience in youth participation and empowerment at local, national, international level?'];
      if (e1.includes("https://") || e1.includes("http://")) {
        return q;
      }
      let size = e1.split(/\W+/).filter(w => !isStopWord(w)).length;
      if (size < minWords) {
        updateEligibleAccordingly(`Not enough words: ${size}`, q);
      }
      return q;
    });
  }

  function addAnId() {
    questions = questions.map((q, i) => {
      q['#Id'] = `${i}`;
      return q;
    })
  }

  function reorderLines() {
    questions = questions.map((q) =>
      ({
        'Can you confirm that you meet the criteria listed above?': q['Can you confirm that you meet the criteria listed above?'],
        'Which option are you applying for?': q['Which option are you applying for?'],
        'Name': q['Name'],
        'Surname / Family name': q['Surname / Family name'],
        'email': q['email'],
        'Please indicate your birthday*:': q['Please indicate your birthday*:'],
        'Gender:': q['Gender:'],
        'What is your citizenship?': q['What is your citizenship?'],
        'What is your citizenship? - If you selected other, please specify:': q['What is your citizenship? - If you selected other, please specify:'],
        'What is your place of birth?': q['What is your place of birth?'],
        'What is your place of birth? - If you selected other, please specify:': q['What is your place of birth? - If you selected other, please specify:'],
        'Please mention the city where you were born.': q['Please mention the city where you were born.'],
        'Which country are you currently living in?': q['Which country are you currently living in?'],
        'Which country are you currently living in? - If you selected other, please specify:': q['Which country are you currently living in? - If you selected other, please specify:'],
        'Please upload your signed Nomination Letter (pdf-format) here:': q['Please upload your signed Nomination Letter (pdf-format) here:'],
        'O-1 Information about the nominating organisation Name of the organisation::Please indicate below the requested information': q['O-1 Information about the nominating organisation Name of the organisation::Please indicate below the requested information'],
        'O-1 Information about the nominating organisation Contact details: (Email):Please indicate below the requested information': q['O-1 Information about the nominating organisation Contact details: (Email):Please indicate below the requested information'],
        'O-1 Information about the nominating organisation Website: (if available):Please indicate below the requested information': q['O-1 Information about the nominating organisation Website: (if available):Please indicate below the requested information'],
        'O-1 Information about the nominating organisation Social Media: (if available):Please indicate below the requested information': q['O-1 Information about the nominating organisation Social Media: (if available):Please indicate below the requested information'],
        'O-2 Which category fits best to your nominating organisation?': q['O-2 Which category fits best to your nominating organisation?'],
        'O-2 Which category fits best to your nominating organisation? - You selected Other, please specify:': q['O-2 Which category fits best to your nominating organisation? - You selected Other, please specify:'],
        'O-3 Please indicate briefly the thematic areas your organisation is focusing on:': q['O-3 Please indicate briefly the thematic areas your organisation is focusing on:'],
        'O-4 Please describe briefly the area(s) of expertise of your organisation/network.': q['O-4 Please describe briefly the area(s) of expertise of your organisation/network.'],
        'O-5 What is your role in your organisation?': q['O-5 What is your role in your organisation?'],
        'O-6 Why are you a good representative of your organisation?': q['O-6 Why are you a good representative of your organisation?'],
        'O-7 Please explain briefly how your organisation has contributed to policy processes (e.g. consultation, advocacy etc.), in particular on youth.': q['O-7 Please explain briefly how your organisation has contributed to policy processes (e.g. consultation, advocacy etc.), in particular on youth.'],
        'O-8 Please give an example of how your organisation has successfully contributed to strengthening youth participation (e.g. campaigns, outreach to vulnerable youth, etc.).': q['O-8 Please give an example of how your organisation has successfully contributed to strengthening youth participation (e.g. campaigns, outreach to vulnerable youth, etc.).'],
        'O-9 Please briefly explain which regions, countries, continents your organisation is covering through its activities.': q['O-9 Please briefly explain which regions, countries, continents your organisation is covering through its activities.'],
        'D-1 How many individual members does your organisation have?': q['D-1 How many individual members does your organisation have?'],
        'D-2 Please describe the profile of your individual members? (e.g. age range, general background, interest and expertise)': q['D-2 Please describe the profile of your individual members? (e.g. age range, general background, interest and expertise)'],
        'D-3 As a network (or similar), how many organisations are member of your network?': q['D-3 As a network (or similar), how many organisations are member of your network?'],
        'D-4 As a network (or similar), please briefly describe the profile of your member organisations / groups (e.g. age range, general background, interest and expertise)': q['D-4 As a network (or similar), please briefly describe the profile of your member organisations / groups (e.g. age range, general background, interest and expertise)'],
        'M-5 Is your organisation member of a larger network?': q['M-5 Is your organisation member of a larger network?'],
        'M-5 Is your organisation member of a larger network? - If YES, please specify:': q['M-5 Is your organisation member of a larger network? - If YES, please specify:'],
        'What is the highest education level you have completed?': q['What is the highest education level you have completed?'],
        'What is the highest education level you have completed? - You have chosen "other". Please specify:': q['What is the highest education level you have completed? - You have chosen "other". Please specify:'],
        'What is your current occupation?': q['What is your current occupation?'],
        'What is your current occupation? - You have chosen "other". Please specify:': q['What is your current occupation? - You have chosen "other". Please specify:'],
        'Are you a member of a Civil Society Organisation, NGO, diaspora organisation, etc.?': q['Are you a member of a Civil Society Organisation, NGO, diaspora organisation, etc.?'],
        'You have chosen Other. Please specify the type of organisation:': q['You have chosen Other. Please specify the type of organisation:'],
        'What is the name of your organisation?': q['What is the name of your organisation?'],
        'Please list all the Civil Society Organisations and other relevant organisations/programmes (e.g. UNICEF, etc.) that you have been involved in.': q['Please list all the Civil Society Organisations and other relevant organisations/programmes (e.g. UNICEF, etc.) that you have been involved in.'],
        'What are your areas of expertise in the frame of working with young people?': q['What are your areas of expertise in the frame of working with young people?'],
        'Please describe briefly your interest and/or expertise:': q['Please describe briefly your interest and/or expertise:'],
        'What are your areas of interest when it comes to working with young people?': q['What are your areas of interest when it comes to working with young people?'],
        'Please describe briefly your areas of interest:': q['Please describe briefly your areas of interest:'],
        'E-1 What is your experience in youth participation and empowerment at local, national, international level?': q['E-1 What is your experience in youth participation and empowerment at local, national, international level?'],
        'E-2 What is your experience in reaching out to large groups of youth, including to youth that are in disadvantaged or vulnerable situations?': q['E-2 What is your experience in reaching out to large groups of youth, including to youth that are in disadvantaged or vulnerable situations?'],
        'M-1 Why do you want to join the Youth Sounding Board?': q['M-1 Why do you want to join the Youth Sounding Board?'],
        'M-2 How will you contribute to the work of the Youth Sounding Board?': q['M-2 How will you contribute to the work of the Youth Sounding Board?'],
        'M-3 Imagine that you are selected for the Youth Sounding Board and after two years you look back on your work. What concrete result would you like to have achieved?': q['M-3 Imagine that you are selected for the Youth Sounding Board and after two years you look back on your work. What concrete result would you like to have achieved?'],
        'M-4 How should the Youth Sounding Board contribute to the implementation of the Youth Action Plan according to you?': q['M-4 How should the Youth Sounding Board contribute to the implementation of the Youth Action Plan according to you?'],
        'C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 1:Language': q['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 1:Language'],
        'C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 1:Level (A1, A2, B1, B2, C1, C2)': q['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 1:Level (A1, A2, B1, B2, C1, C2)'],
        'C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 2:Language': q['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 2:Language'],
        'C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 2:Level (A1, A2, B1, B2, C1, C2)': q['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 2:Level (A1, A2, B1, B2, C1, C2)'],
        'C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 3:Language': q['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 3:Language'],
        'C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 3:Level (A1, A2, B1, B2, C1, C2)': q['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 3:Level (A1, A2, B1, B2, C1, C2)'],
        'C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 4:Language': q['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 4:Language'],
        'C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 4:Level (A1, A2, B1, B2, C1, C2)': q['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 4:Level (A1, A2, B1, B2, C1, C2)'],
        'C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 5:Language': q['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 5:Language'],
        'C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 5:Level (A1, A2, B1, B2, C1, C2)': q['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 5:Level (A1, A2, B1, B2, C1, C2)'],
        'C-2 Please describe how you have successfully used your communication skills (e.g. conducting campaigns, verbal and written skills, etc.) in youth participation and engagement processes, including the use of traditional and social media to advocate and negotiate for youth.': q['C-2 Please describe how you have successfully used your communication skills (e.g. conducting campaigns, verbal and written skills, etc.) in youth participation and engagement processes, including the use of traditional and social media to advocate and negotiate for youth.'],
        'In order to ensure diversity and inclusion within the Youth Sounding Board, we would like to ask you whether you identify as a person with disability, special needs, a member of a minority group, with special needs and/or in a situation of vulnerability. Providing this information is not mandatory. Please note that not providing this information will not prevent a candidate from applying.': q['In order to ensure diversity and inclusion within the Youth Sounding Board, we would like to ask you whether you identify as a person with disability, special needs, a member of a minority group, with special needs and/or in a situation of vulnerability. Providing this information is not mandatory. Please note that not providing this information will not prevent a candidate from applying.'],
        'I identify as a person with:': q['I identify as a person with:'],
        'OPTIONAL - You have chosen "other". Please specify:': q['OPTIONAL - You have chosen "other". Please specify:'],
        'OPTIONAL - Is there anything you would like to add to your application?': q['OPTIONAL - Is there anything you would like to add to your application?'],
        '#Id': q['#Id'],
        'eligible': q['eligible'],
        'totalNotationIndividual': 0,
        'totalNotationOrganisation': 0,
    })
    )
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
    notEnoughWords(20);
    donePhases = [...donePhases, 'Rendre ineligible les candidatures avec moins de 20 mots.'];
    addAnId();
    donePhases = [...donePhases, 'Ajouter un #Id de ligne.'];
    reorderLines();
    donePhases = [...donePhases, 'Réordonner les colonnes.'];

    loading = false;
  }
</script>

<div>
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
</div>
