<template>
  <teleport to="#app">
    <dialog open>
      <div class="dialog-inner">
        <header class="dialog-header">
          <h3>{{ props.modalTitle }}</h3>
        </header>
        <section class="dialog-body">
          <slot></slot>
        </section>
        <button @click="closeModal">
          <Close class="close-modal-btn" />
        </button>
        <footer class="dialog-footer">
          <div>
            <button class="secondary" @click="closeModal"></button>
          </div>
          <button class="primary" @click="onSubmitForm">
            {{ props.btnLabel }}
          </button>
        </footer>
      </div>
    </dialog>
  </teleport>
</template>

<script setup>
import Close from "/src/assets/Close.vue";
const props = defineProps(["modalTitle", "btnLabel"]);
const emits = defineEmits(["closeModal", "submitForm"]);
const closeModal = () => {
  emits("closeModal");
};
const onSubmitForm = () => {
  emits("submitForm");
};
</script>
