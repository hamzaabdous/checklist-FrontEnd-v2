<template>
  <v-container>
    <div>
      Equipments Groupe :
      <span class="titreGRP"> {{ id }}</span>
    </div>
    <v-tabs color="#1D4F91" left>
      <v-tab>users</v-tab>
      <v-tab>Damage type</v-tab>
      <v-tab>Equipment</v-tab>

      <v-tab-item>
        <v-container fluid>
          <v-row class="user">
            <User />
          </v-row>
        </v-container>
      </v-tab-item>
      <v-tab-item>
        <v-container fluid>
          <v-row class="user">
            <DamageType />
          </v-row>
        </v-container>
      </v-tab-item>
      <v-tab-item>
        <v-container fluid>
          <v-row class="user">
            <Equipements />
          </v-row>
        </v-container>
      </v-tab-item>
    </v-tabs>
  </v-container>
</template>

<script>
import User from "./user/Users.vue";
import DamageType from "./damage_Type/DamageType.vue";
import Equipements from "./equipment/Equipments.vue";
export default {
  components: {
    User,
    DamageType,
    Equipements,
  },
  data: () => ({
    id: "",
  }),
  created() {
    this.id = this.$route.params.name;
  },
};
</script>

<style></style>
