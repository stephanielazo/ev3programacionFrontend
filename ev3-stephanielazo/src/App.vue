<script setup>
import { reactive, computed } from 'vue'

// Importar imágenes
import audifonoImg from './assets/audifono.png'
import sillaImg from './assets/silla.png'
import tecladoImg from './assets/teclado.png'
import gabineteImg from './assets/gabinete.png'
import mouseImg from './assets/mouse.png'
import pantallaImg from './assets/pantalla.png'

// Lista de productos con precio y stock
const products = reactive([
  { id: 1, name: 'Audífono', price: 30000, stock: 3, img: audifonoImg },
  { id: 2, name: 'Mouse', price: 20000, stock: 5, img: mouseImg },
  { id: 3, name: 'Teclado', price: 15000, stock: 10, img: tecladoImg },
  { id: 4, name: 'Gabinete', price: 35000, stock: 4, img: gabineteImg },
  { id: 5, name: 'Pantalla', price: 175000, stock: 3, img: pantallaImg },
  { id: 6, name: 'Silla', price: 150000, stock: 2, img: sillaImg }
])

const cart = reactive({})

// Agregar producto al carrito
function addToCart(product) {
  if ((cart[product.id] ?? 0) < product.stock) {
    cart[product.id] = (cart[product.id] ?? 0) + 1
  } else {
    alert(`No hay más unidades disponibles para "${product.name}"`)
  }
}

// Remover producto del carrito
function removeFromCart(id) {
  if (cart[id] > 1) {
    cart[id]--
  } else {
    delete cart[id]
  }
}

// Calcular total a pagar
const total = computed(() => {
  return Object.entries(cart).reduce((acc, [id, qty]) => {
    const product = products.find(p => p.id === Number(id))
    return acc + (product ? product.price * qty : 0)
  }, 0)
})

// Formatear números con separador de miles
function formatPrice(value) {
  return value.toLocaleString('es-CL')
}
</script>

<template>
  <div class="container mt-4">
    <div class="row main-row">
      <!-- Columna izquierda: productos disponibles -->
      <div class="col-md-6">
        <h4 class="mb-3">Productos disponibles</h4>

        <div
          class="card mb-3"
          v-for="product in products"
          :key="product.id"
        >
          <div class="row g-0 align-items-center">
            <div class="col-4">
              <img
                :src="product.img"
                class="img-fluid img-producto"
                :alt="product.name"
              />
            </div>
            <div class="col-8">
              <div class="card-body">
                <h5 class="card-title">
                  {{ product.name }} - Precio: ${{ formatPrice(product.price) }}
                </h5>
                <button
                  class="btn btn-primary mt-2"
                  @click="addToCart(product)"
                >
                  Agregar al carrito
                </button>
              </div>
            </div>
          </div>
        </div>

        <!-- Total a pagar -->
        <h4 class="mt-3">
          <strong>Total a pagar: ${{ formatPrice(total) }}</strong>
        </h4>
      </div>

      <!-- Columna derecha: productos en el carrito -->
      <div class="col-md-6">
        <h4 class="mb-3 text-center">Productos en el carrito</h4>

        <div
          class="card mb-3"
          v-for="(qty, id) in cart"
          :key="id"
        >
          <div class="row g-0 align-items-center">
            <div class="col-4">
              <img
                :src="products.find(p => p.id === Number(id)).img"
                class="img-fluid carrito-img"
                :alt="products.find(p => p.id === Number(id)).name"
              />
            </div>
            <div class="col-8">
              <div class="card-body">
                <h6 class="card-title">
                  {{ products.find(p => p.id === Number(id)).name }}
                </h6>
                <p class="mb-1">Cantidad: {{ qty }}</p>
                <button
                  class="btn btn-primary btn-sm"
                  @click="removeFromCart(id)"
                >
                  🗑 Remover del carrito
                </button>
              </div>
            </div>
          </div>
        </div>

        <!-- Mensaje si el carrito está vacío -->
        <p v-if="Object.keys(cart).length === 0" class="text-muted text-center">
          No hay productos en el carrito.
        </p>
      </div>
    </div>
  </div>
</template>

<style scoped>
.img-producto {
  height: 100px;
  object-fit: contain;
  padding: 10px;
}

.carrito-img {
  height: 70px;
  object-fit: contain;
  padding: 5px;
}

.card {
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.1);
}

@media (min-width: 768px) {
  .main-row {
    display: flex;
    flex-wrap: nowrap;
    align-items: flex-start;
  }
}
</style>
