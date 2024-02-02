<script>
  export let clienteSeleccionado;
  export let mostrarModal;
  console.log(9, mostrarModal) 

  const cerrarModal = () => {
    mostrarModal = false;
    console.log(10, mostrarModal)
  }
</script>

<div class="modal {mostrarModal ? 'visible' : 'invisible'}">
  <button class="close" on:click={cerrarModal}>X</button> 
  <div class="modal-content">
    <div id="modal-body">
      <h2>Información del Cliente</h2>
      {#if clienteSeleccionado}
        <p>ID: {clienteSeleccionado._id}</p>
        <p>Nombre: {clienteSeleccionado.username}</p>
        <p>Apellido: {clienteSeleccionado.lastName}</p>
      {/if}
    </div>
  </div>
</div>

<style>
  .modal {
    display: none;
    position: fixed;
    z-index: 1;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    overflow: auto;
    background-color: rgba(0, 0, 0, 0.4);
  }

  /* .modal-open {
    display: block;
  } */
    .close {
    position: absolute;
    top: 10px;
    right: 10px;
    cursor: pointer;
    font-size: 20px;
  }


    .modal-content {
        background-color: #fefefe;
        margin: 15% auto;
        padding: 20px;
        border-radius: 5px;
        width: 50%;
        height: 60%;
    }

    .visible {
        display: block;
    }

    .invisible {
        display: none;
    }
</style>
