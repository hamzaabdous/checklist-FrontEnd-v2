<template>
  <div style="padding: 5px; padding-top: 8%">
    <v-data-table
      :headers="headers"
      :items="equipmentsFiltre"
      sort-by="item.id"
      class="elevation-1"
    >
      <template v-slot:top>
        <v-toolbar flat>
          <v-spacer></v-spacer>
          <v-dialog v-model="dialog" max-width="1000px">
            <template v-slot:activator="{ on, attrs }">
              <v-btn
                color="#002f6c"
                class="mb-2 btn white--text"
                v-bind="attrs"
                v-on="on"
              >
                <v-icon left> mdi-account-multiple-plus </v-icon>
                Add
              </v-btn>
            </template>
            <v-card>
              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.username"
                        label="username"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.lastName"
                        label="lastName"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.firstName"
                        label="firstName"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.email"
                        label="email"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.update_date"
                        label="update date"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.phone_number"
                        label="phone number"
                      ></v-text-field>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="close(item)">
                  Cancel
                </v-btn>
                <v-btn color="blue darken-1" text @click="save(editedItem)">
                  Save
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
          <v-dialog v-model="dialogDelete" max-width="500px">
            <v-card>
              <v-card-title class="text-h5"
                >Are you sure you want to delete this item?</v-card-title
              >
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="closeDelete"
                  >Cancel</v-btn
                >
                <v-btn color="blue darken-1" text @click="deleteItemConfirm"
                  >OK</v-btn
                >
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>
      <template v-slot:item="{ item }">
        <tr>
          <td class="custom-class">{{ item.name }}</td>
          <td class="custom-class">{{ item.description }}</td>
          <td class="custom-class">{{ item.phoneNumber }}</td>
          <td class="custom-class">{{ item.function }}</td>

          <td class="custom-class">
            <v-btn
              color="#99A799"
              class="mb-2 btn white--text"
              @click="editItem(item)"
            >
              <v-icon medium class="mr-2"> mdi-pencil </v-icon>
            </v-btn>
            <br />
            <v-btn
              color="#99A799"
              class="mb-2 btn white--text"
              @click="editItem(item)"
            >
              <v-icon medium @click="deleteItem(item)"> mdi-delete </v-icon>
            </v-btn>
          </td>
        </tr>
      </template>
      <template v-slot:[`item.actions`]="{ item }">
        <v-btn
          color="#99A799"
          class="mb-2 btn white--text"
          @click="editItem(item)"
        >
          <v-icon medium class="mr-2"> mdi-pencil </v-icon>
        </v-btn>

        <v-icon medium @click="deleteItem(item)"> mdi-delete </v-icon>
      </template>
      <template v-slot:no-data>
        <v-btn color="primary" @click="initialize()"> Reset </v-btn>
      </template>
    </v-data-table>
  </div>
</template>
<script>
import { mapActions, mapGetters } from "vuex";

export default {
  data: () => ({
    dialog: false,
    dialogDelete: false,
    headers: [
      { text: "name", sortable: true },
      { text: "description", sortable: true },
      { text: "Actions", value: "actions", sortable: false },
    ],
    equipments: [],
    equipmentsFiltre: [],
    idgrp: null,
    isAdd: true,
    editedIndex: -1,
    editedItem: {
      id: null,
      name: "",
      department: {
        id: null,
      },
    },
    defaultItem: {
      id: null,
      name: "",
      department: {
        id: null,
      },
    },
  }),
  mounted() {
    document.title = "user";

    this.initialize();
  },
  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "New Item" : "Edit Item";
    },
    ...mapGetters(["getequipments"]),
  },
  watch: {
    dialog(val) {
      val || this.close();
    },
    dialogDelete(val) {
      val || this.closeDelete();
    },
  },
  created() {
    this.idgrp = localStorage.getItem("id");
    //this.initialize();
  },
  methods: {
    initialize() {
      console.log("initialize");
      //debugger;

      this.setequipmentsAction().then(() => {
        this.equipments = [...this.getequipments];
        console.log(this.equipments);
      });

      this.equipments.map((item) => {
        if (item.profileGroup.id == this.idgrp) {
          this.equipmentsFiltre.push(item);
        }
      });
    },
    ...mapActions([
      "setequipmentsAction",
      "editEQUIPMENTAction",
      "deleteEQUIPMENTAction",
      "addEQUIPMENTAction",
    ]),

    editItem(item) {
      this.editedIndex = this.equipments.indexOf(item) + 1;
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },
    deleteItem(item) {
      this.editedIndex = item.id;
      this.editedItem = Object.assign({}, item);
      this.dialogDelete = true;
    },
    deleteItemConfirm() {
      this.deleteEQUIPMENTAction(this.editedIndex).then(() => {
        this.equipments = this.getequipments;
      });
      this.closeDelete();
    },
    close() {
      this.dialog = false;
    },
    closeDelete() {
      this.dialogDelete = false;
    },
    save() {
      if (this.editedIndex == -1) {
        console.log("add");
        this.addUserAction(this.editedItem).then(() => {
          this.equipments = [...this.getequipments];
        });
      } else {
        console.log("edite");

        this.editUserAction(this.editedItem).then(() => {
          this.equipments = [...this.getequipments];
        });
      }

      this.close();
    },
  },
};
</script>
