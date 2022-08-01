<template>
  <div>
    Invoices
    <v-data-table
      :headers="headers"
      :items="invoices"
      hide-default-footer
      class="elevation-0"
    >
      <!-- Template de las acciones -->
      <template v-slot:[`item.actions`]="{ item }">
        <v-icon small class="mr-2" @click="viewItem(item)"> mdi-eye </v-icon>
        <v-icon small class="mr-2" @click="editItem(item)"> mdi-pencil </v-icon>
      </template>

      <!-- Template Modal para crear factura -->
      <template v-slot:top>
        <v-toolbar flat>
          <v-spacer></v-spacer>
          <v-dialog v-model="dialog" max-width="500">
            <template v-slot:activator="{ on, attrs }">
              <v-btn
                color="primary"
                dark
                class="mb-2"
                v-bind="attrs"
                v-on="on"
                title="Añadir"
                @click="openDialogNew()"
              >
                Añadir
                <v-icon>mdi-plus</v-icon>
              </v-btn>
            </template>
            <v-card>
              <v-card-title class="d-flex justify-center align-center">
                <span class="text-h5">Factura nueva</span>
              </v-card-title>

              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12">
                      <v-text-field
                        v-model="formData.iva"
                        outlined
                        dense
                        label="IVA"
                        type="number"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12">
                      <v-select
                        v-model="formData.trasmitter"
                        :items="trasmitter"
                        item-text="name"
                        outlined
                        dense
                        label="Emisor"
                        return-object
                      ></v-select>
                    </v-col>
                    <v-col cols="12">
                      <v-select
                        v-model="formData.receptor"
                        :items="receptor"
                        item-text="name"
                        outlined
                        dense
                        label="Receptor"
                        return-object
                      ></v-select>
                    </v-col>
                    <v-col cols="12">
                      <v-select
                        v-model="formData.products"
                        :items="items"
                        item-text="name"
                        outlined
                        dense
                        type="number"
                        label="Productos"
                        multiple
                        return-object
                      ></v-select>
                    </v-col>
                    <div
                      class="d-flex"
                      v-for="(p, i) in formData.products"
                      :key="i"
                    >
                      <v-col cols="12" md="6" sm="6">
                        <v-text-field
                          v-model="p.name"
                          outlined
                          dense
                          :disabled="true"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" md="6" sm="6">
                        <v-text-field
                          v-model="p.cant"
                          outlined
                          dense
                        ></v-text-field>
                      </v-col>
                    </div>
                  </v-row>
                </v-container>
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="close">
                  Cancelar
                </v-btn>
                <v-btn color="blue darken-1" text @click="saveInvoice()">
                  Agregar
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>
    </v-data-table>

    <!-- Template ver Factura -->
    <v-dialog v-model="viewDialog" max-width="600">
      <v-card>
        <v-card-title class="d-flex justify-center">
          Factura #{{ viewData.nro_factura }}
        </v-card-title>
        <v-card-text>
          <div class="d-flex justify-space-between">
            <div>
              <strong>IVA </strong> {{ viewData.iva }}% <br />
              <strong>SUBTOTAL </strong> {{ viewData.subtotal }}<br />
              <strong>TOTAL </strong> {{ viewData.total }}<br />
            </div>
            <div>
              <strong>Emisor </strong> {{ viewData.trasmitter?.name }}<br />
              <strong>Receptor </strong> {{ viewData.receptor?.name }}<br />
            </div>
          </div>
          <div
            class="d-flex justify-center"
            v-for="(p, i) in viewData.details"
            :key="i"
          >
            {{ i + 1 }} - {{ p.name }}, Cantidad: {{ p.cantidad }}
          </div>
        </v-card-text>
      </v-card>
    </v-dialog>

    <!-- Template actualizar Factura -->
    <v-dialog v-model="updateDialog">
      <v-card>
        <v-card-title class="d-flex justify-center align-center">
          Factura #{{ updateData.nro_factura }}
        </v-card-title>

        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12">
                <v-text-field
                  v-model="updateData.iva"
                  outlined
                  dense
                  label="IVA"
                  type="number"
                ></v-text-field>
              </v-col>
              <v-col cols="12">
                <v-select
                  v-model="updateData.trasmitter"
                  :items="trasmitter"
                  item-text="name"
                  outlined
                  dense
                  label="Emisor"
                  return-object
                ></v-select>
              </v-col>
              <v-col cols="12">
                <v-select
                  v-model="updateData.receptor"
                  :items="receptor"
                  item-text="name"
                  outlined
                  dense
                  label="Receptor"
                  return-object
                ></v-select>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="updateDialog = false"> Cancelar </v-btn>
          <v-btn color="blue darken-1" text @click="updateInvoice()">
            Editar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>
