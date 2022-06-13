<script setup>

import ModalComponent from "./components/ModalComponent";
import { ref } from "vue";
const showModal = ref(false);
const username = ref("");
const inputUsername = ref("");
const inputMessage = ref("");
const totalOnline = ref(0);
const messageArray = ref([])
const socketObj = ref();

const enterChat = async () => {
  username.value = inputUsername.value;
  if (username.value.length === 0) {
    return;
  }
  console.log("Attempting to Connect");
  socketObj.value = await connect();
  showModal.value = false;
}

const connect = async () => {

  let clodUrl = "wss://go-socket-chat-prod-ahmed-munna2-820pj8.mo1.mogenius.io/ws";
  var socket = new WebSocket(clodUrl);
  console.log("Attempting Connection...");

  socket.onopen = () => {
    console.log("Successfully 1Connected");
  };

  socket.onmessage = msg => {
    const jsonMsg = JSON.parse(msg.data);
    console.log(jsonMsg.type);
    if (jsonMsg.type === 0) {
      totalOnline.value = jsonMsg.online;
    }
    else {
      messageArray.value.push(JSON.parse(jsonMsg.body));

      if (messageArray.value.length > 5) {
        window.scrollBy(0, 250);
        console.log("Scrolling");
      }
    }
  };

  socket.onclose = event => {
    showModal.value = true;
    console.log("Socket Closed Connection: ", event);
  };

  socket.onerror = error => {
    console.log("Socket1 Error: ", error);
  };


  console.log("Exiting");
  return socket;
};

const SendMessage = () => {
  if (inputMessage.value === "") {
    return
  }
  console.log("sending msg: ", inputMessage.value);
  let msgObj = {
    user: username.value,
    messageBody: inputMessage.value
  }
  socketObj.value.send(JSON.stringify(msgObj));
  inputMessage.value = "";
};

</script>

<template>

  <Teleport to="body">
    <ModalComponent :show="showModal" @close="enterChat">
      <template #header>
        <h5>You Have been disconnected due to inactivity</h5>
      </template>
    </ModalComponent>
  </Teleport>


  <div class="container-fluid mt-5" v-if="username === '' || username === null">
    <div class="card bg-light">
      <div class="m-2">
        <div class="card-title pt-5">
          <h1 class="text-primary">Online Group Messeneger</h1>
          <hr />
          <div class="container row justify-content-around">
            <input class="form form-control m-1 col-6" type="text" v-model="inputUsername" placeholder="Enter a name" />
            <button class="btn btn-outline-primary m-1 col" @click="enterChat">Enter Chat Room</button>
          </div>
        </div>
        <div class="card-body border">
          <ul class="list-group list-group-flush">
            <li class="list-group-item lead bg-info text-dark mb-1">This is a realtime websocket chat app built using
              Golang and Vue</li>
            <li class="list-group-item lead bg-info text-danger mb-1">Any msg sent will be received by server and server will broadcast it to 
              all the Currently online users. 
            </li>
            <li class="list-group-item lead bg-info text-dark mb-1">Currently It doesn't store any messages or remembers
              any of the user</li>
            <li class="list-group-item lead bg-info text-dark mb-1">If No one is Online Open two different tabs and
              Kindly have a go</li>

            <li class="list-group-item lead bg-success text-white mb-3">Source Code: &nbsp; &nbsp;
              <div>
                <span>
                  <a class="btn btn-primary" href="https://github.com/ahmedMunna1767/vue-mogenius.git">Frontend</a>
                </span>
                &nbsp;
                <span>
                  <a class="btn btn-primary"
                    href="https://github.com/ahmedMunna1767/go-vue-socket-chat.git">Backend</a>
                </span>

              </div>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>


  <div class="" v-else>
    <nav class="navbar fixed-top navbar-light bg-success">
      <div class="container-fluid">
        <a class="navbar-brand" href="#">
          <img src="https://mdbcdn.b-cdn.net/img/Photos/new-templates/bootstrap-chat/ava3-bg.webp" alt="" width="50" height="50">
        </a>
        <p class="navbar-brand mb-0 h1 text-center text-white">Welcome, {{ username }}</p> <span
          class="navbar-brand mb-0 h3 text-end text-white">Total Online: {{ totalOnline }}</span>
      </div>
    </nav>

    <section class="chatbox d-flex flex-column overflow-scroll container-fluid my-5 py-5">
      <ul class="list-group list-unstyled">
        <div  v-for="message in messageArray" :key="message">
          <li v-if="(message.user !== username)" class="d-flex justify-content-start mb-3">

            <img src="https://mdbcdn.b-cdn.net/img/Photos/new-templates/bootstrap-chat/ava6-bg.webp" alt="avatar"
                    class="rounded-circle d-flex align-self-end ms-3 shadow-1-strong" width="25">
            <div class="card" style="width: 30rem;">
              <div class="card-header d-flex justify-content-start p-1">
                <p class="mb-0 p-3 pt-1 pb-1 lead">{{message.user}}</p>
              </div>
              <div class="card-body p-3 pt-1 pb-1">
                <p class="mb-0">
                  {{ message.messageBody }}
                </p>
              </div>
            </div>
          </li>
          <li v-else class="d-flex justify-content-end mb-3" >
            <div class="card" style="width: 30rem;">
              <div class="card-header d-flex justify-content-end p-1">
                <p class="mb-0 lead p-3 pt-1 pb-1">Me</p>
              </div>
              <div class="card-body p-3 pt-1 pb-1">
                <p class="mb-0">
                  {{ message.messageBody }}
                </p>
              </div>
            </div>
            <img src="https://mdbcdn.b-cdn.net/img/Photos/new-templates/bootstrap-chat/ava3-bg.webp" alt="avatar"
                    class="rounded-circle d-flex align-self-end ms-3 shadow-1-strong" width="25">
          </li>
        </div>
      </ul>
    </section>

    <nav class="navbar fixed-bottom navbar-light bg-success">
      <form @submit.prevent="SendMessage" class="container-fluid m-10 mt-0 mb-0">
        <div class="input-group">
          <span class="input-group-text" id="basic-addon1">Hit Enter To Send</span>
          <input type="text" v-model="inputMessage" class="form-control" placeholder="Enter Your Message Here">
        </div>
      </form>
    </nav>
  </div>
</template>
<style scoped>
:root {
  --blue: #1e90ff;
  --white: #ffffff;
  --mineshaft: #1f1f1f;
  --wildsand: #f6f6f6;
  --pastelgreen: #69eb95;
  --turqouise: #0ae8d6;
  --flashorange: #FF8702;
  --scorpion: #5e5e5e;
  --goldenfrizz: #faee3a;
  --malta: #bdafa1;
}

.chatbox::-webkit-scrollbar {
  display: none;
}
</style>
