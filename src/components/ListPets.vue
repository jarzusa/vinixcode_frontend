<template>
  <div class="row">
    <!-- <h3>Guardar Mascota</h3>
    <form class="col s12" action @submit.prevent="pet">
      <div class="row">
        <div class="input-field col s6">
          <input id="name" type="text" class="validate" v-model="name" />
          <label for="name">Nombre</label>
        </div>
        <div class="input-field col s6">
          <input
            id="last_name"
            type="text"
            class="validate"
            v-model="last_name"
          />
          <label for="last_name">Estado</label>
        </div>
        <div class="col s6">
          <select class="browser-default">
            <option value="" disabled selected>Estado</option>
            <option value="1">Option 1</option>
            <option value="2">Option 2</option>
            <option value="3">Option 3</option>
          </select>
        </div>
      </div> -->
    <!-- <div class="row">
        <div class="input-field col s12">
          <input
            disabled
            value="I am not editable"
            id="disabled"
            type="text"
            class="validate"
          />
          <label for="disabled">Disabled</label>
        </div>
      </div>
      <div class="row">
        <div class="input-field col s12">
          <input id="password" type="password" class="validate" />
          <label for="password">Password</label>
        </div>
      </div>
      <div class="row">
        <div class="input-field col s12">
          <input id="email" type="email" class="validate" />
          <label for="email">Email</label>
        </div>
      </div>
      <div class="row">
        <div class="col s12">
          This is an inline input field:
          <div class="input-field inline">
            <input id="email_inline" type="email" class="validate" />
            <label for="email_inline">Email</label>
            <span class="helper-text" data-error="wrong" data-success="right"
              >Helper text</span
            >
          </div>
        </div>
      </div> -->
    <!-- </form>
  </div> -->
    <!-- <modal-add></modal-add> -->
    <button class="btn btn-primary" @click="showTable = !showTable">
      Agregar
    </button>
    <div v-if="showTable == false">
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
        <div class="input-field col s4">
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
        <div class="input-field col s4">
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
    </div>
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
              @click="(e) => viewPet()"
            >
              <b-icon-eye-fill></b-icon-eye-fill>
            </button>
            <button
              class="btn orange waves-effect waves-light btn-small"
              @click="(e) => editPet()"
            >
              <b-icon-pencil-fill></b-icon-pencil-fill>
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
    info: null,
    tags: [],
    optionsTag: [],
    category: [],
    optionsCategory: []
  }),
  mounted() {},
  methods: {
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

    getListCategory() {
      axios.get("http://127.0.0.1:8000/category/findAllCategory").then((res) => {
        if (res.data.status === 200) {
          this.optionsCategory = res.data.data;
        } else {
          this.optionsCategory = [];
        }
        console.log(res);
      });
    },

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

    editPet() {
      alert("Editando");
    },

    viewPet() {
      alert("Viendo");
    },

    addTag(newTag) {
      const tag = {
        name: newTag,
        id: newTag.substring(0, 2) + Math.floor(Math.random() * 10000000),
      };
      this.optionsTag.push(tag);
      this.tags.push(tag);
    },

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
</style>

<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>