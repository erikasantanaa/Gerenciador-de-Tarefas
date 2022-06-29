<template>
  <div id="app">
    <div class="mx-5">
      <b-form class="mx-5 pt-5" @submit="onSubmit" @reset="onReset">
        <div class="row">
          <div class="col">
            <b-form-group
              id="input-group-1"
              label="Titulo da tarefa:"
              label-for="input-1"
            >
              <b-form-input
                v-model="form.task"
                type="text"
                autofocus
                id="input-1"
                maxlength="60"
                placeholder="Insira o titulo da tarefa"
                required
              ></b-form-input>
            </b-form-group>
          </div>
          <div class="col">
            <b-form-group
              id="input-group-2"
              label="Data da entrega:"
              label-for="input-2"
            >
              <b-form-datepicker
                id="input-2"
                v-model="form.deadline"
                class="mb-2"
              ></b-form-datepicker>
            </b-form-group>
          </div>
        </div>

        <div class="text-right">
          <b-button
            class="mr-2 my-2"
            size="sm"
            type="reset"
            variant="outline-danger"
            >Cancelar</b-button
          >
          <b-button
            :disabled="form.task.length === 0"
            class="ml-2 my-2"
            size="sm"
            type="submit"
            variant="primary"
            >Salvar</b-button
          >
        </div>
      </b-form>

      <div class="text-center mt-5" v-if="items.length === 0">
        <p class="h4 mb-2">
          Nenhuma tarefa encontrada. Adicione uma nova tarefa!
          <b-icon icon="exclamation-circle-fill"></b-icon>
        </p>
      </div>

      <div class="mx-5 mt-5" v-else>
        <b-table
          striped
          hover
          bordered
          head-variant="dark"
          tableVariant="light"
          :items="items"
          :fields="fields"
        >
          <template #cell(ações)="row">
            <b-button
              variant="danger"
              pill
              size="sm"
              class="mr-2"
              @click="openDeleteModal(row)"
            >
              Deletar
            </b-button>

            <b-button
              variant="info"
              pill
              size="sm"
              class="mr-2"
              @click="openUpdateModal(row)"
            >
              Editar
            </b-button>
          </template>
        </b-table>
      </div>

      <b-modal v-model="deleteModalShow" title="Deletar tarefa">{{
        deleteModaltext
      }}</b-modal>

      <b-modal
        size="lg"
        v-model="updateModalShow"
        :title="`Editar tarefa: ${taskTitleEdit}`"
      >
        <div class="row">
          <div class="col">
            <b-form-group
              id="input-group-1"
              label="Titulo da tarefa:"
              label-for="input-1"
            >
              <b-form-input
                v-model="form.task"
                type="text"
                autofocus
                id="input-1"
                maxlength="60"
                placeholder="Insira o titulo da tarefa"
                required
              ></b-form-input>
            </b-form-group>
          </div>
          <div class="col">
            <b-form-group
              id="input-group-2"
              label="Data da entrega:"
              label-for="input-2"
            >
              <b-form-datepicker
                id="input-2"
                v-model="form.deadline"
                class="mb-2"
              ></b-form-datepicker>
            </b-form-group>
          </div>
        </div>
      </b-modal>
    </div>
  </div>
</template>

<script>
import Axios from "axios";
export default {
  name: "App",
  components: {},
  data() {
    return {
      items: [
        // { tarefa: "Prova de matematica", data_de_entrega: "2022-06-16" },
        // { tarefa: "Prova de portugues", data_de_entrega: "2022-06-13" },
        // { tarefa: "Prova de quimica", data_de_entrega: "2022-06-10" },
        // { tarefa: "Prova de fisica", data_de_entrega: "2022-05-17" },
      ],
      fields: [
        {
          key: "tarefa",
          sortable: false,
        },
        {
          key: "data_de_entrega",
          sortable: true,
        },
        {
          key: "ações",
          sortable: false,
        },
      ],
      form: {
        task: "",
        deadline: "2022-06-16",
      },
      deleteModalShow: false,
      updateModalShow: false,
      deleteModaltext: "",
      taskTitleEdit: "",
      url: "http://localhost:3000/api/",
    };
  },
  created() {
    this.getAllData();
  },
  methods: {
    getAllData() {
      Axios.get(`${this.url}tarefas/`).then((response) => {
        const tableData = response.data.result;

        tableData.forEach((item) => {
          const data = {
            id: item.codigo,
            tarefa: item.descricao,
            data_de_entrega: item.dataEntrega,
          };

          this.items.push(data);
        });
      });
    },
    onSubmit(event) {
      event.preventDefault();

      // const newTask = {
      //   tituloTarefa: this.form.task,
      //   dataEntrega: "17/06/2022",
      // };

      console.log("newTask", {
        tituloTarefa: this.form.task,
        dataEntrega: "17/06/2022",
      });

      Axios.post(`${this.url}tarefas`, {
        tituloTarefa: this.form.task,
        dataEntrega: "17/06/2022",
      }).then((response) => {
        console.log(response);
        console.log("deu bom", response);
      });
    },
    onReset(event) {
      event.preventDefault();
      this.form.email = "";
      this.form.name = "2022-06-16";
    },
    openDeleteModal(row) {
      this.deleteModalShow = !this.deleteModalShow;
      this.deleteModaltext = `Deseja realmente deletar a tarefa ${row.item.tarefa}?`;
      Axios.delete(`${this.url}tarefas/${row.item.id}`).then((response) => {
        if (response.statusText === "OK") {
          console.log("deletou");
        }
      });
    },
    openUpdateModal(row) {
      console.log(row.item);
      this.taskTitleEdit = row.item.tarefa;
      this.updateModalShow = !this.updateModalShow;
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* text-align: center; */
  color: #2c3e50;
  height: 100vh;
  background-color: fafafa;
}
</style>