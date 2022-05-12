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
            <tr @dblclick="open=true,playerItem = item,uuid=item.uuid, firstName= item.firstName, secondName=item.secondName,licenseNumber=item.licenseNumber, vintage = item.vintage, playerClubName=item.club.name, startNumber= item.startNumber">
              <td>{{index+1}}</td>
              <td>{{item.secondName}}</td>
              <td>{{item.firstName}}</td>
              <td>{{item.licenseNumber}}</td>
              <td>{{item.vintage}}</td>
              <td>tutaj trzeba dorobić kategorię wiekową</td>
              <td>{{item.startNumber}}</td>
              <td>{{item.club.name}} {{item.club.city}}</td>
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
      <q-dialog @keypress.enter="open=false" style="width:80vw">
      <q-card style="width:60vw;height:60vh">
          <q-card-section class="row items-center q-pb-none">
          <q-space />
          <q-btn icon="close" flat round dense v-close-popup />
        </q-card-section>
        <div class="row" style="height:85%;">
        <q-card-section class="col">
          <div>
            <div class="text-h6">Nazwisko i imię:{{playerItem.secondName}} {{playerItem.firstName}}</div>
            <div class="text-h6">Numer startowy:{{playerItem.startNumber}}</div>
            <div class="text-h6">Numer licencji:{{playerItem.licenseNumber}}</div>
            <div class="text-h6">rocznik:{{playerItem.vintage}}</div>
            <div class="text-h6">Klub:{{playerItem.club.name}} {{playerItem.club.city}}</div>
          </div>
        </q-card-section>
        <q-card-section class="col">
          <div>
            <div><q-input class="full-width" filled label="coś" color="primary" stack-label></q-input></div>
            <div>{{playerItem.startNumber}}</div>
            <div>{{playerItem.licenseNumber}}</div>
            <div>{{playerItem.vintage}}</div>
            <div>{{playerItem.club.name}} {{playerItem.club.city}}</div>
          </div>
        </q-card-section>
        </div>
        <q-card-actions align="right">
          <q-btn flat label="zamknij" color="primary" v-close-popup />
          <q-btn flat label="Zapisz" color="primary" v-close-popup />
        </q-card-actions>
      </q-card>
</q-dialog>
<q-dialog v-model="open" @keypress.esc="open=false">
      <q-card class="full-width">
        <q-card-section class="col full-width">
          <div class="row">
          <div class="col">
            <div class="text-h6">Nazwisko: {{playerItem.secondName}}</div>
            <div class="text-h6">Imię: {{playerItem.firstName}}</div>
            <div class="text-h6">Numer startowy: {{playerItem.startNumber}}</div>
            <div class="text-h6">Numer licencji: {{playerItem.licenseNumber}}</div>
            <div class="text-h6">rocznik: {{playerItem.vintage}}</div>
            <div class="text-h6">Klub: {{playerItem.club.name}} {{playerItem.club.city}}</div>
          </div>
          <div class="col">
            <div><q-input class="full-width" v-model="secondName" dense filled color="primary" stack-label label="Nazwisko"></q-input></div>
            <div><q-input class="full-width" v-model="firstName" dense filled color="primary" stack-label label="Imię"></q-input></div>
            <div><q-input class="full-width" v-model="startNumber" dense filled color="primary" stack-label label="Numer Startowy"></q-input></div>
            <div><q-input class="full-width" v-model="licenseNumber" dense filled color="primary" stack-label label="Numer Licencji"></q-input></div>
            <div><q-input class="full-width" v-model="vintage" dense filled color="primary" stack-label label="Rocznik"></q-input></div>
            <div><q-select class="full-width" v-model="playerClubName" :options="allClubsNames" use-input dense filled color="primary" stack-label label="Klub"></q-select></div>
          </div>
          </div>
        </q-card-section>

        <q-card-actions align="right">
          <q-btn flat label="Anuluj" color="primary" v-close-popup @click="playerItem=null"/>
          <q-btn flat label="Zmień" color="primary" v-close-popup  @click="updatePlayer(playerClubName)" />
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
    this.getAllClubNames()
  },
  data() {
    return {
      uuid: "",
      firstName: "",
      secondName: "",
      startNumber: "",
      licenseNumber: "",
      vintage: "",
      playerClubName: null,
      allClubsNames: [],
      playerList: [],
      open: false,
      playerItem: null,
      local: App.host
    };
  },
  methods: {
    getAllPlayers() {
      fetch("http://" + this.local + "/player/", {
        method: "GET",
        headers: {
          "Content-Type": "application/json",
        },
      }).then((response) => {
        response.json().then((response) => {
          this.playerList = response;
        });
      });
    },
    getAllClubNames() {
      fetch("http://" + this.local + "/club/names", {
        method: "GET",
        headers: {
          "Content-Type": "application/json",
        },
      }).then((response) => {
        response.json().then((response) => {
          this.allClubsNames = response;
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
    updatePlayer(club) {
      const data = {
        uuid: this.uuid,
        firstName: this.firstName,
        secondName: this.secondName,
        licenseNumber: this.licenseNumber,
        vintage: this.vintage,
        startNumber: this.startNumber,
      }
      fetch("http://" + this.local + "/player/?club=" + club, {
        method: "PUT",
        body: JSON.stringify(data),
        headers: {
          "Content-Type": "application/json",
        },
      }).then(() => {
        this.getAllPlayers()
      });
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
