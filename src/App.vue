<script setup lang="ts">
import { computed, onMounted, ref } from 'vue';
import Layout from './components/layouts/Layout.vue';
import DashboardPage from './components/pages/DashboardPage.vue';
import WelcomePage from './components/pages/WelcomePage.vue';
import WorkoutPage from './components/pages/WorkoutPage.vue';
import { workoutProgram, type WorkoutDay } from './utils';

const defaultData = {};
for (const workoutIdx in workoutProgram) {
  const workoutData = workoutProgram[workoutIdx];
  //@ts-expect-error ts bullshit
  defaultData[workoutIdx] = {};

  for (const e of workoutData.workout) {
    //@ts-expect-error ts bullshit
    defaultData[workoutIdx][e.name] = '';
  }
}

const selectedDisplay = ref(1);
const data = ref<{
  [key: number]: WorkoutDay;
}>(defaultData);
const selectedWorkout = ref(-1);

const isWorkoutComplete = computed(() => {
  const currentWorkout = data.value?.[selectedWorkout.value];
  if (!currentWorkout) return false;

  const isCompleteCheck = Object.values(currentWorkout).every((ex) => !!ex);
  return isCompleteCheck;
});

const firstIncompleteWorkoutIndex = computed(() => {
  const allWorkouts = data.value;
  if (!allWorkouts) return -1;
  for (const [index, workout] of Object.entries(allWorkouts)) {
    const isComplete = Object.values(workout).every((ex) => !!ex);
    if (!isComplete) return parseInt(index);
  }
  return -1;
});

function handleChangeDisplay(idx: number) {
  selectedDisplay.value = idx;
}

function handleSelectWorkout(idx: number) {
  selectedDisplay.value = 3;
  selectedWorkout.value = idx;
}

function handleSaveWorkout() {
  localStorage.setItem('workouts', JSON.stringify(data.value));
  selectedDisplay.value = 2;
  selectedWorkout.value = -1;
}

function handleResetPlan() {
  selectedDisplay.value = 2;
  selectedWorkout.value = -1;
  data.value = defaultData;
  localStorage.removeItem('workouts');
}

onMounted(() => {
  if (!localStorage) return;
  if (localStorage.getItem('workouts')) {
    const savedData = JSON.parse(localStorage.getItem('workouts')!);
    data.value = savedData;
    selectedDisplay.value = 2;
  }
});
</script>

<template>
  <!-- <RouterView /> -->
  <Layout>
    <!-- page 1 -->
    <WelcomePage :handleChangeDisplay="handleChangeDisplay" v-if="selectedDisplay === 1" />
    <!-- page 2 -->
    <DashboardPage
      :handleResetPlan="handleResetPlan"
      :firstIncompleteWorkoutIndex="firstIncompleteWorkoutIndex"
      :handleSelectWorkout="handleSelectWorkout"
      v-if="selectedDisplay === 2"
    />
    <!-- page 3 -->
    <WorkoutPage
      :handleSaveWorkout="handleSaveWorkout"
      :isWorkoutComplete="isWorkoutComplete"
      :data="data"
      :selectedWorkout="selectedWorkout"
      v-if="workoutProgram[selectedWorkout]"
    />
  </Layout>
</template>

<style scoped></style>
