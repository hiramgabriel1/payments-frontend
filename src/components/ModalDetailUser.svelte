<script>
  export let mostrarModalDetail;
  export let clienteSeleccionado;
  console.log(9, mostrarModalDetail);

  const cerrarModal = () => {
    mostrarModalDetail = false;
    console.log(10, mostrarModalDetail);
  };
</script>

<div class="{mostrarModalDetail ? 'block' : 'hidden'} modal fixed inset-0 flex items-center justify-center bg-gray-100">
	<div class="modal-overlay absolute w-full h-full"></div>
  
	<div class="z-10 bg-white rounded-lg shadow-lg w-2/3">
	  <div class='p-8 z-10'>
		<h2 class='text-2xl mb-4'>Información del Cliente: {clienteSeleccionado.username}</h2>
		<hr class="mb-4">
		{#if clienteSeleccionado}
		  <h2 class="text-xl mb-2">Nombre: {clienteSeleccionado.username}</h2>
		  <h2 class="text-xl mb-2">Apellido: {clienteSeleccionado.lastName}</h2>
		  <p class="text-lg mb-2">Capital prestado: {clienteSeleccionado.capitalPrestado}</p>
		  <p class="text-lg mb-2">Fecha del prestamo: {clienteSeleccionado.fechaPrestamo.slice(0, 10)}</p>
		  <p class="text-lg mb-2">Proxima fecha de pago: {clienteSeleccionado.fechaPago.slice(0, 10)}</p>
		  <p class="text-lg mb-2">Metodo de pago: {clienteSeleccionado.paymentMethod}</p>
		  <p class="text-lg mb-2">Total: {clienteSeleccionado.total}</p>
		  <p class="text-lg mb-2">Pagado: {clienteSeleccionado.pagado}</p>
		  <p class="text-lg mb-2">ID: {clienteSeleccionado._id}</p>
		{/if}
	  </div>
	  
	  <button class="close absolute top-0 right-0 mr-4 mt-4 text-gray-700 hover:text-gray-900" on:click={cerrarModal}>
		<svg class="h-6 w-6" fill="none" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" viewBox="0 0 24 24" stroke="currentColor">
		  <path d="M6 18L18 6M6 6l12 12"></path>
		</svg>
	  </button>
	</div>
  </div>

<style>
	.modal{
		background-color: rgba(0, 0, 0, 0.4);
	}
</style>