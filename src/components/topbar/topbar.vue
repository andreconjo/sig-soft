<template>
  <div class="top-bar">
    <h2 class="title">SIG Soft</h2>

    <div class="actions">
      <button class="upload-button" @click="$emit('import')">
        <img class="icon" src="../../assets/images/tray-arrow-up.svg" alt="upload">
        Importar Ligação
      </button>
    </div>
  </div>
</template>

<script>

export default {
  name: "topbar"
}
</script>

<style lang="css" scoped>
.top-bar {
  background: #468499;
  padding: 0 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 56px;
}

.top-bar .title {
  text-align: left;
  color: white;
}

.top-bar h2 {
  margin: 0;
}

.upload-button {
  height: 30px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.icon {
  height: 15px;
  width: 15px;
  margin: 0 5px;
}
</style>
