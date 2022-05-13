<script setup>
import { mainStore } from "../stores/mainStore";
import { ipcRenderer } from "electron";
import CardStack from "../components/CardStack.vue";
const store = mainStore();

async function handleSave() {
  let content = "";
  for (var i = 0; i < store.cards.length; i++) {
    if (store.cards[i].inTrue) {
      content += `TRUE: \r\n`;
      content += `"${store.cards[i].content}" \r\n`;
      content += `- ${store.cards[i].author} \r\n \r\n`;
    } else if (store.cards[i].inFalse) {
      content += `FALSE: \r\n`;
      content += `"${store.cards[i].content}" \r\n`;
      content += `- ${store.cards[i].author} \r\n \r\n`;
    }
  }
  // console.log("send: " + content);
  const reply = await ipcRenderer.invoke("create-a-file", content);
  console.log("reply: " + reply);
  // ipcRenderer.on("eventFromCreateFile", (event, arg) => {
  //   console.log("response: " + arg);
  // });
}
</script>

<template>
  <!-- <router-link class="underline ml-2" :to="{ name: 'saved-quotes' }"
    >True Quotes</router-link
  > -->
  <button
    v-if="store.cards.length"
    @click="handleSave()"
    class="btn-primary block m-2"
  >
    Save to File
  </button>
  <Suspense>
    <template #default>
      <CardStack></CardStack>
    </template>
    <template #fallback>
      <div class="loaderContainer relative flex w-24 h-7">
        Loading Cards....
      </div>
    </template>
  </Suspense>
</template>

<style>
.loaderContainer {
  top: 2em;
  left: 1em;
  width: 98%;
  height: 100%;
}
</style>
