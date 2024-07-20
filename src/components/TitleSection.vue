<template>
  <div id="titleContainer">
    <img
      src="../assets/GasMask_by_MugenB16.png"
      alt="Gas Mask Icon"
      id="appLogo"
    />
    <h1 id="appTitle">WASTOIDS!</h1>
  </div>
</template>

<script>
export default {
  name: "TitleSection",
};
</script>

<style scoped>
#titleContainer {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 1em;
  flex-wrap: wrap;
  flex-shrink: 0;
}

#appTitle {
  font-size: xx-large;
  font-weight: bolder;
  color: #be4537;
}

@media (max-width: 600px) {
  #appLogo {
    width: 20%;
  }
}
</style>
