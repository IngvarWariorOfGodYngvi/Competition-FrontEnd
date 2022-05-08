<template>
  <q-item style="width: 100%">
    <div class="col">
    <div class="text-center text-h4 text-bold">ZAWODNICY</div>
    <div>
    <q-virtual-scroll :items="playerList" dense type="table" style="height: 40vh;width:100%">
      <template v-slot:before>
        <thead class="thead-sticky text-left">
          <tr>
            <th>lp</th>
            <th>Nazwisko</th>
            <th>Imię</th>
            <th>Licencja</th>
            <th>Rocznik</th>
            <th>Kategoria Wiekowa</th>
            <th>Numer startowy</th>
            <th>Klub</th>
          </tr>
        </thead>
          </template>
          <template v-slot="{ item, index }">
            <tr @dblclick="open=true">
              <td>{{index+1}}</td>
              <td>{{item.secondName}}</td>
              <td>{{item.firstName}}</td>
              <td>{{item.licenseNumber}}</td>
              <td>{{item.vintage}}</td>
              <td>tutaj trzeba dorobić kategorię wiekową</td>
              <td>{{item.startNumber}}</td>
              <td>{{item.club.name}}</td>
            </tr>
          </template>
    </q-virtual-scroll>
    </div>
    </div>
  </q-item>
    <q-item>
      <q-btn label="wszyscy.pdf" @click="getAllPlayersPDF()"></q-btn>
    </q-item>
    <q-item>
      <q-btn label="generuj csv" @click="generateCSV()"></q-btn>
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
import axios from 'axios'

export default defineComponent({
  name: "PlayerView",
  created() {
    this.getAllPlayers()
  },
  data() {
    return {
      playerList: [],
      open: false,
      local: App.host
    };
  },
  methods: {
    getAllPlayers() {
      fetch("http://" + this.local + "/player/", {
        method: "Get",
        headers: {
          "Content-Type": "application/json",
        },
      }).then((response) => {
        response.json().then((response) => {
          this.playerList = response;
        });
      });
    },
    getAllPlayersPDF () {
      axios({
        url: 'http://' + this.local + '/file/all',
        method: 'GET',
        responseType: 'blob'
      }).then(response => {
        const fileURL = window.URL.createObjectURL(new Blob([response.data]))
        const fileLink = document.createElement('a')
        fileLink.href = fileURL
        fileLink.setAttribute('download', 'wszyscy.pdf')
        document.body.appendChild(fileLink)
        fileLink.click()
      })
    },
    generateCSV () {
      axios({
        url: 'http://' + this.local + '/file/generate?number=200',
        method: 'GET',
        responseType: 'blob'
      }).then(response => {
        const fileURL = window.URL.createObjectURL(new Blob([response.data]))
        const fileLink = document.createElement('a')
        fileLink.href = fileURL
        fileLink.setAttribute('download', 'generowanie.csv')
        document.body.appendChild(fileLink)
        fileLink.click()
      })
    },
  },
});
</script>
