<script>
    import ModalNewUser from "../../components/ModalNewUser.svelte";

  let clientesHistorial = []
  let data = [{}]

 export async function clientesPagos(){
    try {
        const clients = await fetch('https://payments-api-jpt5.onrender.com/api/v1/')
        let data = await clients.json()
        let clientesFiltrados = data.data.filter(client => client.pagado === true)
        clientesHistorial = clientesFiltrados
    } catch (error) {
        console.log(error);
    }
 }
clientesPagos()

</script>

<!-- modal -->
<section class="container px-4 mx-auto">
    <div class="sm:flex sm:items-center sm:justify-between">
        <div>
            <div class="flex items-center gap-x-3">
                
                <h2 class="text-lg font-medium text-gray-800 dark:text-white">Total clientes:</h2>
                <!--Numeros de clientes-->
                {#if clientesHistorial}
                <span class="px-3 py-1 text-xs text-blue-600 bg-blue-100 rounded-full dark:bg-gray-800 dark:text-blue-400">{clientesHistorial.length} clientes</span>
                {:else}
                <span class="px-3 py-1 text-xs text-blue-600 bg-blue-100 rounded-full dark:bg-gray-800 dark:text-blue-400">0 clientes</span>
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
        <div class="inline-flex overflow-hidden bg-white border divide-x rounded-lg dark:bg-gray-900 rtl:flex-row-reverse dark:border-gray-700 dark:divide-gray-700">
            <button class="px-5 py-2 text-xs font-medium text-gray-600 transition-colors duration-200 bg-gray-100 sm:text-sm dark:bg-gray-800 dark:text-gray-300">
                <a href="/">
                    Clientes
                </a>
            </button>

            <button class="px-5 py-2 text-xs font-medium text-gray-600 transition-colors duration-200 sm:text-sm dark:hover:bg-gray-800 dark:text-gray-300 hover:bg-gray-100">
                <a href="/pagos-pendientes">
                    Pagos pendientes
                </a>
            </button>

            <button class="px-5 py-2 text-xs font-medium text-gray-600 transition-colors duration-200 sm:text-sm dark:hover:bg-gray-800 dark:text-gray-300 hover:bg-gray-100">
                <a href="/clientes-cancelados">
                    Clientes cancelados
                </a>
            </button>

            <button  class="px-5 py-2 text-xs font-medium text-gray-600 transition-colors duration-200 sm:text-sm dark:hover:bg-gray-800 dark:text-gray-300 hover:bg-gray-100">
                Historial de pagos
            </button>
        </div>

        <!--Search-->
        <div class="relative flex items-center mt-4 md:mt-0">
            <button  type="submit" class="absolute">
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-5 h-5 mx-3 text-gray-400 dark:text-gray-600">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M21 21l-5.197-5.197m0 0A7.5 7.5 0 105.196 5.196a7.5 7.5 0 0010.607 10.607z" />
                </svg>
            </button>

            <input type="text" placeholder="Search..." class="block w-full py-1.5 pr-5 text-gray-700 bg-white border border-gray-200 rounded-lg md:w-80 placeholder-gray-400/70 pl-11 rtl:pr-11 rtl:pl-5 dark:bg-gray-900 dark:text-gray-300 dark:border-gray-600 focus:border-blue-400 dark:focus:border-blue-300 focus:ring-blue-300 focus:outline-none focus:ring focus:ring-opacity-40">
        </div>
    </div>


    <!--tabla clientes-->
    <div class="flex flex-col mt-6">
        <div class="-mx-4 -my-2 overflow-x-auto sm:-mx-6 lg:-mx-8">
            <div class="inline-block min-w-full py-2 align-middle md:px-6 lg:px-8">
                <div class="overflow-hidden border border-gray-200 dark:border-gray-700 md:rounded-lg">
                    <table class="min-w-full divide-y divide-gray-200 dark:divide-gray-700">
                        <thead class="bg-gray-50 dark:bg-gray-800">
                            <tr>
                                <th scope="col" class="py-3.5 px-4 text-sm font-normal text-left rtl:text-right text-gray-500 dark:text-gray-400">
                                    <button class="flex items-center gap-x-3 focus:outline-none">
                                        <span>Id cliente</span>
                                        <svg class="h-3" viewBox="0 0 10 11" fill="none" xmlns="http://www.w3.org/2000/svg">
                                            <!-- SVG Path -->
                                        </svg>
                                    </button>
                                </th>
                                <!--Name-->
                                <th scope="col" class="px-12 py-3.5 text-sm font-normal text-left rtl:text-right text-gray-500 dark:text-gray-400">
                                    Nombre
                                </th>
                                <!--Apellido-->
                                <th scope="col" class="px-4 py-3.5 text-sm font-normal text-left rtl:text-right text-gray-500 dark:text-gray-400">
                                    Apellido
                                </th>
                                <!--Monto dinero prestado-->
                                <th scope="col" class="px-4 py-3.5 text-sm font-normal text-left rtl:text-right text-gray-500 dark:text-gray-400">Monto prestamo</th>
                                <!--Barra porcentae de pago-->
                                <th scope="col" class="px-4 py-3.5 text-sm font-normal text-left rtl:text-right text-gray-500 dark:text-gray-400">Barra porcentaje pago</th>            
                                <th scope="col" class="relative py-3.5 px-4">
                                    <span class="sr-only">Edit</span>
                                </th>
                            </tr>
                        </thead>
                        <tbody id="tbodyPrincipal" class="bg-white divide-y divide-gray-200 dark:divide-gray-700 dark:bg-gray-900">
   
                            <!-- Clientes -->
                            {#if clientesHistorial.length > 0}                             
                                {#each clientesHistorial as client}
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
                                        <td class="px-4 py-2"><!-- Botones de edición --></td>
                                    </tr>
                                {/each}
                            {:else}
                                <tr>
                                    <td colspan="6">Cargando...</td>
                                </tr>
                            {/if}
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>

    <div class="mt-6 sm:flex sm:items-center sm:justify-between ">
        <div class="text-sm text-gray-500 dark:text-gray-400">
            Page <span class="font-medium text-gray-700 dark:text-gray-100">1 of 10</span> 
        </div>

        <div class="flex items-center mt-4 gap-x-4 sm:mt-0">
            <a href="$" class="flex items-center justify-center w-1/2 px-5 py-2 text-sm text-gray-700 capitalize transition-colors duration-200 bg-white border rounded-md sm:w-auto gap-x-2 hover:bg-gray-100 dark:bg-gray-900 dark:text-gray-200 dark:border-gray-700 dark:hover:bg-gray-800">
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-5 h-5 rtl:-scale-x-100">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M6.75 15.75L3 12m0 0l3.75-3.75M3 12h18" />
                </svg>

                <span>
                    previous
                </span>
            </a>

            <a href="$" class="flex items-center justify-center w-1/2 px-5 py-2 text-sm text-gray-700 capitalize transition-colors duration-200 bg-white border rounded-md sm:w-auto gap-x-2 hover:bg-gray-100 dark:bg-gray-900 dark:text-gray-200 dark:border-gray-700 dark:hover:bg-gray-800">
                <span>
                    Next
                </span>

                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-5 h-5 rtl:-scale-x-100">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M17.25 8.25L21 12m0 0l-3.75 3.75M21 12H3" />
                </svg>
            </a>
        </div>
    </div>
</section>

<style>
   
 
</style>

<!--   <tr>

            <td class="px-4 py-4 text-sm font-medium whitespace-nowrap">
                <div>
                    <h2 class="font-medium text-gray-800 dark:text-white ">{element.id}</h2>
                </div>
            </td>
 
            <td class="px-12 py-4 text-sm font-medium whitespace-nowrap">
                <div class="inline px-3 py-1 text-sm font-normal rounded-full text-emerald-500 gap-x-2 bg-emerald-100/60 dark:bg-gray-800">
                    <h4 class="text-gray-700 dark:text-gray-200">{element.nombre}</h4>
                </div>
            </td>
   
            <td class="px-4 py-4 text-sm whitespace-nowrap">
                <div>
                    <h4 class="text-gray-700 dark:text-gray-200">{element.apellido}</h4>
                </div>
            </td>
    
            <td class="px-4 py-4 text-sm whitespace-nowrap">
                <div>
                    <h4 class="text-gray-700 dark:text-gray-200">{element.total}</h4>
                </div>
            </td>
      
            <td class="px-4 py-4 text-sm whitespace-nowrap">
                <div class="w-full bg-gray-200 rounded-full h-2.5 dark:bg-gray-600">
                    <div class="bg-orange-500 h-2.5 rounded-full" style="width: 65%"></div>
                </div>
            </td>
       
            <td class="px-4 py-4 text-sm whitespace-nowrap">
                <div class="inline-flex items-center rounded-md shadow-sm">
                
                    <button class="text-slate-800 hover:text-blue-600 text-sm bg-white hover:bg-slate-100 border border-slate-200 rounded-l-lg font-medium px-4 py-2 inline-flex space-x-1 items-center">
                        <span><svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-4 h-4">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M16.862 4.487l1.687-1.688a1.875 1.875 0 112.652 2.652L10.582 16.07a4.5 4.5 0 01-1.897 1.13L6 18l.8-2.685a4.5 4.5 0 011.13-1.897l8.932-8.931zm0 0L19.5 7.125M18 14v4.75A2.25 2.25 0 0115.75 21H5.25A2.25 2.25 0 013 18.75V8.25A2.25 2.25 0 015.25 6H10" />
                        </svg>
                        </span>
                    </button>
                    
                    <button on:click={()=>{
                        alert(element.id)
                        showModalvisualizar = true;
                    }} class="text-slate-800 hover:text-blue-600 text-sm bg-white hover:bg-slate-100 border border-slate-200  font-medium px-4 py-2 inline-flex space-x-1 items-center">
                        <span>
                            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-4 h-4">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M2.036 12.322a1.012 1.012 0 010-.639C3.423 7.51 7.36 4.5 12 4.5c4.638 0 8.573 3.007 9.963 7.178.07.207.07.431 0 .639C20.577 16.49 16.64 19.5 12 19.5c-4.638 0-8.573-3.007-9.963-7.178z" />
                                <path stroke-linecap="round" stroke-linejoin="round" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
                            </svg>                      
                        </span>
                    </button>
                 
                    <button on:click={
                        ()=>{
                            showModal = true;
                            idDelete = element.id;
                            alert(idDelete)
                            btnusser()

                        }
                    } class="text-slate-800 hover:text-blue-600 text-sm bg-white hover:bg-slate-100 border border-slate-200 rounded-r-lg font-medium px-4 py-2 inline-flex space-x-1 items-center">
                        <span>
                            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-4 h-4">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M14.74 9l-.346 9m-4.788 0L9.26 9m9.968-3.21c.342.052.682.107 1.022.166m-1.022-.165L18.16 19.673a2.25 2.25 0 01-2.244 2.077H8.084a2.25 2.25 0 01-2.244-2.077L4.772 5.79m14.456 0a48.108 48.108 0 00-3.478-.397m-12 .562c.34-.059.68-.114 1.022-.165m0 0a48.11 48.11 0 013.478-.397m7.5 0v-.916c0-1.18-.91-2.164-2.09-2.201a51.964 51.964 0 00-3.32 0c-1.18.037-2.09 1.022-2.09 2.201v.916m7.5 0a48.667 48.667 0 00-7.5 0" />
                            </svg>
                        </span>
                    </button>
                </div>
            </td>
        </tr> -->