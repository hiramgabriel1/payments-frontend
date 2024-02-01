<script>
  //importacion del modal y del corredor de usuarios
  import ModalNewUser from "../components/ModalNewUser.svelte";
  import RecorridoUsers from "../components/RecorridoUsers.svelte";
  import clientesPagos from "./historial-pagos/+page.svelte";
  import clientesCancelados from "./clientes-cancelados/+page.svelte";
  import clientesPendiente from "./pagos-pendientes/+page.svelte";
  import ModalDetailUser from "../components/ModalDetailUser.svelte";



  let mostrarModal = false
  console.log(7, mostrarModal)
  let clienteSeleccionado = null
  console.log(1, clienteSeleccionado)
  let loading = true;
  let dataClients;
  async function getClients() {
    try {
      const clients = await fetch(
        "https://payments-api-jpt5.onrender.com/api/v1/"
      );
      let data = await clients.json();
      dataClients = data;
      loading = false;
      console.log(dataClients);
    } catch (error) {
      console.log(error);
    }
  }
  getClients();
  //total clientes
  //let data = []
  /*async function totalclientes(){
        try {
            let url = await fetch('https://payments-api-jpt5.onrender.com/api/v1/')
            let response = await url.json()
            data = response
            
        } catch (error) {
            console.error("error")
        }
    }
    totalclientes() */

  let searchTerm = "";
  let searchResults = [];

  const handleInput = (event) => {
    searchTerm = event.target.value.toLowerCase();
    if (searchTerm === "") {
      searchResults = [];
    }
  };

  const handleSearch = () => {
    searchResults = dataClients.data.filter((client) => {
      return client.username.toLowerCase().includes(searchTerm);
    });
  };

  const mostrarDetail = (client) => {
    clienteSeleccionado = client
    console.log(2, clienteSeleccionado)
    mostrarModal = true
    console.log(8, mostrarModal)
  }
</script>

