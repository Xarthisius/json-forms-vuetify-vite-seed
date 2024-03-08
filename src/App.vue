<template>
  <h1 class="text-center">JSONForms Vue Vuetify Example</h1>
  <div class="myform">
    <json-forms :schema="jsonschema" :uischema="uischema" :renderers="renderers" :data="data" @change="onChange"
      @vue:mounted="onMounted" @onChange="onChange" />
    <v-btn color="primary" @click="saveForm" :disabled="isDisabled" type="submit">
      Save Form State
    </v-btn>
  </div>
</template>

<script setup lang="ts">

import { JsonSchema7, uiTypeIs } from '@jsonforms/core';
import { JsonForms, JsonFormsChangeEvent } from '@jsonforms/vue';
import { vuetifyRenderers } from '@jsonforms/vue-vuetify';
import CustomRendererComponent from './components/CustomRenderer.vue';
import { JsonFormsRendererRegistryEntry, rankWith, Tester } from "@jsonforms/core";
import { ref, computed, onMounted, watchEffect } from 'vue';


function buildRendererRegistryEntry(testRenderer: any, controlType: Tester) {
  const entry: JsonFormsRendererRegistryEntry = {
    renderer: testRenderer,
    tester: rankWith(3, controlType)
  };
  return entry;
}

const customRendererEntry = buildRendererRegistryEntry(CustomRendererComponent, uiTypeIs('Custom'));

// reactive state
const renderers = Object.freeze([...vuetifyRenderers, customRendererEntry]);
const jsonschema = ref<JsonSchema7>({});
const uischema = ref(undefined);
const formData = ref<JsonFormsChangeEvent>({ data: {}, errors: [] });
const data = ref({});

const isDisabled = computed(() => {
  if (!formData.value || !formData.value.errors) {
    return true;
  }
  const errors = formData.value.errors;
  return (errors && errors.length > 0) ? true : false;
});

watchEffect(async () => {
  const url = 'http://localhost:8000/form';
  jsonschema.value = await (await fetch(url)).json();

  const uiUrl = 'http://localhost:8000/ui';
  uischema.value = await (await fetch(uiUrl)).json();
});

function onChange(event: JsonFormsChangeEvent) {
  formData.value = { data: event.data, errors: event.errors };
  console.log('Button should be disabled: ', isDisabled.value);
}

function saveForm() {
  console.log('Save form state:', JSON.stringify(formData.value.data));
}
</script>