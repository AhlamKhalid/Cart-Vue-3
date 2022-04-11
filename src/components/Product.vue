<script setup>
defineProps(["product"]);
defineEmits(["remove-item", "decrement-quantity", "increment-quantity"]);
</script>

<template>
  <div class="product">
    <div class="details">
      <img :src="product.img" :alt="product.name" class="product-img" />
      <div class="product-info">
        <p class="product-name">{{ product.name }}</p>
        <p class="product-price">${{ product.price }}</p>
      </div>
    </div>
    <div class="controls">
      <div class="quantity">
        <button
          class="decrement-btn"
          @click="$emit('decrement-quantity', product.id)"
        >
          <i class="fa-solid fa-minus fa-sm"></i>
        </button>
        <p class="quantity-num">{{ product.quantity }}</p>
        <button
          class="increment-btn"
          @click="$emit('increment-quantity', product.id)"
        >
          <i class="fa-solid fa-plus fa-sm"></i>
        </button>
      </div>
      <button class="trash-btn" @click="$emit('remove-item', product.id)">
        <i class="fa-solid fa-trash-can fa-lg"></i>
      </button>
    </div>
  </div>
</template>

<style scoped>
.product {
  margin-bottom: 10px;
  border: 1px solid #e8e8e8;
  border-radius: 3px;
}

.details {
  padding: 15px;
  display: flex;
  align-items: center;
}

.product-img {
  width: 100px;
}

.product-info {
  margin-left: 20px;
}

.product-name {
  font-size: 12px;
  font-weight: 700;
}

.product-price {
  font-size: 12px;
  margin-top: 4px;
}

/* controls */
.controls {
  border-top: 1px solid #e8e8e8;
  display: flex;
}

.quantity,
.trash-btn {
  width: 50%;
}

.quantity {
  display: flex;
  justify-content: space-between;
  padding: 10px 15px;
}

.quantity-num {
  font-size: 12px;
}

.trash-btn {
  border-left: 1px solid #e8e8e8;
}

.trash-btn:hover {
  background-color: #f63a35;
  color: white;
}
</style>
