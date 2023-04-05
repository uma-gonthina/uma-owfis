<template>
  <template>
    <div class="flex justify-end">
      <button
        type="button"
        class="rounded-full bg-indigo-600 p-1.5 text-white shadow-sm hover:bg-indigo-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600"
        @click="open = true"
      >
        <PlusIcon class="h-5 w-5" aria-hidden="true" />
      </button>
      <TransitionRoot as="template" :show="open">
        <Dialog as="div" class="relative z-10" @close="open = false">
          <div class="fixed inset-0" />

          <div class="fixed inset-0 overflow-hidden">
            <div class="absolute inset-0 overflow-hidden">
              <div
                class="pointer-events-none fixed inset-y-0 right-0 flex max-w-full pl-10"
              >
                <TransitionChild
                  as="template"
                  enter-from="translate-x-full"
                  enter-to="translate-x-0"
                  leave-from="translate-x-0"
                  leave-to="translate-x-full"
                >
                  <DialogPanel class="pointer-events-auto w-screen max-w-md">
                    <div
                      class="flex h-full flex-col divide-y divide-gray-200 bg-white shadow-xl"
                    >
                      <div
                        class="flex min-h-0 flex-1 flex-col overflow-y-scroll py-6"
                      >
                        <div class="px-4 sm:px-6">
                          <div class="flex items-start justify-between">
                            <DialogTitle
                              class="text-base font-semibold leading-6 text-gray-900"
                              >Suggestions</DialogTitle
                            >
                            <div class="ml-3 flex h-7 items-center">
                              <button
                                type="button"
                                class="rounded-md bg-white text-gray-400 hover:text-gray-500 focus:outline-none focus:ring-2 focus:ring-indigo-500"
                                @click="open = false"
                              >
                                <span class="sr-only">Close panel</span>
                                <XMarkIcon aria-hidden="true" />
                              </button>
                            </div>
                          </div>
                        </div>
                       
                        <div
                          v-for="(note, index) in notes"
                          :key="index"
                          class="mb-3 border-b border-gray-200 bg-white px-4 py-5 sm:px-6"
                        >
                          <CollectionList
                            :note="note"
                            :index="index"
                            @emitData="emitData"
                          />

                          <CollectionEdit v-if="editInput.uid" :note="editInput" @edit="edit" />
                        </div>
                      </div>

                      <div>
                        <CollectionAdd @add="add"></CollectionAdd>
                      </div>
                      
                    </div>
                  </DialogPanel>
                </TransitionChild>
              </div>
            </div>
          </div>
        </Dialog>
      </TransitionRoot>
    </div>
  </template>
</template>
<script setup lang="ts">
import {
  TransitionChild,
  DialogPanel,
  TransitionRoot,
  Dialog,
} from "@headlessui/vue";
import { PlusIcon, XMarkIcon } from "@heroicons/vue/24/outline";
import { ConstantTypes } from "@vue/compiler-core";

interface FormItemSchema {
  url: string;
  entityId: string | number;
  editUrl: string;
  projectId: string | number;
  entity: string;
}
const props = withDefaults(defineProps<FormItemSchema>(), {
  url: "",
  entityId: "",
  editUrl: "",
  projectId: "",
  entity: "",
});
const editInput = ref([]);
const { data: notesdata } = await useAuthLazyFetch(`${props.url}`, {});
const notes = notesdata.value;
const open = ref(true);

console.log("notes.value -->", notes.value);
const emitData = (note: Object) => {
  note.value == "edit" ? (editInput.value = note.note) : deleteNote(note);
};
const add = async (note: any) => {
  const { data } = await  useAuthLazyFetchPost(
    `${props.editUrl}/${props.entity}/${props.entityId}`,
    {
      body: {
        entity_id: props.entityId,
        project_id: props.projectId,
        note: note,
        entity: props.entity,
      },
    }
  );
  notes.unshift(data.value);
  console.log("notes",notes)
  editInput.value=[]
};
const deleteNote = (note: any) => {
  useAuthLazyFetchDelete(`${props.editUrl}/${note.note.uid}`, {});
  // If the tag exists, delete it
  if (note.index !== -1) {
    // To remove deleted tag
    notes.value.splice(note.index, 1);
  }
};
// Edit notes
const edit = (note: any) => {
  useAuthLazyFetchPut(`${props.editUrl}/${note.uid}?name=${note.note}`, {
    body: {
      project_id: props.projectId,
      note: note.note,
      entity: props.entity,
    },
  });
  notes.forEach((data: any) => {
    if (note.uid === data.uid) {
      // Changing the edited tag
      data.name = note.note;
    }
  });
};
</script>
