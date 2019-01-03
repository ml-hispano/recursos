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

    <b-row>
      <b-button class="mr-2" :variant="isSelected(type)" :key="type" v-for="type in typeOfResources">{{type}}</b-button>
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
  computed: {
    results() {
      if (this.typesSelected.length === 0){
        return Object.values(this.allData)
      }else{

        return [];
      }
    }
  },
  methods: {
    isSelected(type) {
      return this.typesSelected.includes(type)
        ? "secondary"
        : "outline-secondary";
    },
    extractInfo(rawData){

      return [ { categories : ['Fácil', 'ML'] , title : 'Curso maravilloso', href :"https://google.com" }]
    },
    extractAllDataFromRepo() {
      this.typeOfResources.forEach((type, idx) => {
        if (this.secondTypeOfResources[idx].length > 0) {
          this.secondTypeOfResources[idx].forEach(subType => {
            this.axios
              .get(
                `${_GITHUB_URL_RAW_CONTENT}/ml-hispano/recursos-ml/master/${type}/${subType}.md`
              )
              .then(res => {
                console.log({ data: res.data });

                // Ahora aquí hay que coger todo ese "raw data" que es una string y sacarla como array
                const cleanResources = this.extractInfo(res.data);
                this.allData[subType] = cleanResources;
              });
          });
        } else {
          this.axios
            .get(
              `${_GITHUB_URL_RAW_CONTENT}/ml-hispano/recursos-ml/master/${type}/${type}.md`
            )
            .then(res => {
              console.log({ data: res.data });

              // Ahora aquí hay que coger todo ese "raw data" que es una string y sacarla como array
              const cleanResources = this.extractInfo(res.data);
              this.allData[type] = cleanResources;
            });
        }
      });
    }
  },
  created() {
    this.extractAllDataFromRepo();
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
