<script>
  import toast, { Toaster } from "svelte-french-toast";

  let modalEditar;
  let modalDetail;
  let modalDelete;
  let loading = true;
  let clients;
  let showDataPaymentToEdit = [];
  let total;

  let formData = {
    username: "",
    lastName: "",
    capitalPrestado: "",
    total: total,
    fechaPrestamo: "",
    fechaPago: "",
    paymentMethod: "",
    direccion: "",
    pagado: false,
    cancelado: false,
  };

  async function getClients() {
    try {
      const response = await fetch(
        "https://payments-api-jpt5.onrender.com/api/v1/"
      );
      const data = await response.json();
      let clientsFourteenDays = data.data.filter(
        (client) =>
          client.modalityPayment === "quincenal" && client.pagado !== true
      );
      clients = clientsFourteenDays;
      loading = false;
      console.log(clients);
    } catch (error) {
      console.error("Error al obtener los clientes:", error);
    }
  }

  function calcularFechasDePago(client) {
    const { fechaPrestamo, fechaPago } = client;

    let fechaInicioPago = new Date(fechaPrestamo);
    const fechaFinalPago = new Date(fechaPago);

    let fechaInicioPrestamo = new Date(fechaInicioPago);
    fechaInicioPrestamo.setDate(fechaInicioPrestamo.getDate() + 14);

    const fechasDePago = [];

    while (fechaInicioPrestamo < fechaFinalPago) {
      fechasDePago.push(new Date(fechaInicioPrestamo));
      fechaInicioPrestamo.setDate(fechaInicioPrestamo.getDate() + 14);
    }
    console.log(fechasDePago);
    return fechasDePago;
  }
  getClients();

  //Función que elimina usuario
  export async function deleteClientsPendientes(idDelete) {
    const response = await fetch(
      `https://payments-api-jpt5.onrender.com/api/v1/delete-user/${idDelete}`,
      {
        method: "DELETE",
        headers: { "Content-Type": "application/json" },
      }
    );
    const deletedClient = await response.json();
    console.log("cliente eliminado con exito " + deletedClient);
    modalDelete = false;
  }

  const validateCount = (e) => {
    const event = e.target.value;

    console.log(event);
    if (event <= 0) {
      return toast.error("No puedes ingresar valores menores a 1");
    }
  };

  // todo: editar usuario
  let patchUser;
  let idUser;
  async function actualizarUser() {
    try {
      if (formData.capitalPrestado > formData.total) {
        toast.error("Error");

        console.log("re pelotuod pelotudo");
        return;
      } else {
        formData.total =
          formData.capitalPrestado -
          showDataPaymentToEdit.reduce((acc, client) => acc + client.total, 0);

        formData.total = Math.abs(formData.total);

        if (formData.total < 1 || formData.total == 0) {
          console.log("es 0");
          console.log((formData.pagado = true));
          // formData.pagado = true
        }

        console.log(total);
        patchUser = await fetch(
          `https://payments-api-jpt5.onrender.com/api/v1/edit-user/${idUser}`,
          {
            method: "PATCH",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify(formData),
          }
        );
        const clientActualizado = await patchUser.json();
        console.log(clientActualizado);

        toast.success("Usuario editado");

        modalEditar = false;
        window.location.reload();
      }
    } catch (error) {
      console.log(error);
    }
  }

  // todo: modificar pagos de usuario
  const addPayments = (client) => {
    idUser = client._id;
    let clientArrayInfo = [client];
    showDataPaymentToEdit = clientArrayInfo;

    console.log(showDataPaymentToEdit);
    modalEditar = true;
  };

  /* Search */
  let searchTerm = "";
  let searchResults = [];

  const handleInput = (event) => {
    searchTerm = event.target.value.toLowerCase();
    if (searchTerm === "") {
      searchResults = [];
    }
  };

  const handleSearch = () => {
    searchResults = clients.filter((client) => {
      return client.username.toLowerCase().includes(searchTerm);
    });
    console.log(searchResults);
  };

  //Eliminar usuario
  let clienteDetail = [];
  const mostrarModalDetail = (client) => {
    let clienteDetailArray = [client];
    clienteDetail = clienteDetailArray;
    modalDetail = true;
  };

  //Actualizar usuario
  let clienteDelete;
  const mostrarModalDelete = (client) => {
    let clienteDeleteArray = [client];
    clienteDelete = clienteDeleteArray;
    modalDelete = true;
  };
