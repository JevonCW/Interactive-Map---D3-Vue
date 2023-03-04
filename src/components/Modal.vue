<template>
    <div class="modal" v-if="selectedCountry">
      <div class="modal-content">
        <span class="close" @click="closeModal">close</span>
        <h2>{{ selectedCountry }}</h2>
        <p>You clicked on {{ selectedCountry }}!</p>
      </div>
    </div>
  </template>
  
  <script lang="ts">
  import { defineComponent } from 'vue'
  
  export default defineComponent({
    name: 'Modal',
    props: {
        selectedCountry: {
        type: String,
        required: true
      }
    },
    methods: {
      closeModal() {
        this.$emit('close')
      }
    }
  })
  </script>
  <style>
  .modal {
    display: block;
    position: fixed;
    z-index: 1;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    overflow: auto;
    background-color: rgba(0,0,0,0.8);
  }
  .modal-content {
    margin: 10% auto;
    padding: 20px;
    border: 1px solid #888;
    width: 80%;
    max-width: 600px;
    background-color: white;
    position: relative;
  }
  .close {
    position: absolute;
    top: 0;
    right: 0;
    color: red;
    font-size: 28px;
    font-weight: bold;
    padding: 12px 16px;
    cursor: pointer;
  }
  .close:hover,
  .close:focus {
    color: black;
    text-decoration: none;
    cursor: pointer;
  }
  </style>