<template>
  <b-container class="hello">
    <h1>Busca los recursos que necesites</h1>
    <small>No le busques sentido al taco</small>
    <b-row>
      <b-input-group>
        <b-input-group-text slot="prepend">
          <strong class="text-danger">🌮</strong>
        </b-input-group-text>
        <b-form-input v-model="searching"></b-form-input>
      </b-input-group>
    </b-row>

    <b-row>
      <b-card
        v-for="card in results"
        :title="card.title || 'Título del curso'"
        :img-src="card.image || 'https://placehold.it/100x100'"
        img-top
        tag="article"
        style="max-width: 20rem;"
        class="mb-2"
      >
        <b-button :href="card.href" variant="primary">Ver curso</b-button>
      </b-card>
    </b-row>
  </b-container>
</template>

<script>
export default {
  name: "Home",
  data() {
    return {
      typeOfResources: ["Libros", "Papers", "Proyectos"],
      searching: "",
      allData: []
    };
  },
  computed: {
    results() {
      return ["resultado"];
    }
  },
  created() {
    console.log("hi");
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
