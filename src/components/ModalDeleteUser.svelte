<script>
    export let mostrarModalDelete;
    export let clienteSeleccionado;
    console.log(9, mostrarModalDelete);
    
    export async function deleteClientsPendientes(idDelete){
    const response = await fetch(`https://payments-api-jpt5.onrender.com/api/v1/delete-user/${idDelete}`,{
      method: "DELETE",
      headers:{'Content-Type': 'application/json'}
    })
    const deletedClient = await response.json()
    console.log('cliente eliminado con exito ' + deletedClient)
    cerrarModal()
}
  

    const cerrarModal = () => {
      mostrarModalDelete = false;
      console.log(10, mostrarModalDelete);
    };
  </script>
  
  <div class="{mostrarModalDelete ? 'block' : 'hidden'} modal fixed inset-0 flex items-center justify-center bg-gray-100">
    <div class="modal-overlay absolute w-full h-full"></div>

    <div class="z-10 bg-white rounded-lg shadow-lg w-2/3">
        <div class='p-8'>
            <h2 class='text-2xl text-center mb-4'>Estas seguro de Eliminar a: {clienteSeleccionado.username}</h2>
        </div>
        <div class='flex justify-between px-8 pb-8'>
            <div >
                <button on:click={() => deleteClientsPendientes(clienteSeleccionado._id)} class="bg-red-500 hover:bg-red-600 text-white font-bold py-2 px-4 rounded">Si</button>
            </div>
            <div>
                <button on:click={cerrarModal} class="bg-gray-400 hover:bg-gray-500 text-white font-bold py-2 px-4 rounded">Volver</button>
            </div>
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