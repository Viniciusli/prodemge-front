<template>
  <v-data-table :headers="headers" :items="desserts" :sort-by="[{ key: 'calories', order: 'asc' }]">
    <template v-slot:top>
      <v-toolbar flat>
        <v-toolbar-title>Pessoas</v-toolbar-title>
        <v-divider class="mx-4" inset vertical></v-divider>
        <v-spacer></v-spacer>
        <v-dialog v-model="dialog" max-width="800px">
          <template v-slot:activator="{ props }">
            <v-btn class="mb-2" color="primary" dark v-bind="props">
              Nova Pessoa
            </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="text-h5">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12" md="6" sm="6">
                    <v-text-field v-model="editedItem.nome" label="Nome"></v-text-field>
                  </v-col>
                  <v-col cols="12" md="6" sm="6">
                    <v-text-field v-model="editedItem.nome_social" label="Nome Social"></v-text-field>
                  </v-col>
                  <v-col cols="12" md="6" sm="6">
                    <v-text-field v-model="editedItem.nome_pai" label="Nome do Pai"></v-text-field>
                  </v-col>
                  <v-col cols="12" md="6" sm="6">
                    <v-text-field v-model="editedItem.nome_mae" label="Nome da Mãe"></v-text-field>
                  </v-col>
                  <v-col cols="12" md="6" sm="6">
                    <v-text-field v-model="editedItem.cpf" label="CPF"></v-text-field>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="12" md="6" sm="6">
                    <v-text-field v-model="editedItem.telefone" label="Telefone"></v-text-field>
                  </v-col>
                  <v-col cols="12" md="6" sm="6">
                    <v-text-field v-model="editedItem.email" label="Email"></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue-darken-1" variant="text" @click="close">
                Cancelar
              </v-btn>
              <v-btn color="blue-darken-1" variant="text" @click="save">
                Salvar
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <v-dialog v-model="dialogDelete" max-width="520px">
          <v-card>
            <v-card-title class="text-h5">Tem certeza que deseja deletar esta pessoa?</v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue-darken-1" variant="text" @click="closeDelete">Cancel</v-btn>
              <v-btn color="blue-darken-1" variant="text" @click="deleteItemConfirm">OK</v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template v-slot:item.opcoes="{ item }">
      <v-icon class="me-2" size="small" to="/pessoas">
        mdi-file
      </v-icon>
      <v-icon class="me-2" size="small" @click="editItem(item)">
        mdi-pencil
      </v-icon>
      <v-icon size="small" @click="deleteItem(item)">
        mdi-delete
      </v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn color="primary" @click="initialize">
        Reset
      </v-btn>
    </template>
  </v-data-table>
  <v-snackbar
    :timeout="4000"
    v-model="snackbar"
    absolute
    bottom
    :color="snackbarType"
    outlined
    right
  >
    {{ snackbarMessage }}

    <template v-slot:action="{ attrs }">
      <v-btn color="pink" text @click="snackbar = false">
        Close
      </v-btn>
    </template>
  </v-snackbar>
</template>
<script>
export default {
  data: () => ({
    dialog: false,
    dialogDelete: false,
    headers: [
      { title: 'ID', align: 'start', key: 'id', sortable: false },
      { title: 'Nome', sortable: false, key: 'nome' },
      { title: 'Nome Social', key: 'nome_social', sortable: false },
      { title: 'CPF', key: 'cpf' },
      { title: 'Nome do Pai', key: 'nome_pai', sortable: false },
      { title: 'Nome do Mãe', key: 'nome_mae', sortable: false },
      { title: 'Telfone', key: 'telefone', sortable: false },
      { title: 'Email', key: 'email', sortable: false },
      { title: 'Opções', key: 'opcoes', sortable: false },
    ],
    desserts: [],
    editedIndex: -1,
    editedItem: {
      id: "",
      nome: "",
      nome_social: "",
      cpf: "",
      nome_pai: "",
      nome_mae: "",
      telefone: "",
      email: "",
    },
    defaultItem: {
      id: -1,
      nome: "",
      nome_social: "",
      cpf: "",
      nome_pai: "",
      nome_mae: "",
      telefone: "",
      email: "",
    },
    snackbar: false,
    snackbarMessage: "",
    snackbarType: "success",
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? 'Nova Pessoa' : 'Editar Pessoa'
    },
  },

  watch: {
    dialog(val) {
      val || this.close()
    },
    dialogDelete(val) {
      val || this.closeDelete()
    },
  },

  created() {
    this.initialize()
  },

  methods: {
    initialize() {
      console.log('aqui');
      fetch('http://localhost/api/pessoas')
        .then((response) => response.json())
        .then((data) => {
          console.log(data);
          this.desserts = data.data
        })
    },

    showSnackbar(message, type='success') {
      this.snackbar = true
      this.snackbarMessage = message
      this.snackbarType = type
    },

    editItem(item) {
      this.editedIndex = item.id
      this.editedItem = Object.assign({}, item)
      this.dialog = true
    },

    deleteItem(item) {
      this.editedIndex = item.id
      this.editedItem = Object.assign({}, item)
      this.dialogDelete = true
    },

    deleteItemConfirm() {
      fetch(`http://localhost/api/pessoas/${this.editedItem.id}`, {
        method: "DELETE",
      })
        .then((response) => response.json())
        .then((data) => {
          this.initialize()
          this.showSnackbar(data.message, 'success')
        })
        .catch((error) => {
          this.showSnackbar(error.message, 'error')
        })
        this.dialogDelete = false
    },

    close() {
      this.dialog = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },

    closeDelete() {
      this.dialogDelete = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },

    save() {
      if (this.editedIndex > -1) {
        fetch(`http://localhost/api/pessoas/${this.editedItem.id}`, {
          method: "PUT",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify(this.editedItem),
        })
          .then((response) => response.json())
          .then((data) => {
            this.initialize()
            this.showSnackbar(data.message, 'success')
          })
          .catch((error) => {
            this.showSnackbar(error.message, 'error')
          })
      } else {
        fetch("http://localhost/api/pessoas", {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify(this.editedItem),
        })
          .then((response) => response.json())
          .then((data) => {
            this.desserts.push(data.data)
            this.showSnackbar(data.message, 'success')
          })
          .catch((error) => {
            this.showSnackbar(error.message, 'error')
          })
      }
      this.close()
    },
  },
}
</script>
