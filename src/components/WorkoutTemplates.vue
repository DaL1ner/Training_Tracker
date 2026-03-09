<template>
  <div class="card mb-4">
    <div class="card-header bg-primary text-white">Конструктор тренировки</div>
    <div class="card-body">
      <!-- Название тренировки -->
      <div class="mb-3">
        <label class="form-label">Название</label>
        <input v-model="newTemplate.name" type="text" class="form-control" placeholder="День ног">
      </div>

      <!-- Список упражнений -->
      <div v-for="(ex, index) in newTemplate.exercises" :key="index" class="border p-3 mb-3 rounded bg-light">
        <div class="row g-2 align-items-center">
          <div class="col-md-4">
            <input v-model="ex.name" class="form-control" placeholder="Упражнение">
          </div>
          <div class="col-md-2">
            <input v-model.number="ex.sets" type="number" class="form-control" placeholder="Подходы">
          </div>
          <div class="col-md-2">
            <input v-model.number="ex.weight" type="number" class="form-control" placeholder="Вес (кг)">
          </div>
          <div class="col-md-2">
            <input v-model.number="ex.restTime" type="number" class="form-control" placeholder="Отдых (сек)">
          </div>
          <div class="col-md-2">
            <button @click="removeExercise(index)" class="btn btn-danger w-100">Удалить</button>
          </div>
        </div>
      </div>

      <button @click="addExercise" class="btn btn-outline-primary mb-3">+ Добавить упражнение</button>
      <button @click="saveTemplate" class="btn btn-success w-100">Сохранить шаблон</button>

      <!-- Список сохраненных -->
      <div v-if="templates.length" class="mt-4">
        <h5>Мои шаблоны</h5>
        <div class="list-group">
          <div v-for="t in templates" :key="t.id" class="list-group-item d-flex justify-content-between align-items-center">
            <div>
              <strong>{{ t.name }}</strong>
              <div class="small text-muted">{{ t.exercises.length }} упражнений</div>
            </div>
            <button @click="$emit('start', t)" class="btn btn-primary btn-sm">Запустить</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
    import { ref, defineEmits } from 'vue';

    const emit = defineEmits(['start']);

    // Состояние формы
    const newTemplate = ref({
      name: '',
      exercises: []
    });

    // Хранилище (пока в памяти)
    const templates = ref([]);

    const addExercise = () => {
      newTemplate.value.exercises.push({ name: '', sets: '', weight: '', restTime: '' });
    };

    const removeExercise = (index) => {
      newTemplate.value.exercises.splice(index, 1);
    };

    const saveTemplate = () => {
      if (!newTemplate.value.name || !newTemplate.value.exercises.length) {
        alert('Заполните название и добавьте упражнения');
        return;
      }
      // Сохраняем копию и очищаем форму
      templates.value.push({ ...newTemplate.value, id: Date.now() });
      newTemplate.value = { name: '', exercises: [] };
    };
</script>