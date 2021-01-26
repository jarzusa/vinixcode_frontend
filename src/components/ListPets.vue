<template>
  <div class="row">
    <!-- @click="showTable = !showTable" -->
    <button
      v-show="showTable"
      class="btn btn-primary"
      @click="(e) => showAddPets()"
    >
      <b-icon-plus-square-fill></b-icon-plus-square-fill>
      Agregar
    </button>

    <!-- Agregar nueva mascota -->
    <div v-if="showTable == false && viewPetOne == false">
      <h3>Guardar Mascota</h3>
      <div class="row">
        <div class="input-field col s4">
          <input
            id="name"
            type="text"
            class="validate"
            v-model="name"
            placeholder="Nombre"
          />
        </div>
        <div class="input-field col s4">
          <input
            id="status"
            type="text"
            class="validate"
            v-model="status"
            placeholder="Estado"
          />
        </div>
        <div class="input-field col s4">
          <input
            id="photoUrls"
            type="text"
            class="validate"
            v-model="photoUrls"
            placeholder="URL Photo"
          />
        </div>
      </div>

      <div class="row">
        <div class="input-field col s6">
          <multiselect
            v-model="tags"
            @tag="(val) => addTag(val)"
            :options="optionsTag"
            :multiple="true"
            :close-on-select="false"
            :clear-on-select="false"
            :preserve-search="true"
            placeholder="Etiquetas"
            label="name"
            track-by="name"
            :taggable="true"
            :preselect-first="true"
          >
          </multiselect>
        </div>
        <div class="input-field col s6">
          <multiselect
            v-model="category"
            :options="optionsCategory"
            :close-on-select="false"
            :searchable="false"
            :clear-on-select="false"
            :preserve-search="true"
            placeholder="Categoria"
            label="name"
            track-by="name"
            :taggable="true"
            :preselect-first="true"
          >
          </multiselect>
        </div>
      </div>
      <button
        class="btn blue waves-effect waves-light btn-small"
        @click="savePet()"
      >
        Guardar
      </button>
      <button
        class="btn red waves-effect waves-light btn-small"
        @click="showTable = !showTable"
      >
        Cancelar
      </button>
    </div>

    <!-- Listado de mascotas  -->
    <table id="tableListPet" class="striped" v-show="showTable">
      <thead>
        <tr>
          <th>#</th>
          <th>ID</th>
          <th>Nombre</th>
          <th>Categoria</th>
          <th>Estado</th>
          <th>Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(p, i) in info" :key="i">
          <td>{{ i + 1 }}</td>
          <td>{{ p.id }}</td>
          <td>{{ p.name ? p.name : "" }}</td>
          <td>{{ p.category ? p.category.name : "" }}</td>
          <td>{{ p.status ? p.status : "" }}</td>
          <td>
            <button
              class="btn blue waves-effect waves-light btn-small"
              @click="(e) => viewPet(p)"
            >
              <b-icon-eye-fill></b-icon-eye-fill>
            </button>
            <button
              class="btn red waves-effect waves-light btn-small"
              @click="(e) => deletingPet(p)"
            >
              <b-icon-trash-fill></b-icon-trash-fill>
            </button>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Ver mascota individual -->
    <div v-if="viewPetOne == true && showTable == false">
      <img v-bind:src="photoUrls" alt="" />
      <div class="row">
        <div class="input-field col s4">
          <input
            id="name"
            type="text"
            class="validate"
            v-model="name"
            placeholder="Nombre"
          />
        </div>
        <div class="input-field col s4">
          <input
            id="status"
            type="text"
            class="validate"
            v-model="status"
            placeholder="Estado"
          />
        </div>
        <div class="input-field col s4">
          <input
            id="photoUrls"
            type="text"
            class="validate"
            v-model="photoUrls"
            placeholder="URL Photo"
          />
        </div>
      </div>

      <div class="row">
        <div class="input-field col s6">
          <multiselect
            v-model="tags"
            @tag="(val) => addTag(val)"
            :options="optionsTag"
            :multiple="true"
            :close-on-select="false"
            :clear-on-select="false"
            :preserve-search="true"
            placeholder="Etiquetas"
            label="name"
            track-by="name"
            :taggable="true"
            :preselect-first="true"
          >
          </multiselect>
        </div>
        <div class="input-field col s6">
          <multiselect
            v-model="category"
            :options="optionsCategory"
            :close-on-select="false"
            :searchable="false"
            :clear-on-select="false"
            :preserve-search="true"
            placeholder="Categoria"
            label="name"
            track-by="name"
            :taggable="true"
            :preselect-first="true"
          >
          </multiselect>
        </div>
      </div>
      <button
        class="btn red waves-effect waves-light btn-small"
        @click="(e) => showListPets()"
      >
        Atras
      </button>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Multiselect from "vue-multiselect";