<script>
export default {
  data() {
    return {
      dialog: false,
      viewDialog: true,
      updateDialog: false,
      headers: [
        {
          text: "Nro Factura",
          align: "start",
          sortable: false,
          value: "nro_factura",
        },
        {
          text: "Emisor",
          align: "start",
          sortable: false,
          value: "trasmitter.name",
        },
        {
          text: "Receptor",
          align: "start",
          sortable: false,
          value: "receptor.name",
        },
        {
          text: "Total",
          align: "start",
          sortable: false,
          value: "total",
        },
        {
          text: "Creado",
          align: "start",
          sortable: false,
          value: "creado",
        },
        {
          text: "Actualizado",
          align: "start",
          sortable: false,
          value: "actualizado",
        },
        { text: "Actions", value: "actions", sortable: false },
      ],
      formData: {
        nro_factura: "",
        iva: 0,
        subtotal: 0,
        total: 0,
        products: [],
        trasmitter: [],
        receptor: [],
      },
      items: [],
      trasmitter: [],
      receptor: [],
      invoices: [],
      viewData: [],
      updateData: [],
    };
  },
  created() {
    this.getInvoices();
    this.getProducts();
    this.getTransmitters();
    this.getReceptors();
  },
  methods: {
    async getInvoices() {
      await this.$axios
        .get("/api/invoices")
        .then((response) => {
          this.invoices = response.data;
        })
        .catch((e) => {
          console.log(e);
        });
    },
    async getProducts() {
      await this.$axios
        .get("/api/items")
        .then((response) => {
          this.items = response.data;
        })
        .catch((e) => {
          console.log(e);
        });
    },
    async getTransmitters() {
      await this.$axios
        .get("/api/transmitter")
        .then((response) => {
          this.trasmitter = response.data;
        })
        .catch((e) => {
          console.log(e);
        });
    },
    async getReceptors() {
      await this.$axios
        .get("/api/receptor")
        .then((response) => {
          this.receptor = response.data;
        })
        .catch((e) => {
          console.log(e);
        });
    },
    async saveInvoice() {
      await this.$axios
        .post("/api/invoices", this.formData)
        .then((response) => {
          this.getInvoices();
        })
        .catch((e) => {
          console.log(e);
        });
    },
    viewItem(item) {
      this.viewDialog = true;
      this.updateDialog = false;
      this.viewData = item;
      //   console.log("View => ", item);
    },
    editItem(item) {
      this.updateData = item;
      this.updateDialog = true;
      this.viewDialog = false;
      console.log("Edit => ", item);
    },
    async updateInvoice() {
      await this.$axios
        .post("/api/invoices/update/" + this.updateData.id, this.updateData)
        .then((response) => {
          this.getInvoices();
        })
        .catch((e) => {
          console.log(e);
        });
    },
    deleteItem(item) {
      console.log("Delete => ", item);
    },
    close() {
      this.dialog = false;
    },
    openDialogNew() {
      this.dialog = true;
      this.formData = {};
    },
  },
};
</script>