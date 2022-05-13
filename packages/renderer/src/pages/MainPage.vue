<script setup>
import { mainStore } from "../stores/mainStore";
import { ipcRenderer } from "electron";
import useAxiosHandler from "../composables/useAxiosHandler";
import CardStack from "../components/CardStack.vue";
import { onMounted } from "vue";

const store = mainStore();

async function getQuotes() {
  store.cards = [];
  try {
    const getCardsQuotes = await useAxiosHandler().get("/quotes");
    for (let i = 0; i < getCardsQuotes.data.results.length; i++) {
      if (i === getCardsQuotes.data.results.length - 1) {
        getCardsQuotes.data.results[i].showCard = true;
      } else {
        getCardsQuotes.data.results[i].showCard = false;
      }
      getCardsQuotes.data.results[i].index = i;
      getCardsQuotes.data.results[i].inTrue = false;
      getCardsQuotes.data.results[i].inFalse = false;
    }
    store.setCards(getCardsQuotes.data.results);
  } catch (err) {
    console.log("Error from API: " + err);
  }
}

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
}

onMounted(() => {
  getQuotes();
});
</script>

<template>
  <!-- <router-link class="underline ml-2" :to="{ name: 'saved-quotes' }"
    >True Quotes</router-link
  > -->
  <button v-if="store.cards.length" @click="handleSave()" class="btn-primary">
    Save to File
  </button>
  <button @click="getQuotes()" class="btn-primary">Reset</button>

  <CardStack></CardStack>
</template>

<style>
button {
  margin: 1em;
}
</style>
