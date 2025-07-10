<script setup>
import { getItems, removeCart, removeItem } from '@/services/cartService';
import { reactive, onMounted } from 'vue';

const state = reactive({
  items: [],
});

const load = async () => {
  const res = await getItems();
  if (res === undefined || res.status !== 200) {
    return;
  }
  state.items = res.data;
};

const remove = async (cartId) => {
  console.log('님아.');
  const res = await removeItem(cartId);
  if (res === undefined || res.status !== 200) {
    return;
  }
  load();
};

const clear = async () => {
  const res = await removeCart();
  if (res.status !== 200) {
    return;
  }
  load();
};

onMounted(() => {
  load();
});
</script>

<template>
  <div class="cart">
    <div class="container">
      <template v-if="state.items.length">
        <ul class="items">
          <li v-for="i in state.items">
            <img
              :alt="`상품 사진(${i.name})`"
              :src="`/pic/item/${i.imgPath}`"
            />
            <b class="name">{{ i.name }}</b>
            <span class="price">
              {{
                (i.price - (i.price * i.discountPer) / 100).toLocaleString()
              }}원
            </span>
            <span class="remove float-and" @click="remove(i.id)" title="삭제"
              >&times;</span
            >
          </li>
        </ul>
        <div class="act d-flex justify-content-between">
          <button @click="clear" class="btn btn-danger">장바구니 비우기</button>
          <router-link to="/order" class="btn btn-primary"
            >주문하기</router-link
          >
        </div>
      </template>
      <div class="text-center py-5" v-else>장바구니가 비어있습니다.</div>
    </div>
  </div>
</template>

<style scoped>
.cart {
  li {
    border: 1px solid #eee;
    margin-top: 25px;
    margin-bottom: 25px;
  }
  img {
    width: 150px;
    height: 150px;
  }

  .items {
    list-style: none;
    margin: 0;
    padding: 0;
  }
  .name {
    margin-left: 25px;
  }
  .price {
    margin-left: 25px;
  }
  .remove {
    cursor: pointer;
    font-size: 30px;
    padding: 5px 15px;
  }
}

.act .btn {
  width: 300px;
  display: block;
  padding: 30px 50px;
  font-size: 20px;
}
</style>
