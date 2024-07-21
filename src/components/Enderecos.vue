<template>
  <v-data-table :headers="headers" :items="desserts" :sort-by="[{ key: 'calories', order: 'asc' }]">
    <template v-slot:top>
      <v-toolbar flat>
        <v-toolbar-title>Endereços</v-toolbar-title>
        <v-divider class="mx-4" inset vertical></v-divider>
        <v-spacer></v-spacer>
        <v-dialog v-model="dialog" max-width="800px">
          <template v-slot:activator="{ props }">
            <v-btn class="mb-2" color="primary" dark v-bind="props">
              Novo Endereço
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
                    <v-combobox label="Tipo"
                      v-model="editedItem.tipo"
                      :items="['residencial', 'comercial']"></v-combobox>
                  </v-col>
                  <v-col cols="12" md="6" sm="6">
                    <v-text-field v-model="editedItem.cep" maxlength="8" label="CEP"></v-text-field>
                  </v-col>
                  <v-col cols="12" md="6" sm="6">
                    <v-text-field v-model="editedItem.logradouro" label="Logradouro"></v-text-field>
                  </v-col>
                  <v-col cols="12" md="6" sm="6">
                    <v-text-field v-model="editedItem.numero" label="Número"></v-text-field>
                  </v-col>
                  <v-col cols="12" md="6" sm="6">
                    <v-text-field v-model="editedItem.complemento" label="Complemento"></v-text-field>
                  </v-col>
                  <v-col cols="12" md="6" sm="6">
                    <v-text-field v-model="editedItem.bairro" label="Bairro"></v-text-field>
                  </v-col>
                  <v-col cols="12" md="6" sm="6">
                    <v-text-field v-model="editedItem.estado" label="Estado"></v-text-field>
                  </v-col>
                  <v-col cols="12" md="6" sm="6">
                    <v-text-field v-model="editedItem.cidade" label="Cidade"></v-text-field>
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
      <v-icon class="me-2" size="small">
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
  <v-snackbar :timeout="4000" v-model="snackbar" absolute bottom :color="snackbarType" outlined right>
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
      { title: 'Tipo', key: 'tipo', sortable: false },
      { title: 'CEP', key: 'cep', sortable: false },
      { title: 'Logradouro', key: 'logradouro', sortable: false },
      { title: 'Número', key: 'numero', sortable: false },
      { title: 'Complemento', key: 'complemento', sortable: false },
      { title: 'Bairro', key: 'bairro', sortable: false },
      { title: 'Estado', key: 'estado', sortable: false },
      { title: 'Cidade', key: 'cidade', sortable: false },
      { title: 'Opções', key: 'opcoes', sortable: false }
    ],
    desserts: [],
    editedIndex: -1,
    editedItem: {
      id: "",
      tipo: "",
      cep: "",
      logradouro: "",
      numero: "",
      complemento: "",
      bairro: "",
      estado: "",
      cidade: ""
    },
    defaultItem: {
      id: -1,
      tipo: "",
      cep: "",
      logradouro: "",
      numero: "",
      complemento: "",
      bairro: "",
      estado: "",
      cidade: ""
    },
    snackbar: false,
    snackbarMessage: "",
    snackbarType: "success",
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? 'Novo Endereço' : 'Editar Endereço'
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
      let id = this.$route.params.id;
      console.log('aqui');
      fetch(`http://localhost/api/pessoas/${id}/enderecos`)
        .then((response) => response.json())
        .then((data) => {
          console.log(data.data.enderecos);
          this.desserts = data.data.enderecos
        })
    },

    showSnackbar(message, type = 'success') {
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
      fetch(`http://localhost/api/pessoas/${this.$route.params.id}/enderecos/${this.editedItem.id}`, {
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
        fetch(`http://localhost/api/pessoas/${this.$route.params.id}/enderecos/${this.editedItem.id}`, {
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
        fetch(`http://localhost/api/pessoas/${this.$route.params.id}/enderecos`, {
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
