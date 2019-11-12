<template>
  <v-card>
    <v-text-field
      type="text"
      label="Title"
      v-model="cta.buttonText"
    ></v-text-field>
    <v-text-field type="text" label="Link or Email Address" v-model="cta.link">
    </v-text-field>
    <v-textarea label="Description" v-model="cta.description"> </v-textarea>
  </v-card>
</template>

<script>
export default {
  name: "CTAInput",
  model: {
    prop: "cta",
    event: "change"
  },
  props: {
    cta: {
      type: Object
    }
  }
};
</script>

<style scoped></style>
