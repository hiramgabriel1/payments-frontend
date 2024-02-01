<script>
    import ModalNewUser from '../../components/ModalNewUser.svelte';
    import RecorridoUsers from '../../components/RecorridoUsers.svelte';
    import { onMount, createEventDispatcher } from 'svelte';

    let dataClients
    let clientesfiltrados
    async function getClients(){
    try {
        const clients = await fetch('https://payments-api-jpt5.onrender.com/api/v1/')
        let data = await clients.json()
        dataClients = data
    } catch (error) {
        console.log(error);
    }
    }
    getClients()
    
    //total clientes
    let data = []
    async function totalclientes(){
        try {
            let url = await fetch('https://payments-api-jpt5.onrender.com/api/v1/')
            let response = await url.json()
            data = response
            
        } catch (error) {
            console.error("error")
        }
    }

    totalclientes()

    async function filterAllClients(prop){
        if(prop === 'pagado'){
            clientesfiltrados = dataClients.data.filter(client => client.pagado === true)
        } else if(prop === 'pendiente'){
            clientesfiltrados = dataClients.data.filter(client => client.pagado === false);
        } else if(prop === 'cancelado'){
            clientesfiltrados = dataClients.data.filter(client => client.cancelado === true)
        }
        else {
            clientesfiltrados = dataClients.data
        }
   }

   let searchTerm = '';
    let searchResults = [];

    const handleInput = (event) => {
        // Actualiza searchTerm cada vez que el usuario escribe en el campo de búsqueda
        searchTerm = event.target.value.toLowerCase();
        console.log('Término de búsqueda:', searchTerm);
    };

    const handleSearch = () => {
        // Realiza la búsqueda cuando el usuario presiona el botón de búsqueda
        searchResults = dataClients.data.filter(client => {
            // Compara el término de búsqueda con el nombre de usuario en minúsculas
            return client.username.toLowerCase().includes(searchTerm);
        });

        console.log('Resultados de la búsqueda:', searchResults);
    };


    const dispatch = createEventDispatcher()
    const itemsPerPage = 4
    
    let currentPage = 0

    const nextPage = () => {
        const lastPage = Math.ceil(data.length / itemsPerPage) - 1
        currentPage = currentPage === lastPage ? 0 : currentPage + 1
        
        dispatch('pageChanged', { currentPage })
    }
 
    const previousPage = () => {
        currentPage = currentPage === 0 ? 0 : currentPage - 1;
        dispatch('pageChanged', { currentPage });
    }

    export const getCurrentPageData = () => {
        const startIndex = currentPage * itemsPerPage
        const endIndex = startIndex + itemsPerPage

        return data.slice(startIndex, endIndex)
    }

    onMount(()=> {
        console.log("funciona xd");
    })

</script>

