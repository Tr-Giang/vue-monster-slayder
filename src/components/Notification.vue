<template>
  <div class="modal">
    <div class="modal-dialog modal-dialog-centered">
      <div class="modal-content">
        <div class="modal-header d-flex justify-content-center">
          <h5 class="modal-title" id="examplemodalLabel">Game Over</h5>
        </div>
        <div class="modal-body">
          <p v-if="winner == 'Player'">You Win</p>
          <p v-else-if="winner == 'Monster'">You Lose</p>
          <p v-else>Draw</p>
        </div>
        <div class="modal-footer">
          <button @click="onstart" data-bs-dismiss="modal">Play again</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  methods: {
    onstart() {
      this.$emit('start');
    },
  },
};
</script>

<style scope>
.modal {
  background-color: rgb(0, 0, 0, 0.6);
  display: block;
  position: fixed;
  top: 0;
  left: 0;
  z-index: 1060;
  width: 100%;
  height: 100%;
  overflow-x: hidden;
  overflow-y: auto;
  outline: 0;
}

.modal-body {
  font-size: 24px;
}
</style>