// import ModalAdd from "./ModalAdd";
export default {
  name: "ListPets",
  components: {
    // ModalAdd,
    Multiselect,
  },
  data: () => ({
    name: "",
    status: "",
    photoUrls: "",
    showTable: true,
    viewPetOne: false,
    info: null,
    tags: [],
    optionsTag: [],
    category: [],
    optionsCategory: [],
  }),
  mounted() {
    this.getListPets();
    this.getListCategory();
  },
  methods: {
    /**
     * Listar mascotas
     */
    getListPets() {
      axios.get("http://127.0.0.1:8000/pet/findAllPets").then((res) => {
        if (res.data.status === 200) {
          this.info = res.data.data;
        } else {
          this.info = [];
        }
        console.log(res);
      });
    },

    /**
     * Listar Categorias para select
     */
    getListCategory() {
      axios
        .get("http://127.0.0.1:8000/category/findAllCategory")
        .then((res) => {
          if (res.data.status === 200) {
            this.optionsCategory = res.data.data;
          } else {
            this.optionsCategory = [];
          }
          console.log(res);
        });
    },

    /**
     * Borrar mascota
     */
    deletingPet(row) {
      axios
        .delete("http://127.0.0.1:8000/pet/" + row.id, {
          headers: {
            "Content-Type": "application/json",
            Authorization: "549c210698e150e9e40a98107b7ea5c1",
          },
          crossDomain: true,
        })
        .then((res) => {
          alert(res.data.message);
        })
        .catch((e) => {
          alert(e);
          console.log(e);
        });
    },

    /**
     * Ver info de mascota
     */
    viewPet(row) {
      // alert("Viendo");
      this.name = row.name;
      this.status = row.status;
      if (row.photoUrls != "" && row.photoUrls != null && row.photoUrls != undefined) {
        this.photoUrls = row.photoUrls;
      } else {
        this.photoUrls = "https://secure.gravatar.com/avatar/86508147669fbb97dd4926dead197649?s=256&d=mm&r=pg"
      }
      this.tags = row.tags;
      this.category = row.category;
      this.viewPetOne = true;
      this.showTable = false;
    },

    /**
     * Ver info de mascota
     */
    showAddPets() {
      // alert("Viendo");
      this.getListCategory();
      this.name = "";
      this.status = "";
      this.photoUrls = "";
      this.tags = [];

      this.viewPetOne = false;
      this.showTable = !this.showTable;
    },

    /**
     * Regresar al listado de mascotas
     */
    showListPets() {
      this.viewPetOne = false;
      this.showTable = true;
    },

    /**
     * Agregar tags
     */
    addTag(newTag) {
      const tag = {
        name: newTag,
        id: newTag.substring(0, 2) + Math.floor(Math.random() * 10000000),
      };
      this.optionsTag.push(tag);
      this.tags.push(tag);
    },

    /**
     * Guardar nueva mascota
     */
    savePet() {
      let data = {
        name: this.name,
        status: this.status,
        tags: this.tags,
        photoUrls: this.photoUrls,
        category: this.category,
      };

      axios
        .post("http://127.0.0.1:8000/pet", data)
        .then((res) => {
          alert(res.data.message);
          if (res.data.status === 200) {
            this.showTable = true;
            // window.location.reload();
          }
        })
        .catch((e) => {
          alert(e);
          console.log(e);
        });
    },
  },
};
</script>

<style scoped>
.row {
  margin: 5%;
}
button {
  margin: 1%;
  border-radius: 76px;
}
img {
  max-width: 60px;
  max-height: 60px;
  border:1px solid red;
}
</style>

<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>