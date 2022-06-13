<template>
  <Transition name="modal">
    <div v-if="show" class="modal-mask">
      <div class="modal-wrapper">
        <div class="modal-container">
          <div class="modal-header">
            <slot name="header">default header</slot>
          </div>
          <div class="modal-footer">
            <button class="btn btn-danger" @click="$emit('close')">
                Reconnect
            </button>
          </div>
        </div>
      </div>
    </div>
  </Transition>
</template>


<script setup>
import { defineProps } from "vue";

defineProps({
  show: Boolean
});
</script>

<style scoped>
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: table;
  transition: opacity 0.5s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}

.modal-container {
  width: 400px;
  margin: 0px auto;
  padding: 10px 15px;
  background-color: var(--blue);
  color: var(--light);
  border-radius: 5px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.5s ease;
}

.modal-header h5 {
  color: var(--light);
  margin-top: 0;
}

.modal-body {
  background-color: var(--blue);
  margin: 20px 0;
}

.modal-default-button {
  float: right;
}

.modal-enter-from {
  opacity: 1;
}

.modal-leave-to {
  opacity: 0;
}

.modal-enter-from .modal-container,
.modal-leave-to .modal-container {
  -webkit-transform: scale(1.3);
  transform: scale(1.3);
}
</style>