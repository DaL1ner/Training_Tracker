<template>
  <div class="container py-4">
    <h1 class="text-center mb-4">Training Tracker</h1>
    
    <div v-if="!activeTemplate">
      <WorkoutTemplates @start="startWorkout" />
    </div>
    <div v-else>
      <button @click="activeTemplate = null" class="btn btn-secondary mb-3">← Назад</button>
      <ActiveWorkout :template="activeTemplate" @finish="finishWorkout" />
    </div>
  </div>
</template>

<script setup>
  import { ref } from 'vue';
  import WorkoutTemplates from './components/WorkoutTemplates.vue';
  import ActiveWorkout from './components/ActiveWorkout.vue';

  const activeTemplate = ref(null);

  const startWorkout = (template) => {
    activeTemplate.value = template;
  };

  const finishWorkout = () => {
    if(confirm('Тренировка завершена! Вернуться в меню?')) {
      activeTemplate.value = null;
    }
  };
</script>