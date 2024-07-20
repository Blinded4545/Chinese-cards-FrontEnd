<template>
  <v-app>
    <NuxtLayout>
      <NuxtPage></NuxtPage>
    </NuxtLayout>
  </v-app>
</template>

<style lang="scss">
::-webkit-scrollbar {
  display: none;
}

html, body, v-app {
  background-color: rgb(249, 250, 251);
  width: 100%;
  height: 99vh;
  min-height: 99vh;
  min-width: 100%;
}

.v-application__wrap{
  min-height: fit-content;
}

</style>
