<template>
  <div class="flex justify-center w-full min-h-screen py-10 bg-surface-ground">
    <Card class="w-full max-w-6xl mx-20">
      <template #title>
        <div class="flex items-center justify-between h-24">
          <span class="text-2xl">Survey Creator for Edurio</span>

          <!-- Add question -->
          <Button
            v-if="editingMode"
            @click="addQuestion"
            class="my-5"
            icon="pi pi-plus"
            label="Add Question"
          />
        </div>
      </template>

      <template #subtitle>
        <div class="flex items-center justify-between">
          <span>By Ansis Sprugevics</span>

          <!-- Toggle Editing -->
          <div class="flex items-center gap-x-2">
            <InputSwitch v-model="editingMode" inputId="editingMode" />
            <label class="cursor-pointer" for="editingMode">Edit Mode</label>
          </div>
        </div>
      </template>

      <template #content>
        <Draggable
          class="flex flex-col gap-y-5"
          v-model="savedState"
          group="questions"
          handle=".handle"
          :animation="300"
          ghostClass="opacity-0"
          :forceFallback="true"
          fallbackClass="!opacity-100"
          itemKey="id"
        >
          <template #item="{ element }: { element: Question }">
            <Panel>
              <template #header>
                <div
                  class="flex items-center justify-between w-full h-10 gap-x-7"
                >
                  <!-- Question text -->
                  <InputText
                    class="w-2/3"
                    v-if="editingMode"
                    v-model="element.question"
                    placeholder="Enter a question"
                  />
                  <div v-else>
                    <span v-if="element.question !== ''" class="ml-3 font-bold">
                      {{ element.question }}
                    </span>
                    <span v-else class="ml-3 italic font-bold"
                      >Empty Question</span
                    >
                  </div>

                  <!-- Question type selection -->
                  <SelectButton
                    v-if="editingMode"
                    :pt="{ label: { class: 'text-sm' } }"
                    v-model="element.type"
                    optionLabel="label"
                    optionValue="value"
                    :allowEmpty="false"
                    :options="[
                      { label: 'Text', value: 'text' },
                      { label: 'Options', value: 'options' },
                    ]"
                  />
                </div>
              </template>

              <template #icons>
                <div v-if="editingMode" class="flex items-center ml-5 gap-x-3">
                  <!-- Delete question -->
                  <Button
                    class="cursor-pointer"
                    rounded
                    text
                    severity="danger"
                    icon="pi pi-trash"
                    @click="deleteQuestion(element)"
                  />
                  <!-- Reorder question -->
                  <span class="cursor-pointer handle pi pi-bars"></span>
                </div>
              </template>
              <div v-if="element.type === 'text'">
                <!-- Question response text area -->
                <Textarea
                  v-model="element.responseText"
                  :disabled="editingMode"
                  placeholder="Your response here"
                  class="w-full h-12 resize-none"
                  autoResize
                />
              </div>
              <div
                class="flex flex-col gap-y-2"
                v-if="element.type === 'options'"
              >
                <!-- Question response options -->
                <div v-for="rank in 5">
                  <RadioButton
                    :disabled="editingMode"
                    v-model="element.responseRank"
                    :inputId="`${element.id}-${rank}`"
                    name="dynamic"
                    :value="rank"
                  />
                  <!-- Label with RAG scale -->
                  <label
                    :for="`${element.id}-${rank}`"
                    class="ml-2 cursor-pointer"
                    :style="{
                      color: `rgb(${(255 * rank * 20) / 100}, ${
                        (255 * (100 - rank * 20)) / 100
                      }, 0)`,
                    }"
                    >{{ scale[rank] }}</label
                  >
                </div>
              </div>
            </Panel>
          </template>
        </Draggable>
      </template>
    </Card>
  </div>
</template>

<script setup lang="ts">
import { useLocalStorage } from "@vueuse/core";
import Draggable from "vuedraggable";
import Card from "primevue/card";
import Panel from "primevue/panel";
import Button from "primevue/button";
import InputText from "primevue/inputtext";
import InputSwitch from "primevue/inputswitch";
import Textarea from "primevue/textarea";
import RadioButton from "primevue/radiobutton";
import { Ref } from "vue";
import SelectButton from "primevue/selectbutton";

type Scale = {
  [key: number]: string;
};

const scale: Scale = {
  1: "Very often",
  2: "Quite often",
  3: "Sometimes",
  4: "Rarely",
  5: "Never",
};

type Question = {
  id: number;
  question: string;
  type: "text" | "options";
  responseText: string | undefined;
  responseRank: number | undefined;
};

const editingMode: Ref<boolean> = useLocalStorage("editingMode", false);
const savedState: Ref<any[]> = useLocalStorage("edurio-survey", []);

// Calculate current active id
let id = Math.max(...savedState.value.map((q) => q.id)) + 1;

function addQuestion() {
  savedState.value.push({ id: id++, question: "", type: "text" });
}

function deleteQuestion(question: Question) {
  savedState.value = savedState.value.filter((q) => q.id !== question.id);
}
</script>
