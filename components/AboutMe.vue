<template>
  <div class="about-me">
    <h1>MiniBlog</h1>
    <p>MiniBlog es un pequeño sistema de blog personal con NuxtJS y Airtable</p>
  </div>
</template>

<style lang="scss" scoped>
.about-me {
  @apply w-full flex flex-col justify-center items-center px-4 py-16 bg-gray-200;

  h1 {
    @apply font-black text-4xl text-gray-900 pb-2;
  }

  p {
    @apply text-lg py-2;
  }
}
</style>
