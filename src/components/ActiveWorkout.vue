<template>
  <div class="container">
    <!-- Общий таймер тренировки -->
    <div class="text-center mb-5 p-3 bg-dark text-white rounded">
      <h1 class="display-3">{{ formatTime(totalTime) }}</h1>
      <p class="mb-0">Время в зале</p>
      <button @click="$emit('finish')" class="btn btn-danger mt-2">Завершить тренировку</button>
    </div>

    <!-- Список упражнений -->
    <div v-for="(ex, exIndex) in session.exercises" :key="exIndex" class="card mb-4 shadow-sm">
      <div class="card-header d-flex justify-content-between">
        <h4 class="m-0">{{ ex.name }}</h4>
        <span class="badge bg-secondary">{{ ex.weight }} кг</span>
      </div>
      <div class="card-body">
        <div v-for="(set, setIndex) in ex.sets" :key="set.id" class="d-flex justify-content-between align-items-center mb-2 p-2 border-bottom">
          <span class="fs-5">Подход {{ setIndex + 1 }}</span>
          
          <div class="d-flex align-items-center gap-3">
            <!-- Индикатор отдыха -->
            <span v-if="set.restActive" class="badge bg-warning text-dark fs-6">
              Отдых: {{ formatTime(set.restLeft) }}
            </span>

            <button 
              @click="toggleSet(exIndex, setIndex)" 
              class="btn" 
              :class="set.done ? 'btn-success' : 'btn-outline-secondary'"
            >
              {{ set.done ? 'Готово' : 'Сделать' }}
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
    import { ref, onMounted, onUnmounted, defineEmits } from 'vue';

    const props = defineProps(['template']);
    const emit = defineEmits(['finish']);

    // Инициализация сессии на основе шаблона
    // Мы создаем новую структуру данных, добавляя поля состояния (done, restActive)
    const session = ref({
      exercises: props.template.exercises.map(ex => ({
        ...ex,
        sets: Array.from({ length: ex.sets }, (_, i) => ({
          id: `${ex.name}-${i}-${Date.now()}`, // Уникальный ID для каждого подхода
          done: false,
          restActive: false,
          restLeft: ex.restTime
        }))
      }))
    });

    const totalTime = ref(0);
    let sessionTimer = null;
    const restTimers = {}; // Храним ID интервалов, чтобы можно было их остановить

    // Запуск общего таймера при монтировании компонента
    onMounted(() => {
    sessionTimer = setInterval(() => {
        totalTime.value++;
      }, 1000);
    });

    // Очистка всех таймеров при уходе со страницы
    onUnmounted(() => {
      clearInterval(sessionTimer);
      Object.values(restTimers).forEach(id => clearInterval(id));
    });

    const formatTime = (seconds) => {
      const m = Math.floor(seconds / 60).toString().padStart(2, '0');
      const s = (seconds % 60).toString().padStart(2, '0');
      return `${m}:${s}`;
    };

    const toggleSet = (exIndex, setIndex) => {
      const set = session.value.exercises[exIndex].sets[setIndex];
      const exercise = session.value.exercises[exIndex];

      if (!set.done) {
        // Начинаем подход
        set.done = true;
        startRestTimer(set, exercise.restTime);
      } 
      else {
        // Отменяем подход
        set.done = false;
        stopRestTimer(set);
      }
    };

    const startRestTimer = (set, duration) => {
      set.restActive = true;
      set.restLeft = duration;

      const timerId = setInterval(() => {
        if (set.restLeft > 0) {
          set.restLeft--;
        } 
        else {
          // Время вышло
          clearInterval(timerId);
          set.restActive = false;
          // В реальном приложении тут был бы звук
          alert('Время отдыха вышло!'); 
        }
      }, 1000);

      restTimers[set.id] = timerId;
    };

    const stopRestTimer = (set) => {
      set.restActive = false;
      if (restTimers[set.id]) {
          clearInterval(restTimers[set.id]);
          delete restTimers[set.id];
      }
    };
</script>