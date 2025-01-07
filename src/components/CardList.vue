<template>
 <section>
  <div class="selected"> 
    <ul class="selected-menu">
      <li><a href="#">Сортировать по рейтингу</a>
          <ul>
            <li><a href="#" @click="sortList1" v-bind="props">По высокому рейтингу</a></li>
            <li><a href="#" @click="sortList2" v-bind="props">По низкому рейтингу</a></li>
            <li><a href="#" @click="sortList3" v-bind="props">Без рейтинга</a></li>
          </ul>
      </li>
    </ul>
  </div> 
  <div class="list-block"
    :style="`background: ${options.color}`"
    @drop="onDrop($event, options.id)"
    @dragover.prevent
    @dragenter.prevent>
   
    <div class="title">
      <h2>
        {{ options.title }}
      </h2>
      <div class="counter">
        <span>{{ cards.length }}</span>
      </div>
    </div>
    <v-btn
      icon="mdi-plus"
      variant="tonal"
      class="mt-5"
      color="white"
      @click="isNewCardDialogOpen = true" />

    <CardItem
      v-for="(card, index) in cards"
      draggable="true"
      :key="index"
      :card="card"
      :options="props.options"
      @delete-card="deleteCard(card.id)"
      @dragstart="onDragStart($event, card)" />

    <CardForm
      title="Добавление новой карточки"
      v-model="isNewCardDialogOpen"
      :form="form"
      @save-card="addCard"
      @close-form="isNewCardDialogOpen = false" />
  </div>
 </section>
</template>

<script setup>
  import { ref, inject } from 'vue';
  import CardItem from './CardItem.vue';
  import CardForm from './CardForm.vue';
  const firstList = inject('firstList');
  const secondList = inject('secondList');
  const lastList = inject('lastList');
  const thirdList = inject('thirdList');

  const props = defineProps({
    options: {},
  });

  const isNewCardDialogOpen = ref(false);

  const form = ref({
    image: '',
    id: null,
    title: '',
    description: '',
    category: '',
    price: null,
    rating: {},
  });

  let cards = ref([]);

  function getLocalCards() {
    switch (props.options.id) {
      case 1:
        cards = firstList;
        break;
      case 2:
        cards = secondList;
        break;
      case 3:
        cards = lastList;
        break;
      case 4:
        cards = thirdList;
        break;
    }
  }
  getLocalCards();

  function addCard() {
    cards.value.unshift(form.value);
    isNewCardDialogOpen.value = false;
  }
  function deleteCard(id) {
    cards.value = cards.value.filter((card) => card.id != id);
  }
  function onDragStart(event, item) {
    event.dataTransfer.dropEffect = 'move';
    event.dataTransfer.effectAllowed = 'move';
    event.dataTransfer.setData('itemId', item.id.toString());
  }
  // Функция перетаскивания карточек
  function onDrop(event, optionsId) {
    const itemId = parseInt(event.dataTransfer.getData('itemId'));
    let otherLists = [];

    switch (optionsId) {
      case 1:
        cards.value = firstList.value;
        otherLists = secondList.value.concat(lastList.value,thirdList.value);
        break;
      case 2:
        cards.value = secondList.value;
        otherLists = firstList.value.concat(lastList.value,thirdList.value); 
        break;
      case 3:
        cards.value = lastList.value;
        otherLists = secondList.value.concat(firstList.value,thirdList.value);
        break;
      case 4:
        cards.value = thirdList.value;
        otherLists = secondList.value.concat(firstList.value,lastList.value,thirdList.value);
        break;
    }

    const newCard = otherLists.find((card) => card.id === itemId);

    firstList.value = firstList.value.filter((card) => card.id !== newCard.id);
    secondList.value = secondList.value.filter((card) => card.id !== newCard.id);
    lastList.value = lastList.value.filter((card) => card.id !== newCard.id);
    thirdList.value = thirdList.value.filter((card) => card.id !== newCard.id);
    cards.value.unshift(newCard);

    switch (optionsId) {
      case 1:
        firstList.value = cards.value;
        break;
      case 2:
        secondList.value = cards.value;
        break;
      case 3:
        lastList.value = cards.value;
        break;
      case 4:
        thirdList.value = cards.value;
        break;
    }
  }
  
  function sortList1() {
    getLocalCards();
    cards = cards.value.sort((a, b) => b.rating.rate - a.rating.rate);
  }
  function sortList2() {
    getLocalCards();
    cards = cards.value.sort((a, b) => a.rating.rate - b.rating.rate);
  }
  function sortList3() {
    getLocalCards();
    cards = cards.value.sort((a, b) => { return Math.random() - 0.5 });
  }
</script>

<style lang="scss" scoped>
  section{
    padding: 10px;
    width: 300px;
    min-height: 500px;
    height: fit-content;
    border-radius: 10px;
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .list-block {
    padding: 10px;
    width: 300px;
    min-height: 500px;
    height: fit-content;
    border-radius: 10px;
    display: flex;
    flex-direction: column;
    align-items: center;
    .title {
      padding-bottom: 10px;
      width: 90%;
      display: flex;
      align-items: center;
      justify-content: center;
      border-bottom: 2px solid white;
      .counter {
        margin-left: 10px;
        background: white;
        border-radius: 50%;
        width: 30px;
        height: 30px;
        display: flex;
        justify-content: center;
        align-items: center;
      }
    }
  }
  .selected{
      height: 55px;
      width: 300px;
      border: 1px solid gray;
      background: white;
      color: black;
      margin-bottom: 20px;
      border-radius: 10px;
    }
    .selected-menu{
        display: flex;
        padding: 10px;
      }
    .selected-menu li{
      width: 380px;
      list-style: none;
      position:relative;
      background: white;
    }
    .selected-menu a{
      width: 100%;
      text-decoration: none ;
      color: black;
      display: block;
      padding: 0 10px;
      height: 40px;
      line-height: 40px;
      transition: all .5s;
    }
    .selected-menu a:hover{
      background: gray;
      width: 100%;
    }
    .selected-menu ul{
      position: absolute;
      display: none;
    }
    .selected-menu li:hover>ul{
      display: block;
    }

</style>
