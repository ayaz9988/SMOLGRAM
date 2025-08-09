<script setup lang="ts">
import Grid from '../GridComponent.vue';
import { gymHealthFacts } from '@/utils';

const randomNumber = Math.floor(Math.random() * gymHealthFacts.length);
const todayFact = gymHealthFacts[randomNumber];

const props = defineProps({
  firstIncompleteWorkoutIndex: Number,
  handleSelectWorkout: Function,
  handleResetPlan: Function,
});
</script>

<template>
  <div>
    <section id="dashboard">
      <div class="card tip-container">
        <h2>Welcome Smoldier</h2>
        <div>
          <p class="tip">
            <strong>Daily Tip</strong><br />
            {{ todayFact }}
          </p>
        </div>
        <button
          @click="
            () =>
              handleSelectWorkout!(
                firstIncompleteWorkoutIndex! < 0 ? 0 : firstIncompleteWorkoutIndex,
              )
          "
        >
          Start workout &rarr;
        </button>
      </div>

      <Grid v-bind="props" />
    </section>
  </div>
</template>

<style scoped>
.tip-container,
.tip-container div,
#dashboard {
  display: flex;
}

.tip-container,
#dashboard {
  flex-direction: column;
}

#dashboard {
  gap: 2rem;
}

.tip-container {
  gap: 0.5rem;
}

@media (min-width: 640px) {
  .tip-container {
    gap: 1rem;
  }
}
</style>
