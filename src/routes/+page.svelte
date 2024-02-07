<script>
  //importacion del modal y del corredor de usuarios
  import ModalNewUser from "../components/ModalNewUser.svelte";
  import RecorridoUsers from "../components/RecorridoUsers.svelte";
  import clientesPagos from "./historial-pagos/+page.svelte";
  import clientesCancelados from "./clientes-cancelados/+page.svelte";
  import clientesPendiente from "./pagos-pendientes/+page.svelte";
  import ModalDetailUser from "../components/ModalDetailUser.svelte";

  // let dataNewClient = {
  //     "nombre": name,
  //     "capitalPrestado": monto,
  //     "total": total,
  //     "fechaPrestamo": fechaPrestamo,
  //     "fechaPago": fechaPago,
  //     "apellido": apellido,
  //     "banco": banco
  // }
  // api(dataNewClient)
  // <button on:click={
  // ()=>{
  // let name = document.getElementById("name").value
  // let monto = parseInt(document.getElementById("montoPrestamo").value);
  // let fechaPrestamo = document.getElementById("fechaPrestamo").value;
  // let fechaPago = document.getElementById("fechaMaximoPago").value;
  // let numeroComoCadena = monto.toFixed(3);
  // console.log(numeroComoCadena)
  // let calculo = (parseFloat(numeroComoCadena) * 0.15) + (parseFloat(numeroComoCadena));
  // let total = calculo.toFixed(3)
  // console.log(total)
  // let apellido = document.getElementById("apellido").value;
  // let banco = document.getElementById("nombreBanco").value;
  // let dataNewClient = {
  //     "nombre": name,
  //     "capitalPrestado": monto,
  //     "total": total,
  //     "fechaPrestamo": fechaPrestamo,
  //     "fechaPago": fechaPago,
  //     "apellido": apellido,
  //     "banco": banco
  // }
  // api(dataNewClient)
  //     }
  // } class="focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-700 transition duration-150 ease-in-out hover:bg-indigo-600 bg-indigo-700 rounded text-white px-8 py-2 text-sm">Agregar</button>
  let formData = {
    username: "",
    lastName: "",
    capitalPrestado: "",
    total: "",
    fechaPrestamo: "",
    fechaPago: "",
    paymentMethod: "",
  };


  let mostrarModalDetail = false;
  let clienteSeleccionado = null;
  let loading = true;
  let dataClients;

  async function getClients() {
    try {
      const clients = await fetch("https://payments-api-jpt5.onrender.com/api/v1/");
      let data = await clients.json();
      dataClients = data;
      loading = false;
      console.log(dataClients);
    } catch (error) {
      console.log(error);
    }
  }

  getClients();

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
    clienteSeleccionado = client;
    mostrarModalDetail = true;
  };

  // show modal
  let modalForm;

  const submitDataUser = async () => {
    try {
      let fechaPrestamo = formData.fechaPrestamo;
      let fechaPago = formData.fechaMaximoPago;

      const dataNew = {
        /* convertMontoPrestamo: parseInt(formData.montoPrestamo), */
        username: formData.username,
        lastName: formData.lastName,
        capitalPrestado: formData.capitalPrestado,
        total: formData.total,
        fechaPrestamo: new Date(fechaPrestamo),
        fechaPago: new Date(fechaPago),
        paymentMethod: formData.paymentMethod,
      };
      console.log(dataNew)
      // console.log(typeof dataNew.convertMontoPrestamo);

      const response = await fetch("https://payments-api-jpt5.onrender.com/api/v1/create-user",
        {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify(dataNew),
        }
      );
      modalForm = false
      console.log(response)
      response.ok ? console.log("funciona") : console.log("no funciona lptm");

    } catch (error) {
      console.error(error);
    }
  };

</script>

