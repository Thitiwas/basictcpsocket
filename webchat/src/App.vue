<template>
  <div id="app">
      <div v-if="!statusmodal" height="100vh">
        <!-- <a class="button is-primary is-rounded" @click="start('room1')">Coding room</a>
        <a class="button is-link is-rounded" @click="start('room2')">Homework room </a>
        <a class="button is-info is-rounded" @click="start('room3')">Friends room </a> -->
        <b-tabs type="is-toggle" expanded>
          <a class="button is-primary is-rounded" @click="start('room1')">Coding room</a>
          <a class="button is-link is-rounded" @click="start('room2')">Homework room </a>
          <a class="button is-info is-rounded" @click="start('room3')">Friends room </a>
          <!-- <b-tab-item :label="countPeople.room1" icon="code-not-equal-variant" @change="start('room1')"></b-tab-item>
          <b-tab-item :label="countPeople.room2" icon="pencil-box" @change="start('room2')"></b-tab-item>
          <b-tab-item :label="countPeople.room3" icon="comment-account" @change="start('room3')"></b-tab-item> -->
        </b-tabs>
      </div>
    <b-modal v-else :canCancel="false" :active.sync="statusmodal">
      <div class="modal-card animation-content">
        <section class="modal-card-body is-titleless">
          <div class="media">
            <div class="media-content">
              <p>What's your name?</p>
              <br>
              <div class="field">
                <div class="control">
                  <input required="required" placeholder="Your name" maxlength="10" v-model="userName" class="input">
                </div>
                <p class="help is-danger"></p>
              </div>
            </div>
          </div>
        </section>
        <footer class="modal-card-foot">
          <button class="button is-primary" @click="statusmodal = false">
            Submit
          </button>
        </footer>
      </div>
    </b-modal>
    <div class="content">
      <p v-for="(chat, index) in allChat" :key="index">
        {{chat}}
        <!-- <p  v-if="key === userName" align="right">{{chat}}</p> -->
      </p>
    </div>
    <div class="footfix">
      <footer>
        <table class="chatbox" width="100%">
          <tr>
            <td><input class="input is-rounded" required="required" placeholder="Your message" maxlength="256" v-model="myMessage"></td>
            <td>&nbsp;&nbsp;<button class="button is-primary" @click="sendMessage()" icon="send" size="is-medium">send</button></td>
          </tr>
        </table>
      </footer>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data () {
    return {
      statusmodal: true,
      userName: '',
      userCount: 0,
      selectRoom: null,
      allChat: [],
      countPeople: null,
      myMessage: ''
    }
  },
  sockets: {
    connect () {
    },
    updatechat (data) {
      this.allChat.push(`${data.userName} : ${data.data}`)
    },
    updateusers (data) {
      this.countPeople = data
    }
  },
  methods: {
    start (room) {
      console.log('start')
      if (!this.selectRoom) {
        this.selectRoom = room
        let data = {
          userName: this.userName,
          room: room
        }
        this.$socket.emit('connectFirstTime', data)
      } else if (this.selectRoom && this.selectRoom !== room) {
        this.selectRoom = room
        this.$socket.emit('switchRoom', room)
        this.allChat = []
      }
    },
    sendMessage () {
      console.log('sendms')
      if (this.myMessage) {
        this.$socket.emit('sendchat', this.myMessage)
        this.myMessage = ''
      }
    }
  }
}
</script>

<style>
#app {
  height: 93vh;
}
.chatbox {

}
.footfix {
  padding-left: 5%;
  padding-right: 1%;
  width: 100%;
  display:scroll;
  position:fixed;
  bottom:2%;
}
.content {
  padding-left: 2%;
  padding-right: 2%;
  overflow: scroll;
  max-height: 80vh;
  display: block;
}
</style>
