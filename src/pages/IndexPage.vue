<template>
  <q-page class="col">
    <ClubView class="col-6" style="width: 50%"></ClubView>
    <PlayerView class="col-6" style="width: 50%"></PlayerView>
    <div>
      <q-item>
        <q-input filled v-model="file" class="full-width"></q-input>
      </q-item>
      <q-btn @click="sendFile(file), this.$refs.PlayerView.getAllPlayers()">wyślij</q-btn>
    </div>
  </q-page>
</template>

<script>
import { defineComponent } from 'vue'
import ClubView from 'src/components/ClubView.vue'
import PlayerView from '../components/PlayerView.vue'
import App from 'src/App.vue'

export default defineComponent({
    name: "IndexPage",
    components: { ClubView, PlayerView },
    data () {
      return {
        file: null,
        allPlayers: [],
        local: App.host
      }
    },
    methods: {
      sendFile(file) {
        const data = file.replaceAll(/\\/gi, '/');
        fetch('http://' + this.local + '/file/', {
          method: 'PUT',
          body: JSON.stringify(data),
          headers: {
          'Content-Type': 'application/json'
        }
        })
      }
    }
})
</script>