<!-- modal -->
<section class="container px-4 mx-auto">
  <div class="sm:flex sm:items-center sm:justify-between">

    <div>
      <div class="flex items-center gap-x-3">
        <h2 class="flex text-lg font-medium text-gray-800 dark:text-white">
          Total clientes:
        </h2>
        <!--Numeros de clientes-->
        {#if dataClients}
       
          <span
          class="pl-2 py-1 w-20 text-xs text-blue-600 bg-blue-100 rounded-full dark:bg-gray-800 dark:text-blue-400"
          >{dataClients.data.length} clientes</span>

        {:else}
          <span
            class="flex px-3 py-1 text-xs text-blue-600 bg-blue-100 rounded-full dark:bg-gray-800 dark:text-blue-400"
            >0 clientes</span
          >
        {/if}
      </div>
    </div>

    <div class="w-full flex justify-end py-12" id="button">
      <button on:click={() => (modalForm = true)} class="focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-700 transition duration-150 ease-in-out hover:bg-indigo-600 bg-indigo-700 rounded text-white px-4 sm:px-8 py-2 text-xs sm:text-sm" onclick="modalHandler(true)">Agregar cliente</button>
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
            <RecorridoUsers />
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

<!-- modal here -->
{#if modalForm}
  <div
    class="py-12 transition duration-150 ease-in-out z-10 absolute top-0 right-0 bottom-0 left-0"
    id="modal"
  >
    <form
      on:submit|preventDefault={submitDataUser}
      role="alert"
      class="container mx-auto w-11/12 md:w-2/3 max-w-lg"
    >
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
          Agregar nuevo usuario
        </h1>

        <!--Nombre-->
        <label
          for="name"
          class="text-gray-800 text-sm font-bold leading-tight tracking-normal"
          >Nombre</label
        >
        <input
          id="name"
          class="mb-5 mt-2 text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal w-full h-10 flex items-center pl-3 text-sm border-gray-300 rounded border"
          bind:value={formData.username}
          placeholder="James"
        />

        <!--Apellido-->
        <label
          for="apellido"
          class="text-gray-800 text-sm font-bold leading-tight tracking-normal"
          >Apellido</label
        >
        <input
          id="apellido"
          class="mb-5 mt-2 text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal w-full h-10 flex items-center pl-3 text-sm border-gray-300 rounded border"
          bind:value={formData.lastName}
          placeholder="Gonzales"
        />

        <!--Monto del prestamo-->
        <label
          for="montoPrestamo"
          class="text-gray-800 text-sm font-bold leading-tight tracking-normal"
          >Capital prestado</label>
        <div class="relative mb-5 mt-2">
          <div class="absolute text-gray-600 flex items-center px-4 border-r h-full">
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
            type="text"
            id="montoPrestamo"
            class="text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal w-full h-10 flex items-center pl-16 text-sm border-gray-300 rounded border"
            bind:value={formData.capitalPrestado}
            placeholder="Monto prestamo"
          />
        </div>

        <label
          for="montoPrestamo"
          class="text-gray-800 text-sm font-bold leading-tight tracking-normal"
          >Total</label>
        <div class="relative mb-5 mt-2">
          <div class="absolute text-gray-600 flex items-center px-4 border-r h-full">
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
            type="number"
            id="montoPrestamo"
            class="text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal w-full h-10 flex items-center pl-16 text-sm border-gray-300 rounded border"
            bind:value={formData.total}
            placeholder="Monto total"
          />
        </div>

        <!--Fecha de prestamo-->
        <label
          for="fechaPrestamo"
          class="text-gray-800 text-sm font-bold leading-tight tracking-normal"
          >Fecha del prestamo</label
        >
        <div class="relative mb-5 mt-2">
          <div
            class="absolute right-0 text-gray-600 flex items-center pr-3 h-full cursor-pointer"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg"
              class="icon icon-tabler icon-tabler-calendar-event"
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
              <rect x="4" y="5" width="16" height="16" rx="2" />
              <line x1="16" y1="3" x2="16" y2="7" />
              <line x1="8" y1="3" x2="8" y2="7" />
              <line x1="4" y1="11" x2="20" y2="11" />
              <rect x="8" y="15" width="2" height="2" />
            </svg>
          </div>
          <input
            type="date"
            id="fechaPrestamo"
            class="text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal w-full h-10 flex items-center pl-3 text-sm border-gray-300 rounded border"
            bind:value={formData.fechaPrestamo}
            placeholder="MM/YY"
          />
        </div>

        <!--Fecha maxima de pago-->
        <label
          for="fechaMaximoPago"
          class="text-gray-800 text-sm font-bold leading-tight tracking-normal"
          >Fecha maxima de pago</label
        >
        <div class="relative mb-5 mt-2">
          <div
            class="absolute right-0 text-gray-600 flex items-center pr-3 h-full cursor-pointer"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg"
              class="icon icon-tabler icon-tabler-info-circle"
              width="20"
              height="20"
              viewBox="0 0 24 24"
              stroke-width="1.5"
              stroke="currentColor"
              fill="none"
              stroke-linecap="round"
              stroke-linejoin="round"
            >
              <path stroke="none" d="M0 0h24v24H0z"></path>
              <circle cx="12" cy="12" r="9"></circle>
              <line x1="12" y1="8" x2="12.01" y2="8"></line>
              <polyline points="11 12 12 12 12 16 13 16"></polyline>
            </svg>
          </div>
          <input
            type="date"
            id="fechaMaximoPago"
            class="mb-8 text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal w-full h-10 flex items-center pl-3 text-sm border-gray-300 rounded border"
            bind:value={formData.fechaPago}
            placeholder="MM/YY"
          />
        </div>

        <!--Nombre del banco-->
        <label
          for="nombreBanco"
          class="text-gray-800 text-sm font-bold leading-tight tracking-normal"
          >Nombre del banco</label
        >
        <div class="relative mb-5 mt-2">
          <div
            class="absolute text-gray-600 flex items-center px-4 border-r h-full"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg"
              fill="none"
              viewBox="0 0 24 24"
              stroke-width="1.5"
              stroke="currentColor"
              class="w-6 h-6"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M2.25 18.75a60.07 60.07 0 0 1 15.797 2.101c.727.198 1.453-.342 1.453-1.096V18.75M3.75 4.5v.75A.75.75 0 0 1 3 6h-.75m0 0v-.375c0-.621.504-1.125 1.125-1.125H20.25M2.25 6v9m18-10.5v.75c0 .414.336.75.75.75h.75m-1.5-1.5h.375c.621 0 1.125.504 1.125 1.125v9.75c0 .621-.504 1.125-1.125 1.125h-.375m1.5-1.5H21a.75.75 0 0 0-.75.75v.75m0 0H3.75m0 0h-.375a1.125 1.125 0 0 1-1.125-1.125V15m1.5 1.5v-.75A.75.75 0 0 0 3 15h-.75M15 10.5a3 3 0 1 1-6 0 3 3 0 0 1 6 0Zm3 0h.008v.008H18V10.5Zm-12 0h.008v.008H6V10.5Z"
              />
            </svg>
          </div>
          <input
            type="text"
            id="nombreBanco"
            class="text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal w-full h-10 flex items-center pl-16 text-sm border-gray-300 rounded border"
            bind:value={formData.paymentMethod}
            placeholder="BVBA"
          />
        </div>
        <div class="flex items-center justify-start w-full">
          <!--Btn guardar datos-->
          <button class="focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-700 transition duration-150 ease-in-out hover:bg-indigo-600 bg-indigo-700 rounded text-white px-4 sm:px-8 py-2 text-xs sm:text-sm">
            Agregar
          </button>

          <button
            class="focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-gray-400 ml-3 bg-gray-100 transition duration-150 text-gray-600 ease-in-out hover:border-gray-400 hover:bg-gray-300 border rounded px-8 py-2 text-sm"
            on:click={() => (modalForm = false)}
          >
            Cancelar
          </button>
        </div>
        <button
          class="cursor-pointer absolute top-0 right-0 mt-4 mr-5 text-gray-400 hover:text-gray-600 transition duration-150 ease-in-out rounded focus:ring-2 focus:outline-none focus:ring-gray-600"
          on:click={() => (modalForm = false)}
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
    </form>
  </div>
{/if}

<!--   <tbody
                id="tbodyPrincipal"
                class="bg-white divide-y divide-gray-200 dark:divide-gray-700 dark:bg-gray-900"
              >
             
            
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
                    <button on:click={() => mostrarDetail(client)} class="text-slate-800 hover:text-blue-600 text-sm bg-white hover:bg-slate-100 border border-slate-200 font-medium px-4 py-2 inline-flex space-x-1 items-center">
                      <span>
                        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-4 h-4">
                          <path stroke-linecap="round" stroke-linejoin="round" d="M2.036 12.322a1.012 1.012 0 010-.639C3.423 7.51 7.36 4.5 12 4.5c4.638 0 8.573 3.007 9.963 7.178.07.207.07.431 0 .639C20.577 16.49 16.64 19.5 12 19.5c-4.638 0-8.573-3.007-9.963-7.178z" />
                          <path stroke-linecap="round" stroke-linejoin="round" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
                        </svg>                      
                      </span>
                    </button>
                  </td>
                  <td class="px-4 py-2"></td>
                </tr>
                {#if mostrarModalDetail && clienteSeleccionado._id === client._id}
                  <ModalDetailUser {clienteSeleccionado} {mostrarModalDetail} />
                {/if}
              {/each}
                {/if}
              </tbody> -->
