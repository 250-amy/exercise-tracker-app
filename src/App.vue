<script setup>
import { ref, onMounted, computed, watch } from "vue";

const exercises = ref([]);
const name = ref("");

const input_content = ref("");
const input_sets = ref("");
const input_reps = ref("");
const input_weight = ref("");
const weight_choice = ref(null);

const exercises_asc = computed(() =>
  exercises.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

watch(
  exercises,
  (newVal) => {
    localStorage.setItem("exercises", JSON.stringify(newVal));
  },
  { deep: true }
);

const addExercise = () => {
  if (input_content.value.trim() === "") {
    return;
  }
  exercises.value.push({
    content: input_content.value,
    sets: input_sets.value,
    reps: input_reps.value,
    weight: input_weight.value,
    category: weight_choice.value,
    done: false,
    createdAt: new Date().getTime(),
  });
  input_content.value = "";
  input_sets.value = "";
  input_reps.value = "";
  input_weight.value = "";
};

const removeExercise = (exercise) => {
  exercises.value = exercises.value.filter((t) => t !== exercise);
};

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  exercises.value = JSON.parse(localStorage.getItem("exercises")) || [];
});
</script>

<template>
  <main class="app bg-gray-800 shadow-xl rounded-2xl px-8 py-6 sm:px-32 lg:px-64">
    <section class="greeting mb-6">
      <h1 class="title font-bold uppercase tracking-wide mb-1">Exercise Tracker</h1>
      <p class="text-sm">Lift weights, log, repeat 💪</p>
    </section>

    <div id="form">
      <section class="create-exercise">
        <h2 class="subtitle mb-2 text-left font-medium">
          Create a New Exercise
        </h2>

        <form @submit.prevent="addExercise" autocomplete="off">
          <div class="flex flex-col mb-2 w-auto">
            <div class="relative">
              <p class="input-label flex content-start">Name of exercise:</p>
              <input
                type="text"
                id="content"
                class="rounded-lg border-transparent flex-1 appearance-none border border-gray-300 w-full py-2 px-4 bg-white text-gray-700 placeholder-gray-400 shadow-sm text-base focus:outline-none focus:ring-2 focus:ring-amber-500 focus:border-transparent"
                placeholder="e.g. shoulder press"
                v-model="input_content"
                required
              />
            </div>
          </div>
          <div class="inline-flex mb-4 justify-between space-x-2 w-auto">
            <div class="relative">
              <p class="input-label flex content-start"># of sets:</p>
              <input
                type="number"
                class="rounded-lg border-transparent mr-2 flex-1 appearance-none border border-gray-300 w-full py-2 px-4 bg-white text-gray-700 placeholder-gray-400 shadow-sm text-base focus:outline-none focus:ring-2 focus:ring-amber-500 focus:border-transparent"
                placeholder="0"
                name="sets"
                id="sets"
                v-model="input_sets"
                required
              />
            </div>
            <div class="relative">
              <p class="input-label flex content-start"># of reps:</p>
              <input
                type="number"
                class="rounded-lg border-transparent flex-1 appearance-none border border-gray-300 w-full py-2 px-4 bg-white text-gray-700 placeholder-gray-400 shadow-sm text-base focus:outline-none focus:ring-2 focus:ring-amber-500 focus:border-transparent"
                name="reps"
                placeholder="0"
                id="reps"
                v-model="input_reps"
                required
              />
            </div>
          </div>
          <p class="input-label flex content-start">Weight:</p>
          <div class="flex">
            <div class="w-full text-left mr-3">
              <input
                type="number"
                class="rounded-lg border-transparent flex-1 inline appearance-none border border-gray-300 w-full py-2 px-4 bg-white text-gray-700 placeholder-gray-400 shadow-sm text-base focus:outline-none focus:ring-2 focus:ring-amber-500 focus:border-transparent"
                name="weight"
                placeholder="0"
                id="weight"
                v-model="input_weight"
                required
              />
            </div>
            <span class="flex items-center pl-2 w-1/3">
              <label class="text-justify">
                <input
                  type="radio"
                  name="category"
                  id="category2"
                  value="lb"
                  v-model="weight_choice"
                />
                <span class="ml-1 mr-1 sm:ml-1 sm:mr-6">lb</span>
              </label>

              <label class="text-justify">
                <input
                  type="radio"
                  name="category"
                  id="category1"
                  value="kg"
                  v-model="weight_choice"
                />
                <span class="ml-1">kg</span>
              </label>
            </span>
          </div>

          <div class="flex w-full my-4">
            <button
              type="submit"
              class="uppercase py-2 px-4 bg-gradient-to-br from-pink-500 to-orange-400 focus:ring-4 focus:outline-none focus:ring-pink-200 dark:focus:ring-pink-800 transition-opacity duration-1000 ease-out opacity-100 hover:opacity-75 text-white tracking-wide w-full text-center text-base font-bold shadow-md rounded-lg"
            >
              Add Exercise
            </button>
          </div>
        </form>
      </section>
    </div>

    <section class="exercise_list mt-8 w-auto">
      <h2 class="subtitle text-left font-medium">Exercise List</h2>
      <p class="mb-4 flex text-sm text-left content-start w-auto">
        Your inputted exercises will appear here. You can edit the information at any time; just click on the text that you want to change. 
      </p>
    <div class="flex justify-center">
      <div class="w-9/12 sm:w-full" id="exercise_list">
        <div
          v-for="exercise in exercises_asc"
          v-bind:key="exercise.id"
          :class="`exercise-item ${exercises.done && 'done'}`"
        >
          <div class="exercise-content">
            <div class="mb-4">
              <div
                id="list"
                class="flex flex-col mb-2 w-auto border-gray-300 bg-white shadow-sm"
              >
                <input
                  type="text"
                  class="font-semibold text-xl text-gray-800 uppercase tracking-wide bg-transparent mx-3 pb-3"
                  v-model="exercise.content"
                />
                <div
                  class="flex py-2 px-4 uppercase text-gray-600 font-semibold"
                >
                  # of sets:
                  <input
                    type="text"
                    size="4"
                    class="font-light text-gray-600 bg-transparent mx-3"
                    v-model="exercise.sets"
                  />
                </div>
                <div
                  class="flex py-2 px-4 uppercase text-gray-600 font-semibold"
                >
                  # of reps:<input
                    type="text"
                    size="4"
                    class="font-light text-gray-600 bg-transparent mx-3"
                    v-model="exercise.reps"
                  />
                </div>
                <div
                  class="flex py-2 pl-4 uppercase text-gray-600 font-semibold"
                >
                  Weight&nbsp;<span class="lowercase"
                    >({{ exercise.category }})</span
                  >:<input
                    type="text"
                    size="4"
                    class="font-light text-gray-600 bg-transparent ml-3"
                    v-model="exercise.weight"
                  />
                </div>

                <div class="actions">
                  <button
                    class="delete uppercase py-2 px-4 mt-4 w-1/2 bg-gradient-to-br from-pink-500 to-orange-400 focus:ring-4 focus:outline-none focus:ring-pink-200 dark:focus:ring-pink-800 transition-opacity duration-1000 ease-out opacity-100 hover:opacity-75 text-white text-center text-base font-bold tracking-wide shadow-md rounded-lg"
                    @click="removeExercise(exercise)"
                  >
                    Delete
                  </button>
                </div>
                </div>//
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

