<template>
  <div class="slidev-layout default">
    <slot />
  </div>
</template>

<style lang="css">
:global {
  @keyframes movement {
    0%, 100% {
      background-size:
        100vmax 100vmax,
        90vmax 90vmax,
        100vmax 100vmax,
        90vmax 90vmax,
        60vmax 60vmax;
      background-position:
        -60vmax -90vmax,
        50vmax -40vmax,
        0vmax -20vmax,
        -40vmax -20vmax,
        40vmax 60vmax;
    }
  }
}


.slidev-layout.default {
  background-color: #e493d0 !important;
  background-image: 
    radial-gradient(closest-side, rgba(235, 105, 78, 1), rgba(235, 105, 78, 0)), 
    radial-gradient(closest-side, rgba(243, 11, 164, 1), rgba(243, 11, 164, 0)), 
    radial-gradient(closest-side, rgba(254, 234, 131, 1), rgba(254, 234, 131, 0)), 
    radial-gradient(closest-side, rgba(170, 142, 245, 1), rgba(170, 142, 245, 0)), 
    radial-gradient(closest-side, rgba(248, 192, 147, 1), rgba(248, 192, 147, 0));
  background-size:
    130vmax 130vmax,
    80vmax 80vmax,
    90vmax 90vmax,
    110vmax 110vmax,
    90vmax 90vmax !important;
  background-position:
    -80vmax -80vmax,
    60vmax -30vmax,
    10vmax 10vmax,
    -30vmax -10vmax,
    50vmax 50vmax !important;
  animation: movement 3s linear infinite;
}

</style>