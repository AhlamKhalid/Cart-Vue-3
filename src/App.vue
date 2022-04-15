<script setup>
// Reactivity, Computed, Lifecycle hook
import { ref, computed, onMounted } from "vue";

// local image
// import makeupImage from "@/assets/img/makeup.jpeg";

// components
import Product from "@/components/Product.vue";

// data
const isCartOpen = ref(false);
const products = ref([]);

// computed
const numCartItems = computed(() =>
  products.value.reduce((numItems, product) => numItems + product.quantity, 0)
);

const cartTotal = computed(() =>
  products.value.reduce(
    (total, product) => total + product.price * product.quantity,
    0
  )
);

// lifecycle
onMounted(async () => {
  // products.value = [
  //   {
  //     id: 1,
  //     img: makeupImage,
  //     name: "Red lipstick",
  //     price: 16,
  //     quantity: 2
  //   },
  //   {
  //     id: 2,
  //     img: makeupImage,
  //     name: "Red lipstick",
  //     price: 16,
  //     quantity: 1
  //   },
  //   {
  //     id: 3,
  //     img: makeupImage,
  //     name: "Red lipstick",
  //     price: 16,
  //     quantity: 1
  //   },
  //   {
  //     id: 4,
  //     img: makeupImage,
  //     name: "Red lipstick",
  //     price: 16,
  //     quantity: 1
  //   }
  // ];
  products.value = await fetchProducts();
});

// methods
const showCart = () => {
  isCartOpen.value = true;
};

const hideCart = () => {
  isCartOpen.value = false;
};

/**** Sync ****/
// const removeItem = ($event) => {
//   const productId = $event;

//   products.value = products.value.filter((product) => product.id !== productId);
// };

/**** Async ****/
const removeItem = async ($event) => {
  const productId = $event;

  // update backend
  const response = await fetch(`http://localhost:5000/products/${productId}`, {
    method: "DELETE"
  });

  // update UI
  products.value = products.value.filter((product) => product.id !== productId);
};

/**** Sync ****/
// const decrementQuantity = ($event) => {
//   const productId = $event;

//   const productIndex = products.value.findIndex(
//     (product) => product.id === productId
//   );

//   // if quantity is 1, remove item
//   if (products.value[productIndex].quantity === 1) removeItem(productId);
//   else products.value[productIndex].quantity--;
// };

/**** Async ****/
const decrementQuantity = async ($event) => {
  const productId = $event;
  const productIndex = products.value.findIndex(
    (product) => product.id === productId
  );

  // if quantity is 1, remove item
  if (products.value[productIndex].quantity === 1) removeItem(productId);
  else {
    const productToUpdate = await fetchProduct(productId);
    const updatedProduct = {
      ...productToUpdate,
      quantity: --productToUpdate.quantity
    };

    const response = await fetch(
      `http://localhost:5000/products/${productId}`,
      {
        method: "PUT",
        headers: {
          "Content-type": "application/json"
        },
        body: JSON.stringify(updatedProduct)
      }
    );

    products.value[productIndex].quantity = updatedProduct.quantity;
  }
};

/**** Sync ****/
// const incrementQuantity = ($event) => {
//   const productId = $event;

//   const productIndex = products.value.findIndex(
//     (product) => product.id === productId
//   );

//   products.value[productIndex].quantity++;
// };

/**** Async ****/
const incrementQuantity = async ($event) => {
  const productId = $event;
  const productIndex = products.value.findIndex(
    (product) => product.id === productId
  );

  const productToUpdate = await fetchProduct(productId);
  const updatedProduct = {
    ...productToUpdate,
    quantity: ++productToUpdate.quantity
  };

  const response = await fetch(`http://localhost:5000/products/${productId}`, {
    method: "PUT",
    headers: {
      "Content-type": "application/json"
    },
    body: JSON.stringify(updatedProduct)
  });

  products.value[productIndex].quantity = updatedProduct.quantity;
};

// fetch all products
const fetchProducts = async () => {
  const response = await fetch("http://localhost:5000/products");
  const data = await response.json();
  return data;
};

// fetch single product
const fetchProduct = async (productId) => {
  const response = await fetch(`http://localhost:5000/products/${productId}`);
  const data = await response.json();
  return data;
};
</script>

<template>
  <!-- cart -->
  <button id="cart-btn" @click="showCart">
    <i class="fa-brands fa-opencart fa-xl"></i>
  </button>
  <!-- cart sidebar -->
  <div id="cart-sidebar" class="cart-sidebar" :class="{ show: isCartOpen }">
    <!-- header -->
    <div class="cart-header-padding">
      <div class="cart-header">
        <p>
          <span class="title">cart</span>
          <span class="items">
            (<span>{{ numCartItems }}</span>
            {{ numCartItems === 1 ? "item" : "items" }})</span
          >
        </p>
        <button id="close-btn" @click="hideCart">
          <i class="fa-solid fa-xmark fa-xl"></i>
        </button>
      </div>
    </div>
    <!-- products -->
    <div class="cart-products">
      <Product
        v-for="product in products"
        :key="product.id"
        :product="product"
        @remove-item="removeItem"
        @decrement-quantity="decrementQuantity"
        @increment-quantity="incrementQuantity"
      />
    </div>
    <!-- footer -->
    <div class="cart-footer">
      <div class="total">
        <p>total</p>
        <p>${{ cartTotal }}</p>
      </div>
      <a href="#" class="checkout-link">checkout</a>
    </div>
  </div>
</template>

<style>
@import "@/assets/css/base.css";
</style>
