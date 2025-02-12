<script setup lang="ts">
import { ref } from "vue";
import students from './students.json';
import topics from './topics.json';

const min = ref(1);
const max = ref(28);
const students_class = ref('8b')
const participant1 = ref(null);
const participant2 = ref(null);
const teams = ref([]);
const participant1_name = ref('')
const participant2_name = ref('')
const usedNumbers = ref(new Set());
const level = ref('1')
const team_topic = ref('')
const team_title = ref('')
const topic_requirements = ref('')
const topic_tools = ref('')

const generate = () => {
  let participantOne = generateUniqueNumber();
  let participantTwo = generateUniqueNumber();
  // Make sure that 2nd player is not the same as 1st.
  if(participantOne === participantTwo) {
    participantTwo = generateUniqueNumber();
  }

  // Do some kinky shit here.
  // if(students_class.value.length > 0 &&
  //     students_class.value === '8b' &&
  //     (participantOne == 9 || participantTwo == 9 || participantOne == 19 || participantTwo == 19)
  // ) {
  //   // Remove the numbers which were chosen at random.
  //   usedNumbers.value.delete(participantOne)
  //   usedNumbers.value.delete(participantTwo)
  //
  //   participantOne = 19
  //   participantTwo = 9
  //
  //   usedNumbers.value.add(participantOne);
  //   usedNumbers.value.add(participantTwo);
  // }

  // Add the participants for the show.
  participant1.value = participantOne
  participant2.value = participantTwo

  // Figure out their names.
  participant1_name.value = students[students_class.value][participant1.value - 1]['name']
  participant2_name.value = students[students_class.value][participant2.value - 1]['name']

  generateTopic()
  // Add them to the team board.
  addTeam();
}


const addTeam = () => {
  teams.value.push({
    participant_1: participant1.value,
    participant_2: participant2.value,
    participant1_name: participant1_name.value,
    participant2_name: participant2_name.value,
    topic: team_topic.value,
    title: team_title.value,
    requirements: topic_requirements.value,
    tools: topic_tools.value,
  })
}

// Generate a unique number that hasn't been used yet
const generateUniqueNumber = () => {
  let num;
  let attempts = 0;

  do {
    num = Math.floor(Math.random() * (max.value - min.value + 1)) + min.value;
    attempts++;
    if (attempts > max.value) {
      return null;
    }
  } while (usedNumbers.value.has(num));

  usedNumbers.value.add(num);
  return num;
};

const generateTopic = () => {
  let projects = [];

  // Figure out what level to select from
  switch (level.value) {
    case '2':
      projects = topics['projects']['level_2']
    break;
    case '3':
      projects = topics['projects']['level_3']
    break;
    default:
      projects = topics['projects']['level_1']
    break;
  }

  let random_project = Math.floor(Math.random() * (projects.length - 1 + 1) + 1);
  let topics_list = projects[random_project - 1]['topics'];
  let random_topic;

  if(topics_list !== 'undefined' && topics_list.length > 0) {
    random_topic = Math.floor(Math.random() * (topics_list.length - 1 + 1) + 1);
    team_topic.value = topics_list[random_topic]
  }

  team_title.value = projects[random_project - 1]['title']
  topic_requirements.value = projects[random_project - 1]['requirements']
  topic_tools.value = projects[random_project - 1]['tools']
}

</script>

<template>
  <header>
    <img alt="Vue logo" class="logo" src="./assets/50_years_full.png" width="125" height="125" />

    <div class="wrapper">
    </div>
  </header>

  <main>
    <div v-if="teams.length == 0 || teams.length != (max / 2)">
    <div class="quiz-wrapper">
      <div>
        <label class="block mb-1">Class:</label>
        <input v-model="students_class" type="text" class="border p-2 rounded" />
      </div>

      <div>
        <label class="block mb-1">Level:</label>
        <select v-model="level">
          <option disabled value="">Please select one</option>
          <option>1</option>
          <option>2</option>
          <option>3</option>
        </select>
      </div>

      <div>
        <label class="block mb-1">Number of players:</label>
        <input v-model.number="max" type="number" class="border p-2 rounded" />
      </div>
    </div>
    <div>Участник 1: {{ participant1 }}</div>
    <div>Участник 2: {{ participant2 }}</div>
    <div>Категория: {{ team_title }}</div>

    <button :disabled="!students_class.trim()" @click="generate" class="mt-4 px-4 py-2 bg-blue-500 text-white rounded">
      Избери мъченици
    </button>
    </div>
    <div class="motivation" v-else>
      Нека силата бъде с Вас!
      <img alt="Vue logo" class="yoda" src="./assets/fantasy-troll-beautiful-environment.jpg" width="300" height="300" />
    </div>

    <div class="teams-wrapper">
      <div class="member" v-for="(team, index) in teams" :key="index">
        <h2>Team Number {{ index + 1 }}</h2>
        <div class="members">
          <div class="section-title title">
            Members
          </div>
          <div class="players">
            <span>{{ team.participant_1 }}. {{ team.participant1_name }}</span>
            <span>{{ team.participant_2 }}. {{ team.participant2_name }}</span>
          </div>
        </div>
        <div class="topic">
          <div class="section-title title">
            Category
          </div>
          <div>
            <h2>{{ team.title }}</h2>
            <h3>{{ team.topic }}</h3>
          </div>
          <div class="section-title title">
            Requirements
          </div>
          <div>
            {{ team.requirements }}
          </div>
          <div class="section-title title">
            Tools
          </div>
          <div>
            {{ team.tools }}
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<style scoped>
header {
  margin-bottom: 4rem;
}

main {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.teams-wrapper {
  display: flex;
  gap: 20px;
  margin-top: 4rem;
  flex-wrap: wrap;

  .member {
    border: 1px solid #fff;
    padding: 10px;

    h2 {
      font-weight: bold;
    }

    .members {
      display: flex;
      flex-direction: column;

      .title {
        flex: 100%;
      }

      .players {
        display: flex;
        flex-direction: column;
      }
    }
  }
}

.section-title {
  font-size: 20px;
  color: #fff;
  border-bottom: 1px solid;
  text-align: center;
  margin-bottom: 2rem;
  margin-top: 2rem;
}

.topic {
  display: flex;
  flex-direction: column;
  flex-wrap: wrap;

  h2,
  h3 {
    max-width: 300px;
  }
}

.motivation {
  font-size: 50px;
  text-transform: uppercase;
}

.yoda {
  display: block;
  margin: 0 auto;
}

</style>
