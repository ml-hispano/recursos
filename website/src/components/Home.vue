<template>
  <b-container class="hello">
    <h3>Busca los recursos que necesites</h3>
    <small>No le busques sentido al taco</small>
    <b-row class="mb-3">
      <b-input-group>
        <b-input-group-text slot="prepend">
          <strong class="text-danger">🌮</strong>
        </b-input-group-text>
        <b-form-input v-model="searching"></b-form-input>
      </b-input-group>
    </b-row>

    <b-row class="mb-4">
      <b-button
        class="mr-2"
        :variant="isSelected(type)"
        @click="selectType(type)"
        :key="type"
        v-for="type in typeOfResources"
      >{{type}}</b-button>
    </b-row>

    <b-row>
      <b-card
        v-for="card in results"
        :title="card.title || 'Título del curso'"
        :img-src="card.image || 'https://placehold.it/100x100'"
        img-top
        tag="article"
        style="max-width: 20rem;"
        class="mb-2 mr-4"
        :key="card.title"
      >
        <b-button :href="card.href" variant="primary">Ver curso</b-button>
      </b-card>
    </b-row>
  </b-container>
</template>

<script>
const _GITHUB_URL = "https://api.github.com";
const _GITHUB_URL_RAW_CONTENT = "https://raw.githubusercontent.com";
// TODO: Cambiar esto a master cuando se termine
const branch = 'develop';
export default {
  name: "Home",
  data() {
    return {
      typeOfResources: ["Libros", "Papers", "Proyectos"],
      secondTypeOfResources: [[], [], ["Tensorflow/ProyectosTensorflow"]],
      searching: "",
      allData: [],
      typesSelected: ["Libros"]
    };
  },
  // TODO: Hay un puto problema con la reactividad del results ...
  computed: {
    results() {
      let selected = {};

      if (this.typesSelected.length === 0) {
        selected = this.allData;
      } else {
        Object.keys(this.allData).forEach(key => {
          if (this.typesSelected.includes(key)) {
            selected[key] = this.allData[key];
          }
        });
      }
      const flatten = [].concat.apply([], Object.values(selected));
      return flatten.filter(resource =>
        resource.title.toLowerCase().includes(this.searching.toLowerCase())
      );
    }
  },
  methods: {
    isSelected(type) {
      return this.typesSelected.includes(type)
        ? "secondary"
        : "outline-secondary";
    },
    extractInfo(rawData) {
      console.log(rawData);
      // TODO: Coger este rawData y sacar la info de aquí. Generar el array limpio de objetos
      return [
        {
          categories: ["Fácil", "ML"],
          title: "Curso maravilloso ",
          href: "https://google.com"
        }
      ];
    },
    selectType(type) {
      if (this.typesSelected.includes(type)) {
        this.typesSelected = this.typesSelected.filter(t => t != type);
      } else {
        this.typesSelected.push(type);
      }
    },
    extractAllDataFromRepo() {
      this.typeOfResources.forEach((type, idx) => {
        if (this.secondTypeOfResources[idx].length > 0) {
          this.secondTypeOfResources[idx].forEach(subType => {
            this.axios
              .get(
                `${_GITHUB_URL_RAW_CONTENT}/ml-hispano/recursos-ml/${branch}/${type}/${subType}.md`
              )
              .then(({ data }) => this.extractInfo(data))
              .then(cleanResources => (this.allData[type] = cleanResources));
          });
        } else {
          this.axios
            .get(
              `${_GITHUB_URL_RAW_CONTENT}/ml-hispano/recursos-ml/${branch}/${type}/${type}.md`
            )
            .then(({ data }) => this.extractInfo(data))
            .then(cleanResources => (this.allData[type] = cleanResources));
        }
      });
    }
  },
  beforeMount() {
    this.extractAllDataFromRepo();
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
