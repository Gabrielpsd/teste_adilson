<template>
    <div>
      <el-input
        v-model="texto"
        :maxlength="maxLengh"
        :placeholder="Label"
        show-word-limit
        clearable
        :rows="4"
        type="textarea"
        @input="onInput"
        :size="Size"
        style="width: 500px;"
      />
    </div>
  </template>
  
  <script>
  
  export default {
    props: {
      modelValue: {
        type: String,
      },
      maxLengh: {
        type: Number,
        default: 255,
      },
      Label: {
        type: [Text,String],
        default: "Descrição"
      },
      Size: {
        type: String,
        default: 'large'
      },
      disable: {
        type: Boolean,
        default: false,
        },
    },
    data() {
      return {
        texto : this.modelValue
      };
    },
    methods: {
      onInput(event) {
        this.$emit('update:modelValue', this.texto);
      },
    },
  };
  </script>
  
  <style scoped>
  el-input{
    width: 100%;
    max-width: 240px;
  }
  </style>
  