<template>
  <div style="padding: 5px; padding-top: 8%">
    <v-data-table
      :headers="headers"
      :items="damageTypesFiltre"
      sort-by="item.id"
      class="elevation-1"
    >
      <template v-slot:top>
        <v-toolbar flat>
          <v-spacer></v-spacer>
          <v-dialog v-model="dialog" max-width="500px">
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
              <v-card-title>
                <span class="text-h5">{{ formTitle }}</span>
              </v-card-title>

              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" sm="6" md="8">
                      <v-text-field
                        v-model="editedItem.name"
                        label="name"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="8">
                      <v-text-field
                        v-model="editedItem.profileGroup.id"
                        label="groupe"
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
                <v-btn color="blue darken-1" text @click="openSave">
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
          <v-dialog v-model="confirmAddSave" max-width="500px">
            <v-card>
              <v-card-title class="text-h5" v-if="editedIndex == -1"
                >Are you sure you want to add this item?</v-card-title
              >
              <v-card-title class="text-h5" v-else
                >Are you sure you want to update this item?</v-card-title
              >
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="closeAddSaveDialog"
                  >No</v-btn
                >
                <v-btn color="blue darken-1" text @click="save">Yes</v-btn>
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>
      <template v-slot:item="{ item }">
        <tr>
          <td class="custom-class">{{ item.name }}</td>
          <td class="custom-class">{{ item.createdDate }}</td>
          <td class="custom-class">{{ item.profileGroup.name }}</td>
          <td class="custom-class">{{ item.department.name }}</td>

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
        <v-btn color="#99A799" class="m-2 mb-2 btn white--text">
          <v-icon large class="mr-2" @click="editItem(item)">
            mdi-pencil
          </v-icon>
        </v-btn>
        <v-btn color="#99A799" class="m-2 btn white--text">
          <v-icon large @click="deleteItem(item)"> mdi-delete </v-icon>
        </v-btn>
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
    confirmAddSave: false,
    headers: [
      { text: "name", value: "name", sortable: true },
      { text: "created date", value: "createdDate", sortable: true },
      { text: "profile Group", value: "profileGroup.name", sortable: true },
      { text: "department", value: "department.name", sortable: true },

      { text: "Actions", value: "actions", sortable: false },
    ],
    damageTypes: [],
    damageTypesFiltre: [],
    isAdd: true,
    editedIndex: -1,
    editedItem: {
      created_date: "",
      updateDate: "",
      name: "",
      department: {
        id: "",
      },
      profileGroup: {
        id: "",
      },
    },
    defaultItem: {
      created_date: "",
      updateDate: "",
      name: "",
      department: {
        id: "",
      },
      profileGroup: {
        id: "",
      },
    },
  }),
  mounted() {
    document.title = "damage type";

    this.initialize();
  },
  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "New Item" : "Edit Item";
    },
    ...mapGetters(["getdamageTypes"]),
  },
  watch: {
    dialog(val) {
      if (this.editedIndex == -1) {
        this.editedIndex = -1;
        this.editedItem = {
          created_date: "",
          updateDate: "",
          name: "",
          department: {
            id: "",
          },
          profileGroup: {
            id: "",
          },
        };
      }

      val || this.close();
    },
    dialogDelete(val) {
      val || this.closeDelete();
    },
  },
  created() {
    //this.initialize();
  },
  methods: {
    initialize() {
      this.setDAMAGETYPESAction().then(() => {
        this.damageTypes = [...this.getdamageTypes];
        this.damageTypes.map((item) => {
          if (item.profileGroup.id == localStorage.getItem("id")) {
            this.damageTypesFiltre.push(item);
          }
        });
        console.log(this.damageTypesFiltre);
      });
    },
    ...mapActions([
      "setDAMAGETYPESAction",
      "editDAMAGETYPEAction",
      "deleteDAMAGETYPEAction",
      "addDAMAGETYPEAction",
    ]),

    editItem(item) {
      this.editedIndex = this.damageTypes.indexOf(item) + 1;
      this.editedItem = Object.assign({}, item);
      console.log("item :", item);
      this.dialog = true;
    },
    deleteItem(item) {
      this.editedIndex = item.id;
      this.editedItem = Object.assign({}, item);
      this.dialogDelete = true;
    },
    deleteItemConfirm() {
      this.deleteDAMAGETYPEAction(this.editedIndex).then(() => {
        this.damageTypes = this.getdamageTypes;
      });
      this.closeDelete();
    },
    close() {
      this.editedIndex = -1;
      this.dialog = false;
    },
    closeDelete() {
      this.dialogDelete = false;
    },
    closeAddSaveDialog() {
      this.confirmAddSave = false;
    },
    openSave() {
      this.confirmAddSave = true;
    },
    save() {
      if (this.editedIndex == -1) {
        this.addDAMAGETYPEAction(this.editedItem).then(() => {
          this.damageTypes = [...this.damageTypes];
        });
        this.closeAddSaveDialog();
      } else {
        this.editDAMAGETYPEAction(this.editedItem).then(() => {
          this.damageTypes = this.getdamageTypes;
        });
        this.closeAddSaveDialog();
      }

      this.close();
    },
  },
};
</script>