</script>

<Toaster />

<section class="container mt-24 px-4 mx-auto">
  <div class="sm:flex sm:items-center sm:justify-between">
    <div>
      <div class="flex items-center gap-x-3">
        <h2 class="text-lg font-medium text-gray-800 dark:text-white">
          Total clientes:
        </h2>
        <!--Numeros de clientes-->
        {#if clients}
          <span
            class="px-3 py-1 text-xs text-blue-600 bg-blue-100 rounded-full dark:bg-gray-800 dark:text-blue-400"
          >
            {clients.length}
            {clients.length === 1 ? "cliente" : "clientes"}
          </span>
        {:else}
          <span
            class="px-3 py-1 text-xs text-blue-600 bg-blue-100 rounded-full dark:bg-gray-800 dark:text-blue-400"
            >0 clientes</span
          >
        {/if}
      </div>
    </div>

    <div class="flex items-center mt-4 gap-x-3">
      <!--Modal creacion de nuevos usuarios-->
    </div>
  </div>

  <div class="mt-6 md:flex md:items-center md:justify-between">
    <!--Grupos-->
    <div
      class="inline-flex overflow-hidden bg-white border divide-x rounded-lg dark:bg-gray-900 rtl:flex-row-reverse dark:border-gray-700 dark:divide-gray-700"
    >
      <button
        class="px-5 py-2 text-xs font-medium text-gray-600 transition-colors duration-200 bg-gray-100 sm:text-sm dark:bg-gray-800 dark:text-gray-300"
      >
        <a href="/">Clientes</a>
      </button>

      <button
        class="px-5 py-2 text-xs font-medium text-gray-600 transition-colors duration-200 sm:text-sm dark:hover:bg-gray-800 dark:text-gray-300 hover:bg-gray-100"
      >
        <a href="/pagos-pendientes">Pagos pendientes</a>
      </button>

      <button
        class="px-5 py-2 text-xs font-medium text-gray-600 transition-colors duration-200 sm:text-sm dark:hover:bg-gray-800 dark:text-gray-300 hover:bg-gray-100"
      >
        <a href="/clientes-cancelados">Clientes cancelados</a>
      </button>

      <button
        class="px-5 py-2 text-xs font-medium text-gray-600 transition-colors duration-200 sm:text-sm dark:hover:bg-gray-800 dark:text-gray-300 hover:bg-gray-100"
      >
        <a href="/historial-pagos">Historial de pagos</a>
      </button>

      <button
        class="px-5 py-2 text-xs font-medium text-gray-600 transition-colors duration-200 sm:text-sm dark:hover:bg-gray-800 dark:text-gray-300 hover:bg-gray-100"
      >
        <a href="grupo-uno">Armandina</a>
      </button>

      <button
        class="px-5 py-2 text-xs font-medium text-gray-600 transition-colors duration-200 sm:text-sm dark:hover:bg-gray-800 dark:text-gray-300 hover:bg-gray-100"
      >
        San Juana
      </button>

      <button
        class="px-5 py-2 text-xs font-medium text-gray-600 transition-colors duration-200 sm:text-sm dark:hover:bg-gray-800 dark:text-gray-300 hover:bg-gray-100"
      >
        <a href="grupo-tres">Tianguis</a>
      </button>
      <a
        class="px-5 py-2 text-xs font-medium text-gray-600 transition-colors duration-200 sm:text-sm dark:hover:bg-gray-800 dark:text-gray-300 hover:bg-gray-100"
        href="/pagos-cercanos"
      >
        Pagos cercanos
      </a>
    </div>

    <!--Search-->
    <div class="relative flex items-center mt-4 md:mt-0">
      <button on:click={handleSearch} type="submit" class="absolute">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
          stroke-width="1.5"
          stroke="currentColor"
          class="w-5 h-5 mx-3 text-gray-400 dark:text-gray-600"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            d="M21 21l-5.197-5.197m0 0A7.5 7.5 0 105.196 5.196a7.5 7.5 0 0010.607 10.607z"
          />
        </svg>
      </button>

      <input
        on:input={handleInput}
        type="text"
        placeholder="Buscar cliente..."
        class="block w-full py-1.5 pr-5 text-gray-700 bg-white border border-gray-200 rounded-lg md:w-80 placeholder-gray-400/70 pl-11 rtl:pr-11 rtl:pl-5 dark:bg-gray-900 dark:text-gray-300 dark:border-gray-600 focus:border-blue-400 dark:focus:border-blue-300 focus:ring-blue-300 focus:outline-none focus:ring focus:ring-opacity-40"
      />
    </div>
  </div>

  <!--tabla clientes-->
  <div class="flex flex-col mt-6">
    <div class="-mx-4 -my-2 overflow-x-auto sm:-mx-6 lg:-mx-8">
      <div class="inline-block min-w-full py-2 align-middle md:px-6 lg:px-8">
        <div
          class="overflow-hidden border border-gray-200 dark:border-gray-700 md:rounded-lg"
        >
          <table
            class="min-w-full divide-y divide-gray-200 dark:divide-gray-700"
          >
            <thead class="bg-gray-50 dark:bg-gray-800">
              <tr>
                <th
                  scope="col"
                  class="py-3.5 px-4 text-sm font-normal text-left rtl:text-right text-gray-500 dark:text-gray-400"
                >
                  <button class="flex items-center gap-x-3 focus:outline-none">
                    <span>Id cliente</span>
                    <svg
                      class="h-3"
                      viewBox="0 0 10 11"
                      fill="none"
                      xmlns="http://www.w3.org/2000/svg"
                    >
                      <!-- SVG Path -->
                    </svg>
                  </button>
                </th>
                <!--Name-->
                <th
                  scope="col"
                  class="px-12 py-3.5 text-sm font-normal text-left rtl:text-right text-gray-500 dark:text-gray-400"
                >
                  Nombre
                </th>
                <!--Apellido-->
                <th
                  scope="col"
                  class="px-4 py-3.5 text-sm font-normal text-left rtl:text-right text-gray-500 dark:text-gray-400"
                >
                  Apellido
                </th>
                <!--Monto dinero prestado-->
                <th
                  scope="col"
                  class="px-4 py-3.5 text-sm font-normal text-left rtl:text-right text-gray-500 dark:text-gray-400"
                  >Monto prestamo</th
                >
                <th
                  scope="col"
                  class="px-4 py-3.5 text-sm font-normal text-left rtl:text-right text-gray-500 dark:text-gray-400"
                  >Dias de pago</th
                >
                <!--Barra porcentae de pago-->
                <th
                  scope="col"
                  class="px-4 py-3.5 text-sm font-normal text-left rtl:text-right text-gray-500 dark:text-gray-400"
                  >Opciones</th
                >
                <th scope="col" class="relative py-3.5 px-4">
                  <span class="sr-only">Edit</span>
                </th>
              </tr>
            </thead>
            <tbody
              id="tbodyPrincipal"
              class="bg-white divide-y divide-gray-200 dark:divide-gray-700 dark:bg-gray-900"
            >
              <!-- Clientes -->
              {#if loading}
                <tr>
                  <td colspan="6">Cargando...</td>
                </tr>
              {:else if searchResults.length > 0}
                {#each searchResults as client}
                  {#if client.modalityPayment === "quincenal"}
                    <tr>
                      <td
                        class="px-4 py-8 text-sm font-medium text-gray-800 dark:text-white"
                        >{client._id}</td
                      >
                      <td class="px-12 py-8 text-gray-700 text-sm"
                        >{client.username}</td
                      >
                      <td class="px-4 py-8 text-gray-700 text-sm"
                        >{client.lastName}</td
                      >
                      <td class="px-4 py-8 text-gray-700 text-sm"
                        >{client.total}</td
                      >
                      <td class="px-4 py-8 text-gray-700 text-sm">
                        {client.fechaPrestamo.slice(0, 10)}
                        <br />
                        {client.fechaPago.slice(0, 10)}
                      </td>
                      <td class="px-4 py-2">
                        <!-- Boton de ver detalles del cliente -->
                        <button
                          on:click={() => mostrarModalDetail(client)}
                          class="text-slate-800 hover:text-blue-600 text-sm bg-white hover:bg-slate-100 border border-slate-200 font-medium px-4 py-2 inline-flex space-x-1 items-center"
                        >
                          <span>
                            <svg
                              xmlns="http://www.w3.org/2000/svg"
                              fill="none"
                              viewBox="0 0 24 24"
                              stroke-width="1.5"
                              stroke="currentColor"
                              class="w-4 h-4"
                            >
                              <path
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                d="M2.036 12.322a1.012 1.012 0 010-.639C3.423 7.51 7.36 4.5 12 4.5c4.638 0 8.573 3.007 9.963 7.178.07.207.07.431 0 .639C20.577 16.49 16.64 19.5 12 19.5c-4.638 0-8.573-3.007-9.963-7.178z"
                              />
                              <path
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"
                              />
                            </svg>
                          </span>
                        </button>

                        <!--Eliminar usser-->
                        <button
                          on:click={() => mostrarModalDelete(client)}
                          class="text-slate-800 hover:text-blue-600 text-sm bg-white hover:bg-slate-100 border border-slate-200 rounded-r-lg font-medium px-4 py-2 inline-flex space-x-1 items-center"
                        >
                          <span>
                            <svg
                              xmlns="http://www.w3.org/2000/svg"
                              fill="none"
                              viewBox="0 0 24 24"
                              stroke-width="1.5"
                              stroke="currentColor"
                              class="w-4 h-4"
                            >
                              <path
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                d="M14.74 9l-.346 9m-4.788 0L9.26 9m9.968-3.21c.342.052.682.107 1.022.166m-1.022-.165L18.16 19.673a2.25 2.25 0 01-2.244 2.077H8.084a2.25 2.25 0 01-2.244-2.077L4.772 5.79m14.456 0a48.108 48.108 0 00-3.478-.397m-12 .562c.34-.059.68-.114 1.022-.165m0 0a48.11 48.11 0 013.478-.397m7.5 0v-.916c0-1.18-.91-2.164-2.09-2.201a51.964 51.964 0 00-3.32 0c-1.18.037-2.09 1.022-2.09 2.201v.916m7.5 0a48.667 48.667 0 00-7.5 0"
                              />
                            </svg>
                          </span>
                        </button>
                      </td>
                    </tr>
                  {/if}
                {/each}
              {:else if clients.length > 0}
                {#each clients as client}
                  {#if client.modalityPayment === "quincenal"}
                    <tr>
                      <td
                        class="px-4 py-8 text-sm font-medium text-gray-800 dark:text-white"
                        >{client._id}</td
                      >
                      <td class="px-12 py-8 text-gray-700 text-sm"
                        >{client.username}</td
                      >
                      <td class="px-4 py-8 text-gray-700 text-sm"
                        >{client.lastName}</td
                      >
                      <td class="px-4 py-8 text-gray-700 text-sm"
                        >{client.total}</td
                      >
                      <td class="px-4 py-8 text-gray-700 text-sm">
                        {client.fechaPrestamo.slice(0, 15)}
                        <br />
                        {client.fechaPago.slice(0, 15)}
                      </td>
                      <td class="px-4 py-2">
                        <!-- Boton de ver detalles del cliente -->
                        <button
                          on:click={() => mostrarModalDetail(client)}
                          class="text-slate-800 hover:text-blue-600 text-sm bg-white hover:bg-slate-100 border border-slate-200 font-medium px-4 py-2 inline-flex space-x-1 items-center"
                        >
                          <span>
                            <svg
                              xmlns="http://www.w3.org/2000/svg"
                              fill="none"
                              viewBox="0 0 24 24"
                              stroke-width="1.5"
                              stroke="currentColor"
                              class="w-4 h-4"
                            >
                              <path
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                d="M2.036 12.322a1.012 1.012 0 010-.639C3.423 7.51 7.36 4.5 12 4.5c4.638 0 8.573 3.007 9.963 7.178.07.207.07.431 0 .639C20.577 16.49 16.64 19.5 12 19.5c-4.638 0-8.573-3.007-9.963-7.178z"
                              />
                              <path
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"
                              />
                            </svg>
                          </span>
                        </button>

                        <!--Eliminar user-->
                        <button
                          on:click={() => mostrarModalDelete(client)}
                          class="text-slate-800 hover:text-blue-600 text-sm bg-white hover:bg-slate-100 border border-slate-200 rounded-r-lg font-medium px-4 py-2 inline-flex space-x-1 items-center"
                        >
                          <span>
                            <svg
                              xmlns="http://www.w3.org/2000/svg"
                              fill="none"
                              viewBox="0 0 24 24"
                              stroke-width="1.5"
                              stroke="currentColor"
                              class="w-4 h-4"
                            >
                              <path
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                d="M14.74 9l-.346 9m-4.788 0L9.26 9m9.968-3.21c.342.052.682.107 1.022.166m-1.022-.165L18.16 19.673a2.25 2.25 0 01-2.244 2.077H8.084a2.25 2.25 0 01-2.244-2.077L4.772 5.79m14.456 0a48.108 48.108 0 00-3.478-.397m-12 .562c.34-.059.68-.114 1.022-.165m0 0a48.11 48.11 0 013.478-.397m7.5 0v-.916c0-1.18-.91-2.164-2.09-2.201a51.964 51.964 0 00-3.32 0c-1.18.037-2.09 1.022-2.09 2.201v.916m7.5 0a48.667 48.667 0 00-7.5 0"
                              />
                            </svg>
                          </span>
                        </button>
                      </td>
                      <td class="px-4 py-2">
                        <!--EDITAR user-->
                        <button
                          on:click={() => addPayments(client)}
                          class="text-slate-800 hover:text-blue-600 text-sm bg-white hover:bg-slate-100 border border-slate-200 rounded-r-lg font-medium px-4 py-2 inline-flex space-x-1 items-center"
                        >
                          <span>
                            <svg
                              xmlns="http://www.w3.org/2000/svg"
                              fill="none"
                              viewBox="0 0 24 24"
                              stroke-width="1.5"
                              stroke="currentColor"
                              class="w-4 h-4"
                            >
                              <path
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                d="M16.862 4.487l1.687-1.688a1.875 1.875 0 112.652 2.652L10.582 16.07a4.5 4.5 0 01-1.897 1.13L6 18l.8-2.685a4.5 4.5 0 011.13-1.897l8.932-8.931zm0 0L19.5 7.125M18 14v4.75A2.25 2.25 0 0115.75 21H5.25A2.25 2.25 0 013 18.75V8.25A2.25 2.25 0 015.25 6H10"
                              />
                            </svg>
                          </span>
                        </button>
                      </td>
                    </tr>
                  {/if}
                {/each}
              {:else}
                <tr>
                  <td colspan="6">No hay datos</td>
                </tr>
              {/if}
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</section>

<!-- Modal Detalles del cliente -->
{#if modalDetail}
  {#each clienteDetail as client}
    <div
      class="modal fixed inset-0 flex items-center justify-center bg-gray-100"
    >
      <div class="modal-overlay absolute w-full h-full"></div>

      <div class="z-10 bg-white rounded-lg shadow-lg w-2/3 relative">
        <button
          on:click={() => (modalDetail = false)}
          class="close absolute top-0 right-0 mr-4 mt-4 text-gray-700 hover:text-gray-900"
        >
          <svg
            class="h-6 w-6"
            fill="none"
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            viewBox="0 0 24 24"
            stroke="currentColor"
          >
            <path d="M6 18L18 6M6 6l12 12"></path>
          </svg>
        </button>

        <div class="p-8 z-10">
          <h2 class="text-2xl mb-4">
            Información del Cliente: {client.username}
          </h2>
          <hr class="mb-4" />
          <h2 class="text-xl mb-2">Nombre: {client.username}</h2>
          <h2 class="text-xl mb-2">Apellido: {client.lastName}</h2>
          <!-- <p class="text-lg mb-2">Capital prestado: {client.capitalPrestado}</p> -->
          <p class="text-lg mb-2">
            Fecha del prestamo: {client.fechaPrestamo}
          </p>
          <p class="text-lg mb-2">
            Fecha limite de pago: {client.fechaPago}
          </p>
          <p class="text-lg mb-2">Metodo de pago: {client.paymentMethod}</p>
          <p class="text-lg mb-2">Dirección: {client.direccion}</p>
          <p class="text-lg mb-2">Total: {client.total}</p>
          <p class="text-lg mb-2">Pagado: {client.pagado ? "si" : "no"}</p>
          <p class="text-lg mb-2">ID: {client._id}</p>
        </div>
      </div>
    </div>
  {/each}
{/if}

<!-- Modal Eliminar cliente -->
{#if modalDelete}
  {#each clienteDelete as client}
    <div
      class="modal fixed inset-0 flex items-center justify-center bg-gray-100"
    >
      <div class="modal-overlay absolute w-full h-full"></div>

      <div class="z-10 bg-white rounded-lg shadow-lg w-2/3">
        <div class="p-8">
          <h2 class="text-2xl text-center mb-4">
            Estas seguro de Eliminar a: {client.username}
            {client.lastName}
          </h2>
        </div>
        <div class="flex justify-between px-8 pb-8">
          <div>
            <button
              on:click={() => deleteClientsPendientes(client._id)}
              class="bg-red-500 hover:bg-red-600 text-white font-bold py-2 px-4 rounded"
              >Si</button
            >
          </div>
          <div>
            <button
              on:click={() => (modalDelete = false)}
              class="bg-gray-400 hover:bg-gray-500 text-white font-bold py-2 px-4 rounded"
              >Volver</button
            >
          </div>
        </div>

        <button
          class="close absolute top-0 right-0 mr-4 mt-4 text-gray-700 hover:text-gray-900"
          on:click={() => (modalDelete = false)}
        >
          <svg
            class="h-6 w-6"
            fill="none"
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            viewBox="0 0 24 24"
            stroke="currentColor"
          >
            <path d="M6 18L18 6M6 6l12 12"></path>
          </svg>
        </button>
      </div>
    </div>
  {/each}
{/if}

<!-- editar -->
{#if modalEditar}
  {#each showDataPaymentToEdit as clientRender}
    <!--Comienzo del modal-->
    <form on:submit|preventDefault={actualizarUser}>
      <div
        class="py-12 transition duration-150 ease-in-out z-10 absolute top-0 right-0 bottom-0 left-0"
        id="modal"
        style="top: {window.scrollY}px;"
      >
        <div role="alert" class="container mx-auto w-11/12 md:w-2/3 max-w-lg">
          <div
            class="relative py-8 px-5 md:px-10 bg-white shadow-md rounded border border-gray-400"
          >
            <div class="w-full flex justify-start text-gray-600 mb-3">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                class="icon icon-tabler icon-tabler-wallet"
                width="52"
                height="52"
                viewBox="0 0 24 24"
                stroke-width="1"
                stroke="currentColor"
                fill="none"
                stroke-linecap="round"
                stroke-linejoin="round"
              >
                <path stroke="none" d="M0 0h24v24H0z" />
                <path
                  d="M17 8v-3a1 1 0 0 0 -1 -1h-10a2 2 0 0 0 0 4h12a1 1 0 0 1 1 1v3m0 4v3a1 1 0 0 1 -1 1h-12a2 2 0 0 1 -2 -2v-12"
                />
                <path d="M20 12v4h-4a2 2 0 0 1 0 -4h4" />
              </svg>
            </div>
            <h1
              class="text-gray-800 font-lg font-bold tracking-normal leading-tight mb-4"
            >
              Añadir pago
            </h1>
            <!--Nombre-->
            <label
              for="name"
              class="text-gray-800 text-sm font-bold leading-tight tracking-normal"
              >Nombre</label
            >
            <input
              bind:value={formData.username}
              id="username"
              class="mb-5 mt-2 text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal w-full h-10 flex items-center pl-3 text-sm border-gray-300 rounded border"
              placeholder={clientRender.username}
            />

            <!--Apellido-->
            <label
              for="apellido"
              class="text-gray-800 text-sm font-bold leading-tight tracking-normal"
              >Apellido</label
            >
            <input
              bind:value={formData.lastName}
              id="lastName"
              class="mb-5 mt-2 text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal w-full h-10 flex items-center pl-3 text-sm border-gray-300 rounded border"
              placeholder="Gonzales"
            />

            <!--Monto del prestamo-->
            <label
              for="montoPrestamo"
              class="text-gray-800 text-sm font-bold leading-tight tracking-normal"
            >
              Pago quincenal
            </label>
            <div class="relative mb-5 mt-2">
              <div
                class="absolute text-gray-600 flex items-center px-4 border-r h-full"
              >
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  class="icon icon-tabler icon-tabler-credit-card"
                  width="20"
                  height="20"
                  viewBox="0 0 24 24"
                  stroke-width="1.5"
                  stroke="currentColor"
                  fill="none"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                >
                  <path stroke="none" d="M0 0h24v24H0z" />
                  <rect x="3" y="5" width="18" height="14" rx="3" />
                  <line x1="3" y1="10" x2="21" y2="10" />
                  <line x1="7" y1="15" x2="7.01" y2="15" />
                  <line x1="11" y1="15" x2="13" y2="15" />
                </svg>
              </div>
              <input
                bind:value={formData.capitalPrestado}
                type="number"
                id="montoPrestamo"
                on:input={validateCount}
                class="text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal w-full h-10 flex items-center pl-16 text-sm border-gray-300 rounded border"
                placeholder="Monto"
              />
            </div>

            <label
              for="total"
              class="text-gray-800 text-sm font-bold leading-tight tracking-normal"
              >Total Restante:</label
            >
            <div class="relative mb-5 mt-2">
              <div
                class="absolute text-gray-600 flex items-center px-4 border-r h-full"
              >
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  class="icon icon-tabler icon-tabler-credit-card"
                  width="20"
                  height="20"
                  viewBox="0 0 24 24"
                  stroke-width="1.5"
                  stroke="currentColor"
                  fill="none"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                >
                  <path stroke="none" d="M0 0h24v24H0z" />
                  <rect x="3" y="5" width="18" height="14" rx="3" />
                  <line x1="3" y1="10" x2="21" y2="10" />
                  <line x1="7" y1="15" x2="7.01" y2="15" />
                  <line x1="11" y1="15" x2="13" y2="15" />
                </svg>
              </div>
              <input
                disabled
                type="number"
                id="montoPrestamo"
                min="0"
                class="text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal w-full h-10 flex items-center pl-16 text-sm border-gray-300 rounded border"
                placeholder={clientRender.total}
              />
            </div>

            <div class="pagados">
              <label
                for="pagadoCheckbox"
                class="text-gray-800 text-sm font-bold leading-tight tracking-normal"
                >Pagado</label
              >
              <input
                class="w-10 cursor-pointer"
                type="checkbox"
                id="pagadoCheckbox"
                checked={formData.pagado}
                on:change={() => (formData.pagado = !formData.pagado)}
              />

              <label
                for="canceladoCheckbox"
                class="text-gray-800 text-sm font-bold leading-tight tracking-normal"
                >Cancelado</label
              >
              <input
                class="w-10 cursor-pointer"
                type="checkbox"
                id="canceladoCheckbox"
                checked={formData.cancelado}
                on:change={() => (formData.cancelado = !formData.cancelado)}
              />
            </div>

            <div class="flex items-center justify-start w-full">
              <!--Btn guardar datos-->
              <button
                type="submit"
                class="focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-700 transition duration-150 ease-in-out hover:bg-indigo-600 bg-indigo-700 rounded text-white px-8 py-2 text-sm"
                >enviar</button
              >
              <button
                on:click={() => (modalEditar = false)}
                class="focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-gray-400 ml-3 bg-gray-100 transition duration-150 text-gray-600 ease-in-out hover:border-gray-400 hover:bg-gray-300 border rounded px-8 py-2 text-sm"
              >
                Cancelar
              </button>
            </div>
            <button
              on:click={() => (modalEditar = false)}
              class="cursor-pointer absolute top-0 right-0 mt-4 mr-5 text-gray-400 hover:text-gray-600 transition duration-150 ease-in-out rounded focus:ring-2 focus:outline-none focus:ring-gray-600"
              aria-label="close modal"
              type="button"
            >
              <svg
                xmlns="http://www.w3.org/2000/svg"
                class="icon icon-tabler icon-tabler-x"
                width="20"
                height="20"
                viewBox="0 0 24 24"
                stroke-width="2.5"
                stroke="currentColor"
                fill="none"
                stroke-linecap="round"
                stroke-linejoin="round"
              >
                <path stroke="none" d="M0 0h24v24H0z" />
                <line x1="18" y1="6" x2="6" y2="18" />
                <line x1="6" y1="6" x2="18" y2="18" />
              </svg>
            </button>
          </div>
        </div>
      </div>
    </form>
  {/each}
{/if}

<style>
  .modal {
    background-color: rgba(0, 0, 0, 0.2);
  }
</style>
