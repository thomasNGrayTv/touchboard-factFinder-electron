<script setup>
import { ref, onMounted } from "vue";
import Card from "./Card.vue";
import useAxiosHandler from "../composables/useAxiosHandler";
import { mainStore } from "../stores/mainStore";

const store = mainStore();

try {
  if (store.cards.length === 0) {
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
  }
} catch (err) {
  console.log(err);
}
</script>

<template>
  <div class="cards">
    <div class="zone">
      <h2 class="text-4xl text-center">True</h2>
      <div class="leftCustom1 widthCustom1 hCustom dropzone saveDropZone"></div>
    </div>
    <div class="cardContainer dropzone">
      <Card
        v-for="(card, index) in store.cards"
        :key="card"
        :card="card"
        :data-card="JSON.stringify(card)"
        :style="{
          top: '2em',
          left: '-5px',
          position: 'absolute',
        }"
        :class="{ showCard: card.showCard }"
        :id="'card' + index"
      ></Card>
    </div>
    <div class="zone">
      <h2 class="text-4xl text-center">False</h2>
      <div
        class="leftCustom2 widthCustom2 hCustom dropzone trashDropZone"
      ></div>
    </div>
  </div>
</template>

<style>
.cards {
  top: 2em;
  left: 1em;
  width: 100%;
  min-width: 100em;
  height: 100%;
  display: flex;
  justify-content: space-around;
}

.cardContainer {
  position: relative;
  width: 30em;
  height: 100%;
}

.dropzone:not(.cardContainer) {
  margin: 1em;
  text-align: center;
}

h2 {
  background-color: bisque;
  padding: 1em 2em;
  text-align: center;
}

.widthCustom1 {
  min-width: 29em;
}
.widthCustom2 {
  min-width: 29em;
}
.hCustom {
  height: 100%;
  min-height: 30em;
}
</style>
