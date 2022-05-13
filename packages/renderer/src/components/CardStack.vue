<script setup>
import { ref, onMounted } from "vue";
import Card from "./Card.vue";
import useAxiosHandler from "../composables/useAxiosHandler";
import { mainStore } from "../stores/mainStore";

const store = mainStore();

try {
  if (store.cards.length === 0) {
    const getCardsQuotes = await useAxiosHandler().get("/quotes");
    store.setCards(getCardsQuotes.data.results);
  }
} catch (err) {
  console.log(err);
}
</script>

<template>
  <div class="cards">
    <div class="leftCustom1 widthCustom1 hCustom dropzone saveDropZone">
      <h2 class="text-4xl text-center">True</h2>
    </div>
    <div class="cardContainer">
      <Card
        v-for="(card, index) in store.cards"
        :key="card"
        :card="card"
        :data-card="JSON.stringify(card)"
        :style="{
          top: index - 0.5 + 'em',
          zIndex: 22 - index,
          position: 'absolute',
        }"
        :id="'card' + index"
      ></Card>
    </div>
    <div class="leftCustom2 widthCustom2 hCustom dropzone trashDropZone">
      <h2 class="text-4xl text-center">False</h2>
    </div>
  </div>
</template>

<style>
.cards {
  top: 2em;
  left: 1em;
  width: 100%;
  height: 100%;
  display: flex;
}

.cardContainer {
  position: relative;
  width: 20em;
  height: 100%;
}

.dropzone {
  margin: 1em;
  border: 2px dashed black;
  text-align: center;
}

.dropzone.bg-green-200 {
  background-color: rgb(39, 236, 39);
}

.dropzone.bg-red-300 {
  background-color: red;
}

.widthCustom1 {
  width: 19em;
}
.widthCustom2 {
  width: 19em;
}
.hCustom {
  height: 30em;
}
</style>