<section class="container px-4 mx-auto">
    <div class="sm:flex sm:items-center sm:justify-between">
        <div>
            <div class="flex items-center gap-x-3">
                
                <h2 class="text-lg font-medium text-gray-800 dark:text-white">Total clientes:</h2>
                {#if data != ""}
                    <span class="px-3 py-1 text-xs text-blue-600 bg-blue-100 rounded-full dark:bg-gray-800 dark:text-blue-400">{data.length} clientes</span>
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
            <button class="px-5 py-2 text-xs font-medium text-gray-600 transition-colors duration-200 bg-gray-100 sm:text-sm dark:bg-gray-800 dark:text-gray-300" on:click={filterAllClients}>
                Clientes
            </button>

            <a href="/pagos-pendientes" class="px-5 py-2 text-xs font-medium text-gray-600 transition-colors duration-200 sm:text-sm dark:hover:bg-gray-800 dark:text-gray-300 hover:bg-gray-100" 
            on:click={() => filterAllClients('pendiente')}>
                Pagos pendientes
            </a>

            <button class="px-5 py-2 text-xs font-medium text-gray-600 transition-colors duration-200 sm:text-sm dark:hover:bg-gray-800 dark:text-gray-300 hover:bg-gray-100" on:click={() => filterAllClients('cancelado')}>
                Clientes cancelados
            </button>

            <button class="px-5 py-2 text-xs font-medium text-gray-600 transition-colors duration-200 sm:text-sm dark:hover:bg-gray-800 dark:text-gray-300 hover:bg-gray-100" on:click={() => filterAllClients('pagado')}>
                Historial de pagos
            </button>
        </div>

        <!--Search-->
        <div class="relative flex items-center mt-4 md:mt-0">
            <button on:click={handleSearch} type="submit" class="absolute">
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-5 h-5 mx-3 text-gray-400 dark:text-gray-600">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M21 21l-5.197-5.197m0 0A7.5 7.5 0 105.196 5.196a7.5 7.5 0 0010.607 10.607z" />
                </svg>
            </button>

            <input on:input={handleInput} type="text" placeholder="Search" class="block w-full py-1.5 pr-5 text-gray-700 bg-white border border-gray-200 rounded-lg md:w-80 placeholder-gray-400/70 pl-11 rtl:pr-11 rtl:pl-5 dark:bg-gray-900 dark:text-gray-300 dark:border-gray-600 focus:border-blue-400 dark:focus:border-blue-300 focus:ring-blue-300 focus:outline-none focus:ring focus:ring-opacity-40">
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
                                            <path d="M2.13347 0.0999756H2.98516L5.01902 4.79058H3.86226L3.45549 3.79907H1.63772L1.24366 4.79058H0.0996094L2.13347 0.0999756ZM2.54025 1.46012L1.96822 2.92196H3.11227L2.54025 1.46012Z" fill="currentColor" stroke="currentColor" stroke-width="0.1" />
                                            <path d="M0.722656 9.60832L3.09974 6.78633H0.811638V5.87109H4.35819V6.78633L2.01925 9.60832H4.43446V10.5617H0.722656V9.60832Z" fill="currentColor" stroke="currentColor" stroke-width="0.1" />
                                            <path d="M8.45558 7.25664V7.40664H8.60558H9.66065C9.72481 7.40664 9.74667 7.42274 9.75141 7.42691C9.75148 7.42808 9.75146 7.42993 9.75116 7.43262C9.75001 7.44265 9.74458 7.46304 9.72525 7.49314C9.72522 7.4932 9.72518 7.49326 9.72514 7.49332L7.86959 10.3529L7.86924 10.3534C7.83227 10.4109 7.79863 10.418 7.78568 10.418C7.77272 10.418 7.73908 10.4109 7.70211 10.3534L7.70177 10.3529L5.84621 7.49332C5.84617 7.49325 5.84612 7.49318 5.84608 7.49311C5.82677 7.46302 5.82135 7.44264 5.8202 7.43262C5.81989 7.42993 5.81987 7.42808 5.81994 7.42691C5.82469 7.42274 5.84655 7.40664 5.91071 7.40664H6.96578H7.11578V7.25664V0.633865C7.11578 0.42434 7.29014 0.249976 7.49967 0.249976H8.07169C8.28121 0.249976 8.45558 0.42434 8.45558 0.633865V7.25664Z" fill="currentColor" stroke="currentColor" stroke-width="0.3" />
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
                                <!-- <th scope="col" class="px-4 py-3.5 text-sm font-normal text-left rtl:text-right text-gray-500 dark:text-gray-400">Barra porcentaje pago</th>             -->
                                <th scope="col" class="relative py-3.5 px-4">
                                    <span class="sr-only">Edit</span>
                                </th>
                            </tr>
                        </thead>
                        <tbody id="tbodyPrincipal" class="bg-white divide-y divide-gray-200 dark:divide-gray-700 dark:bg-gray-900">
                            <RecorridoUsers />
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</section>

<style>
    .div{
        display: flex;     
        justify-content: space-between;
    }
  
</style>