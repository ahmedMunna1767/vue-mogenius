<script setup>
import { ref } from "vue";

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
}

const connect = async () => {
  let clodUrl = "ws://go-socket-chat-n070lf:443/ws";

  // let clodUrl = "ws://go-socket-chat-prod-ahmed-munna2-820pj8.mo1.mogenius.io:80/ws";
  // let localUrl = "ws://localhost:8080/ws";
  var socket = new WebSocket(clodUrl);
  console.log("Attempting Connection...");

  socket.onopen = () => {
    console.log("Successfully Connected");
  };

  socket.onmessage = msg => {
    const jsonMsg = JSON.parse(msg.data);
    console.log(jsonMsg.type);
    if (jsonMsg.type === 0) {
      totalOnline.value = jsonMsg.online;
    }
    else {
      messageArray.value.push(JSON.parse(jsonMsg.body));
      
      if(messageArray.value.length > 5){
        window.scrollBy(0, 250);
        console.log("Scrolling");
      } 
    }
  };

  socket.onclose = event => {
    console.log("Socket Closed Connection: ", event);
  };

  socket.onerror = error => {
    console.log("Socket Error: ", error);
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
  <div class="view login" v-if="username === '' || username === null">
    <div class="login-form">
      <div class="form-inner">
        <h1>What Should We Call You</h1>
        <input type="text" v-model="inputUsername" placeholder="Please enter a name e.g. Dragon123" />
        <button @click="enterChat">Enter Chat Room</button>
        <div></div>
        <h4>This is a realtime websocket chat app built using Golang and vue</h4>
        <h4>Currently It doesn't store any messages or remembers any of the user. </h4>
        <h4>Git repo link <a href="">Frontend</a> &nbsp;<a href="">Backend</a></h4>
      </div>
    </div>
  </div>

  <div  class="view chat" v-else>
    <header>
      <div class="header-wrapper">
        <h3>Welcome, {{ username }}</h3>
        <h3>Total Online: {{ totalOnline }}</h3>
      </div>
    </header>

    <section class="chat-box">
      <div id="chatDivID" v-for="message in messageArray" :key="message"
        :class="(message.user === username ? 'message current-user' : 'message')">
        <div class="message-inner">
          <div class="username">{{ message.user === username ? 'me' : message.user }}</div>
          <div class="content">{{ message.messageBody }}</div>
          <hr />
        </div>
      </div>
    </section>

    <footer>
      <form @submit.prevent="SendMessage">
        <input type="text" v-model="inputMessage" placeholder="Write a message..." />
        <input type="submit" value="Send" />
      </form>
    </footer>
  </div>
</template>

<style lang="scss">
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


* {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  background-color: var(--pastelgreen);

  transition: all .5s ease-out;

}

.view {
  margin: auto;
  width: 50%;
  display: flex;
  justify-content: center;
  min-height: 100vh;

  overflow-y: scroll hr {
    display: block;
    margin-top: 0.5em;
    margin-bottom: 0.5em;
    margin-left: auto;
    margin-right: auto;
    border-style: inset;
    border-width: 1px;
  }


  &.login {
    align-items: center;

    .login-form {
      display: block;
      width: 100%;
      padding: 15px;

      .form-inner {
        display: block;
        padding: 50px 15px;
        border-radius: 16px;
        background-color: var(--mineshaft);
        box-shadow: 0px 6px 12px rgba(0, 0, 0, 0.5);

        h1 {
          background-color: var(--mineshaft);
          color: var(--turqouise);
          font-size: 28px;
          margin-bottom: 30px;
        }

        input[type="text"] {
          appearance: none;
          border: none;
          outline: none;
          background: none;
          display: block;
          width: 100%;
          padding: 10px 15px;
          border-radius: 8px;
          margin-bottom: 15px;

          color: var(--mineshaft);
          font-size: 18px;
          box-shadow: 0px 0px 0px rgba(0, 0, 0, 0);
          background-color: var(--wildsand);
          transition: 0.4s;

          &::placeholder {
            color: var(--turqouise);
            transition: 0.4s;
          }
        }

        button {
          cursor: pointer;
          display: block;
          width: 100%;
          padding: 10px 15px;
          background-color: var(--blue);
          border-radius: 8px;
          color: var(--mineshaft);
          font-size: 18px;
        }

        button:hover {
          background-color: var(--turqouise);
          font-size: larger;
          transition: all 1s ease-in-out;
        }

        &:focus-within {
          input[type="text"] {
            background-color: var(--white);
            box-shadow: 0 0 6px rgba(0, 0, 0, 0.6);

            &::placeholder {
              color: var(--scorpion);
            }
          }
        }
      }
    }
  }

  &.chat {
    display: block;


    header {
      width: 50%;
      position: fixed;
      display: flex;
      justify-content: center;
      align-items: center;
      margin-bottom: 10px;
      padding: 5px;
      border-radius: 999px;
      box-shadow: 0 0 6px rgba(0, 0, 0, 0.6);
      background-color: var(--flashorange);
      .header-wrapper {
        h3 {
          background-color: var(--flashorange);
          color: var(--wildsand);
        }
      }
    }

    .chat-box {
      flex-direction: column;
      scroll-behavior: auto;
      border-radius: 24px 24px 0px 0px;
      background-color: var(--turqouise);
      box-shadow: 0px 0px 12px rgba(100, 100, 100, 0.2);
      flex: 1 1 100%;
      padding: 30px;
      min-height: 100vh;
      .message {
        display: flex;
        margin-bottom: 15px;
        background-color: var(--turqouise);
        flex-wrap: wrap-reverse;

        .message-inner {
          background-color: var(--turqouise);

          .username {
            color: var(--scorpion);
            font-size: 16px;
            margin-bottom: 5px;
            padding-left: 15px;
            padding-right: 15px;
            background-color: var(--turqouise);
          }



          .content {
            display: inline-block;
            padding: 10px 20px;
            background-color: var(--white);
            border-radius: 999px;
            color: var(--blue);
            font-size: 18px;
            line-height: 1.2em;
            text-align: left;
          }
        }

        &.current-user {
          margin-top: 30px;
          justify-content: flex-end;
          text-align: right;

          .message-inner {
            max-width: 75%;

            .content {
              color: var(--wildsand);
              font-weight: 400;
              background-color: var(--blue);
            }
          }
        }
      }
    }

    footer {
      position: sticky;
      bottom: 0px;
      padding: 10px;
      box-shadow: 0px 0px 12px rgba(100, 100, 100, 0.7);
      background-color: var(--flashorange);

      form {
        display: flex;
        background-color: var(--flashorange);

        input[type="text"] {
          flex: 1 1 100%;
          display: block;
          width: 100%;
          padding: 10px 15px;
          border-radius: 8px 8px 8px 8px;

          color: var(--mineshaft);
          font-size: 18px;
          box-shadow: 0px 0px 12px rgba(100, 100, 100, 0.2);
          background-color: var(--wildsand);
          transition: 0.5s;

          &::placeholder {
            color: var(--scorpion);
            transition: all 0.4s ease-in;
          }
        }

        input[type="submit"] {
          cursor: pointer;
          display: block;
          padding: 10px 15px;
          border-radius: 8px 8px 8px 8px;
          background-color: var(--blue);
          color: var(--wildsand);
          font-size: 18px;
          font-weight: 500;
          margin-left: 2px;
        }

        input[type="submit"]:hover {
          font-size: 19px;
          margin-left: 4px;
        }
      }
    }
  }
}
</style>
