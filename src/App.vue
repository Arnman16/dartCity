<template>
  <v-app>
    <!-- <v-app-bar height="20px" app color="#1C2833" dark>
      <v-btn text>New Game</v-btn>
    </v-app-bar> -->

    <v-main>
      <v-container fill-height fluid>
      <!-- <v-container fill-height fluid style="background-color: black"> -->
        <v-row align="center" justify="center">
          <v-card class="mb-10" flat width="100%">
            <Calculator msg="TEST" />
          </v-card>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import Calculator from "./components/Calculator";

export default {
  name: "App",

  components: {
    Calculator,
  },

  data: () => ({
    //
  }),
};
</script>
<style>
html {
  background-color: black;
  overflow: hidden;
}
</style>