<!-- modal -->
<section class="container px-4 mx-auto">
  <div class="sm:flex sm:items-center sm:justify-between">
    <div>
      <div class="flex items-center gap-x-3">
        <h2 class="text-lg font-medium text-gray-800 dark:text-white">
          Total clientes:
        </h2>
        <!--Numeros de clientes-->
        {#if dataClients}
          <span
            class="px-3 py-1 text-xs text-blue-600 bg-blue-100 rounded-full dark:bg-gray-800 dark:text-blue-400"
            >{dataClients.data.length} clientes</span
          >
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
      <ModalNewUser />
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
        <a href="/"> Clientes </a>
      </button>

      <button
        class="px-5 py-2 text-xs font-medium text-gray-600 transition-colors duration-200 sm:text-sm dark:hover:bg-gray-800 dark:text-gray-300 hover:bg-gray-100"
        on:click={clientesPendiente}
      >
        <a href="/pagos-pendientes"> Pagos pendientes </a>
      </button>

      <button
        class="px-5 py-2 text-xs font-medium text-gray-600 transition-colors duration-200 sm:text-sm dark:hover:bg-gray-800 dark:text-gray-300 hover:bg-gray-100"
        on:click={clientesCancelados}
      >
        <a href="/clientes-cancelados"> Clientes cancelados </a>
      </button>

      <button
        class="px-5 py-2 text-xs font-medium text-gray-600 transition-colors duration-200 sm:text-sm dark:hover:bg-gray-800 dark:text-gray-300 hover:bg-gray-100"
        on:click={clientesPagos}
      >
        <a href="/historial-pagos"> Historial de pagos </a>
      </button>

      <button
        class="px-5 py-2 text-xs font-medium text-gray-600 transition-colors duration-200 sm:text-sm dark:hover:bg-gray-800 dark:text-gray-300 hover:bg-gray-100"
        on:click={clientesPagos}
      >
        <a href="/pagos-siete-dias"> Cada 7 dias </a>
      </button>

      <button
        class="px-5 py-2 text-xs font-medium text-gray-600 transition-colors duration-200 sm:text-sm dark:hover:bg-gray-800 dark:text-gray-300 hover:bg-gray-100"
      >
        <a href="/pagos-catorce-dias"> Cada 14 dias </a>
      </button>
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
        placeholder="Search..."
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
                <!--Barra porcentae de pago-->
                <th
                  scope="col"
                  class="px-4 py-3.5 text-sm font-normal text-left rtl:text-right text-gray-500 dark:text-gray-400"
                  >Barra porcentaje pago</th
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
              <!--Recorrido de los usuarios usando un each en la pagina RecorridoUser.svelte-->
              <RecorridoUsers />

              <!-- Clientes -->             

              {#if loading}
                <tr>
                  <td colspan="6">Cargando...</td>
                </tr>
              {:else if searchResults.length > 0}
                {#each searchResults as client}
                  <tr>
                    <td class="px-4 py-2">{client._id}</td>
                    <td class="px-12 py-2">{client.username}</td>
                    <td class="px-4 py-2">{client.lastName}</td>
                    <td class="px-4 py-2">{client.total}</td>
                    <td class="px-4 py-2">
                      <progress max="100" value={(client.capitalPrestado / client.total) * 10}></progress>
                    </td>
                  </tr>
                {/each}
              {:else if dataClients}
              {#each dataClients.data as client}
              <tr>
                <td class="px-4 py-2">{client._id}</td>
                <td class="px-12 py-2">{client.username}</td>
                <td class="px-4 py-2">{client.lastName}</td>
                <td class="px-4 py-2">{client.total}</td>
                <td class="px-4 py-2">
                  {#if client.capitalPrestado === undefined}
                    <progress></progress>
                  {:else}
                    <progress max="100" value={(client.capitalPrestado / client.total) * 50}></progress>
                  {/if}
                </td>
                <td class="px-4 py-2">
                  <!-- Boton de ver detalles del cliente -->
                  <button on:click={() => mostrarDetail(client)} class="text-slate-800 hover:text-blue-600 text-sm bg-white hover:bg-slate-100 border border-slate-200 font-medium px-4 py-2 inline-flex space-x-1 items-center">
                    <span>
                      <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-4 h-4">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M2.036 12.322a1.012 1.012 0 010-.639C3.423 7.51 7.36 4.5 12 4.5c4.638 0 8.573 3.007 9.963 7.178.07.207.07.431 0 .639C20.577 16.49 16.64 19.5 12 19.5c-4.638 0-8.573-3.007-9.963-7.178z" />
                        <path stroke-linecap="round" stroke-linejoin="round" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
                      </svg>                      
                    </span>
                  </button>
                </td>
                <td class="px-4 py-2"><!-- Botones de edición --></td>
              </tr>
              <!-- Muestro el modal -->
              {#if mostrarModal && clienteSeleccionado._id === client._id}
                <ModalDetailUser {clienteSeleccionado} {mostrarModal} />
              {/if}
            {/each}
              {/if}
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>

  <div class="mt-6 sm:flex sm:items-center sm:justify-between">
    <div class="text-sm text-gray-500 dark:text-gray-400">
      Page <span class="font-medium text-gray-700 dark:text-gray-100"
        >1 of 10</span
      >
    </div>

    <div class="flex items-center mt-4 gap-x-4 sm:mt-0">
      <a
        href="$"
        class="flex items-center justify-center w-1/2 px-5 py-2 text-sm text-gray-700 capitalize transition-colors duration-200 bg-white border rounded-md sm:w-auto gap-x-2 hover:bg-gray-100 dark:bg-gray-900 dark:text-gray-200 dark:border-gray-700 dark:hover:bg-gray-800"
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
          stroke-width="1.5"
          stroke="currentColor"
          class="w-5 h-5 rtl:-scale-x-100"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            d="M6.75 15.75L3 12m0 0l3.75-3.75M3 12h18"
          />
        </svg>

        <span> previous </span>
      </a>

      <a
        href="$"
        class="flex items-center justify-center w-1/2 px-5 py-2 text-sm text-gray-700 capitalize transition-colors duration-200 bg-white border rounded-md sm:w-auto gap-x-2 hover:bg-gray-100 dark:bg-gray-900 dark:text-gray-200 dark:border-gray-700 dark:hover:bg-gray-800"
      >
        <span> Next </span>

        <svg
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
          stroke-width="1.5"
          stroke="currentColor"
          class="w-5 h-5 rtl:-scale-x-100"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            d="M17.25 8.25L21 12m0 0l-3.75 3.75M21 12H3"
          />
        </svg>
      </a>
    </div>
  </div>
</section>

<style>
</style>
