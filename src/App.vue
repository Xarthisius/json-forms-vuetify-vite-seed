<template>
  <v-app>
    <v-main>
      <json-forms :schema="jsonschema" :uischema="uischema" :renderers="renderers" :data="data"/>
    </v-main>
  </v-app>
</template>

<script setup lang="ts">
  import { JsonSchema7, uiTypeIs } from '@jsonforms/core';
import {JsonForms} from '@jsonforms/vue'
  import {vuetifyRenderers} from '@jsonforms/vue-vuetify'
  import CustomRendererComponent  from './components/CustomRenderer.vue'
  import { JsonFormsRendererRegistryEntry, rankWith, Tester} from "@jsonforms/core";

function buildRendererRegistryEntry(testRenderer: any, controlType: Tester) {
  const entry: JsonFormsRendererRegistryEntry = {
    renderer: testRenderer,
    tester: rankWith(3, controlType )
  };
  return entry
}

const customRendererEntry = buildRendererRegistryEntry(CustomRendererComponent, uiTypeIs('Custom'))

  const renderers = [ ...vuetifyRenderers, customRendererEntry ]
  const jsonschema: JsonSchema7 = {
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "properties": {
    "address": {
      "type": "object",
      "properties": {
        "street_address": {
          "type": "string"
        },
        "city": {
          "type": "string"
        },
        "state": {
          "type": "string"
        }
      },
      "required": [
        "street_address",
        "city",
        "state"
      ]
    },
    "user": {
      "type": "string",
    }
  }
}
const uischema = {
    "type": "VerticalLayout",
    "elements": [
        {
            "type": "Control",
            "scope": "#/properties/address"
        },
        {
            "type": "Custom",
            "scope": "#/properties/user"
        }
    ]
}

const data = {}
</script>
