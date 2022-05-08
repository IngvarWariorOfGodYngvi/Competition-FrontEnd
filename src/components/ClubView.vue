<template>
  <q-item style="width: 100%">
    <div class="col">
    <div class="text-center text-h4 text-bold">KLUBY</div>
    <div>
    <q-virtual-scroll :items="clubList" dense type="table" class="col" style="height: 40vh;width: 100%">
      <template v-slot:before>
        <thead class="thead-sticky text-left">
          <tr>
            <th>lp</th>
            <th>Nazwa Klubu</th>
            <th>Miasto</th>
          </tr>
        </thead>
      </template>
      <template v-slot="{ item, index }">
        <tr @dblclick="open=true">
          <td>{{ index+1 }}</td>
          <td>{{ item.name }}</td>
          <td>{{item.city}}</td>
        </tr>
      </template>
    </q-virtual-scroll>
    </div>
    </div>
  </q-item>
  <q-dialog v-model="open" @keypress.enter="open=false">
      <q-card>
        <q-card-section>
          <div class="text-h6">coś będzie zmieniane</div>
        </q-card-section>

        <q-card-actions align="right">
          <q-btn flat label="zamknij" color="primary" v-close-popup />
          <q-btn flat label="Zapisz" color="primary" v-close-popup />
        </q-card-actions>
      </q-card>
</q-dialog>
</template>
<style src="../style/style.scss" lang="scss">

</style>
<script>
import { defineComponent } from "vue";
import App from 'src/App.vue';

export default defineComponent({
  name: "ClubView",
  created() {
    this.getAllClubs()
  },
  data() {
    return {
      clubList: [],
      open: false,
      local: App.host,
    };
  },
  methods: {
    getAllClubs() {
      fetch("http://" + this.local + "/club/", {
        method: "Get",
        headers: {
          "Content-Type": "application/json",
        },
      }).then((response) => {
        response.json().then((response) => {
          this.clubList = response;
        });
      });
    },
  }
});
</script>
