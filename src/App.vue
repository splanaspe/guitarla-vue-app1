<script setup>
  import {ref, reactive, onMounted, watch} from 'vue';
  import {db} from './data/guitarras'
  import Guitarra from './components/Guitarra.vue';
  import Header from './components/Header.vue';
  import Footer from './components/Footer.vue';

  const guitarras = ref(db);
  const carrito = ref([]);
  const guitarraHeader = ref({});

  watch(carrito, () => {
    guardarLocalStorage()
  },{
    deep:true // Entrara a todos los attr del carrito para ver cuando cambien
  })

  onMounted( () => {
    guitarras.value=db
    guitarraHeader.value=db[4]

    const carritoStorage = localStorage.getItem('carrito')
    if(carritoStorage) carrito.value = JSON.parse(carritoStorage)
  })

  const guardarLocalStorage = () => {
    localStorage.setItem('carrito', JSON.stringify(carrito.value))
  }

  const agregarCarrito = (guitarra) => {
    console.log("Producto agregado: " +guitarra.nombre)

    const existeCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id)
    if(existeCarrito >=0) {
      carrito.value[existeCarrito].cantidad++
    }else{
      guitarra.cantidad=1
      carrito.value.push(guitarra)
    }  // accedemos con .value porque estamos en el script
  }

  const decrementarCantidad = (id) =>{
    const index = carrito.value.findIndex(producto => producto.id === id)
    if(carrito.value[index].cantidad <= 1 ){
      eliminarProducto(id)
      return
    }
    carrito.value[index].cantidad--
  }

  const incrementarCantidad = (id) =>{
    const index = carrito.value.findIndex(producto => producto.id === id)
    if(carrito.value[index].cantidad > 4) return
    carrito.value[index].cantidad++
  }

  const eliminarProducto = (id) =>{
    carrito.value = carrito.value.filter(producto => producto.id != id)
  }

  const vaciarCarrito = () => {
    carrito.value = []
  }

</script>
<template>
  <Header
    :carrito="carrito"
    @incrementar-cantidad="incrementarCantidad"
    @decrementar-cantidad="decrementarCantidad"
    :guitarra="guitarraHeader"
    @agregar-carrito="agregarCarrito"
    @eliminar-producto="eliminarProducto"
    @vaciar-carrito="vaciarCarrito"
    ></Header>
    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>
        <div class="row mt-5">
          <Guitarra
            v-for="guitarra in guitarras"
            v-bind:guitarra="guitarra"
            @agregar-carrito="agregarCarrito"/>        
        </div>
    </main>
    <Footer></Footer>
</template>
