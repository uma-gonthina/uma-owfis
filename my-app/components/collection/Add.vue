<template>
  <div>
      <label
        for="note"
        class="block text-sm font-semibold leading-6 text-gray-900"
        >note</label
      >
      <div class="mt-2.5">
        <input
          name="note"
          v-model="note"
          id="note"
          rows="4"
          class="block w-full rounded-md border-0 px-3.5 py-2 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
        />
      </div>

      <div class="mt-10">
        <button
          type="submit"
          @click="add()"
          class="block rounded-md bg-indigo-600 px-3.5 py-2.5 text-center text-sm font-semibold text-white shadow-sm hover:bg-indigo-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600"
        >
          save
        </button>
      </div>
  </div>
</template>
<script setup lang="ts">
const emit = defineEmits(["add"]);
const note = ref("");
const add = () => {
  emit("add", note.value);
  note.value=''
};
</script>
