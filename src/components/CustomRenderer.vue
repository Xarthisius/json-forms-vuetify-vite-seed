<template>
    <h1>Custom Renderer</h1>
  </template>
  
  <script lang="ts">
  import {
    JsonFormsRendererRegistryEntry,
    rankWith,
    uiTypeIs,
  } from '@jsonforms/core';
  import {} from '@jsonforms/vue';
  import { defineComponent } from 'vue';
  
  const spacerRenderer = defineComponent({
    name: 'CustomRenderer',
  });
  
  export default spacerRenderer;
  
  export const entry: JsonFormsRendererRegistryEntry = {
    renderer: spacerRenderer,
    tester: rankWith(1, uiTypeIs('Custom')),
  };
  </script>
  