<template>
  <div class="chat container">
    <h2 class="center teal-text">Chat Room</h2>
    <div class="card">
      <div class="card-content">
        <ul class="messages">
          <li v-for="message in messages" :key="message.id">
            <span class="teal-text">{{message.name}}</span>
            <span class="gray-taxt text-darken-3">{{message.message}}</span>
            <span class="gray-text time">{{message.time}}</span>
          </li>
        </ul>
        <div class="card-action">
          <NewMessage :name="name"/>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import NewMessage from "@/components/NewMessage";
import db from "@/firebase/init";
import moment from "moment";
export default {
  name: "Chat",
  components: {
    NewMessage
  },
  props: ["name"],
  data() {
    return {
      messages: []
    };
  },
  created() {
    let ref = db.collection("messages").orderBy("time");
    ref.onSnapshot(snapshot => {
      snapshot.docChanges.forEach(change => {
        if (change.type == "added") {
          let doc = change.doc;
          this.messages.push({
            id: doc.id,
            name: doc.data().name,
            message: doc.data().content,
            time: moment(doc.data().time).format("lll")
          });
        }
      });
    });
  }
};
</script>

<style>
.chat h2 {
  font-size: 2.6em;
  margin-bottom: 40px;
}
.chat span {
  font-size: 1.4em;
}
.chat .time {
  display: block;
  font-size: 1.2em;
}
</style>


