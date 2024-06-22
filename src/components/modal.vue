<template>
  <div class="modal">
    <div class="modal-content">
      <span class="close-button" @click="$emit('close')">&times;</span>
      <h2>{{ title }}</h2>
      <p>{{ body }}</p>
      <button @click="closeModal">La oss kjøre!</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "Modal",
  props: {
    title: {
      type: String,
      required: true,
    },
    body: {
      type: String,
      required: true,
    },
  },
  methods: {
    closeModal() {
      this.$emit("close");
    },
  },
};
</script>

<style scoped>
.modal {
  color: black;
  display: flex;
  justify-content: center;
  align-items: center;
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgba(0, 0, 0, 0.8);
}

.modal-content {
  background-color: #fefefe;
  margin: auto;
  padding: 20px;
  border: 1px solid #888;
  width: 80%;
  border: 1px solid black;
  border-radius: 0.75rem;
}
button {
  background-color: rgb(var(--accent-dark));
  color: white;
  padding: 14px 20px;
  margin: 8px 0;
  border: none;
  cursor: pointer;
  width: 100%;
  min-width: 9.5rem;
  border-radius: 0.25rem;
  padding: 0 1rem;
  height: 3rem;
}
.close-button {
  color: #aaaaaa;
  float: right;
  font-size: 28px;
  font-weight: bold;
}

.close-button:hover,
.close-button:focus {
  color: #000;
  text-decoration: none;
  cursor: pointer;
}
</style>
