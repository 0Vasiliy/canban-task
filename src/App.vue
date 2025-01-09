<template>
  <main class="main">
    <CardList v-for="(item, index) in CardListOptions" :key="index" :options="item" />
  </main>
</template>

<script setup>
  import { ref, provide } from 'vue';
  import axios from 'axios';
  import CardList from './components/CardList.vue';

  const firstList = ref([]);
  const secondList = ref([]);
  const lastList = ref([]);
  const thirdList = ref([]);

  const CardListOptions = ref([
    {
      color: '#0aa6e362',
      title: 'Необработанные',
      id: 1,
    },
    {
      color: '#e86405bd',
      title: 'В работе',
      id: 2,
    },
     {
      color: '#5E2129',
      title: 'Отложенные',
      id: 3,
    },
    {
      color: '#008080',
      title: 'Завершенные',
      id: 4,
    },
  ]);

  const getAllCards = async () => {
    try {
      await axios
        .get('https://fakestoreapi.com/products')
        // .get('./db.json')
        .then((res) => (firstList.value = res.data));
      console.log('getAllCards - успешно');
    } catch (error) {
      console.log(error);
      alert('Упс! Ошибка получения данных ;(');
    }
  };

  getAllCards();

  provide('firstList', firstList);
  provide('secondList', secondList);
  provide('lastList', lastList);
  provide('thirdList', thirdList);
</script>

<style>
*{
  max-width: 1920px;
  margin: 0  auto;
  padding: 0;  
  box-sizing: border-box;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
}
  .main {
    margin: 2% 0;
    display: flex;
    justify-content: center;
    gap: 2%;
    width: 100%;
  }
</style>
