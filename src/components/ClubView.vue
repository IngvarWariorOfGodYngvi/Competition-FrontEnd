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
            <tr @dblclick="open=true,clubItem = item,uuid=item.uuid, name= item.name, city=item.city">
          <td>{{ index+1 }}</td>
          <td>{{ item.uuid }}</td>
          <td>{{ item.name }}</td>
          <td>{{item.city}}</td>
        </tr>
      </template>
    </q-virtual-scroll>
    </div>
    </div>
  </q-item>
<q-dialog v-model="open" @keypress.esc="open=false">
      <q-card class="full-width">
        <q-card-section class="col full-width">
          <div class="row">
          <div class="col">
            <div class="text-h6">ID: {{clubItem.uuid}}</div>
            <div class="text-h6">Nazwa: {{clubItem.name}}</div>
            <div class="text-h6">Miasto: {{clubItem.city}}</div>
          </div>
          <div class="col">
            <div><q-input class="full-width" v-model="name" dense filled color="primary" stack-label label="Nazwa"></q-input></div>
            <div><q-input class="full-width" v-model="city" dense filled color="primary" stack-label label="Miasto"></q-input></div>
          </div>
          </div>
        </q-card-section>

        <q-card-actions align="right">
          <q-btn flat label="Anuluj" color="primary" v-close-popup @click="clubItem=null"/>
          <q-btn flat label="Zmień" color="primary" v-close-popup  @click="updateClub()" />
        </q-card-actions>
      </q-card>
</q-dialog>
<q-dialog :position="'top'" v-model="failure">
      <q-card>
        <q-card-section>
          <div v-if="message!=null" class="text-h6">{{message}}</div>
          <div v-else class="text-h6">Nie można wykonać żądania. Sprawdź poprawność danych.</div>
        </q-card-section>

      </q-card>
</q-dialog>
<q-dialog :position="'top'" v-model="success">
      <q-card>
        <q-card-section>
          <div v-if="message!=null" class="text-h6">{{message}}</div>
        </q-card-section>

      </q-card>
</q-dialog>
<q-dialog :position="'top'" v-model="forbidden">
      <q-card class="bg-warning">
        <q-card-section>
          <div class="text-h6">Niewłaściwy kod. Spróbuj ponownie.</div>
        </q-card-section>

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
      uuid: "",
      name: "",
      city: "",
      clubItem: null,
      open: false,
      failure: false,
      success: false,
      forbidden: false,
      local: App.host
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
    updateClub() {
      const data = {
        uuid: this.uuid,
        name: this.firstName,
        city: this.secondName
      }
      fetch("http://" + this.local + "/club/", {
        method: "PUT",
        body: JSON.stringify(data),
        headers: {
          "Content-Type": "application/json",
        },
      }).then(() => {
        this.getAllClubs()
      });
    },
  }
});
</script>
