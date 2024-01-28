<script>
    //Obtencion de valores del api
    let data = [];
    async function api() {
        try {
            const url = await fetch("https://payments-api-jpt5.onrender.com/api/v1/")
            //recibiendo la promosea de la api
            let response = await url.json()
            //data = a la respuesta de la api
            data = response.data; 
            console.log (data)     
        } catch (error) {
            //console.error()
            return error
        }
    }
    //llamado de la api
    api()
    //variable reactiva en tiempo real
    $: url = "https://payments-api-jpt5.onrender.com/api/v1/delete-user/"

    
    async function deleteUsser(urlNueva){
        try {
            const responseDelete = await fetch(urlNueva,{
                method: "DELETE",
                headers:{
                    'Content-Type': 'application/json'
                }
            })
            const dataDelete = await responseDelete.json()
            console.log("Usuario eliminado correctamente", dataDelete)

        } catch (error) {
            console.error("Error al eliminar el usuario")
        }
    }
    
    let showModal;
    let showModalEditar;
    let idDelete;

    let newDataEliminar = [];
    let countProcessData = 4

    //metodo get para recibir los datos del usser para editar, visualizar u eliminar
    async function btnusser() {
        try {
            const metodoGet = await fetch("https://payments-api-jpt5.onrender.com/api/v1/" + idDelete,{
                method: 'GET',
                headers: {'Content-Type': 'application/json'}
            })
            //recibiendo la promosea de la api
            let response = await metodoGet.json()
            //data = a la respuesta de la api
            newDataEliminar = response;
            console.log(newDataEliminar)

        } catch (error) {
            //console.error()
            return error
        }
    }
    
    
</script>

{#each data.slice(0, countProcessData) as element}
    <tr>
        <!--Id clientes-->
        <td class="px-4 py-4 text-sm font-medium whitespace-nowrap">
            <div>
                <h2 class="font-medium text-gray-800 dark:text-white ">{element._id}</h2>
            </div>
        </td>
        <!--Nombre cliente-->
        <td class="px-12 py-4 text-sm font-medium whitespace-nowrap">
            <div class="inline px-3 py-1 text-sm font-normal rounded-full text-emerald-500 gap-x-2 bg-emerald-100/60 dark:bg-gray-800">
                <h4 class="text-gray-700 dark:text-gray-200">{element.username}</h4>
            </div>
        </td>
        <!--Apellido cliente-->
        <td class="px-4 py-4 text-sm whitespace-nowrap">
            <div>
                <h4 class="text-gray-700 dark:text-gray-200">{element.lastName}</h4>
            </div>
        </td>
        <!--Monto cliente cliente-->
        <td class="px-4 py-4 text-sm whitespace-nowrap">
            <div>
                <h4 class="text-gray-700 dark:text-gray-200">{element.total}</h4>
            </div>
        </td>

        <!-- todo: acciones -->
        <td class="px-4 py-4 text-sm whitespace-nowrap">
            <div class="inline-flex items-center rounded-md shadow-sm">

                <!--Editar usuario-->
                <button on:click={()=>{
                    // alert(element._id)
                        showModalEditar = true;
                    }} 
                    class="focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-700 text-slate-800 hover:text-blue-600 text-sm bg-white hover:bg-slate-100 border border-slate-200 rounded-l-lg font-medium px-4 py-2 inline-flex space-x-1 items-center">
                    <span><svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-4 h-4">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M16.862 4.487l1.687-1.688a1.875 1.875 0 112.652 2.652L10.582 16.07a4.5 4.5 0 01-1.897 1.13L6 18l.8-2.685a4.5 4.5 0 011.13-1.897l8.932-8.931zm0 0L19.5 7.125M18 14v4.75A2.25 2.25 0 0115.75 21H5.25A2.25 2.25 0 013 18.75V8.25A2.25 2.25 0 015.25 6H10" />
                    </svg>
                    </span>
                </button>
                <!--Vista previa del usser-->
                <button on:click={()=>{
                    // alert(element._id)
                    showModalEditar = true;
                }} class="text-slate-800 hover:text-blue-600 text-sm bg-white hover:bg-slate-100 border border-slate-200  font-medium px-4 py-2 inline-flex space-x-1 items-center">
                    <span>
                        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-4 h-4">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M2.036 12.322a1.012 1.012 0 010-.639C3.423 7.51 7.36 4.5 12 4.5c4.638 0 8.573 3.007 9.963 7.178.07.207.07.431 0 .639C20.577 16.49 16.64 19.5 12 19.5c-4.638 0-8.573-3.007-9.963-7.178z" />
                            <path stroke-linecap="round" stroke-linejoin="round" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
                        </svg>                      
                    </span>
                </button>
                <!--Eliminar usser-->
                <button on:click={
                    ()=>{
                        showModal = true;
                        idDelete = element._id;
                        // alert(idDelete)
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
    </tr>

    <!-- todo: Modal delte usser-->
    {#if showModal}
    <div   class="min-w-screen h-screen animated fadeIn faster  fixed  left-0 top-0 flex justify-center items-center inset-0 z-50 outline-none focus:outline-none bg-no-repeat bg-center bg-cover"   id="modal-id">
        <div class="absolute bg-black opacity-80 inset-0 z-0"></div>
     <div class="w-full  max-w-lg p-5 relative mx-auto my-auto rounded-xl shadow-lg  bg-white ">
         <!--content-->
         <div class="">
             <!--body-->
             <div class="text-center p-5 flex-auto justify-center">
                 <svg xmlns="http://www.w3.org/2000/svg" class="w-4 h-4 -m-1 flex items-center text-red-500 mx-auto" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                   <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path>
                 </svg>
                 <svg xmlns="http://www.w3.org/2000/svg" class="w-16 h-16 flex items-center text-red-500 mx-auto" viewBox="0 0 20 20" fill="currentColor">
                     <path fill-rule="evenodd" d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm5-1a1 1 0 00-1 1v6a1 1 0 102 0V8a1 1 0 00-1-1z" clip-rule="evenodd" />
                 </svg>
                 <h2 class="text-xl font-bold py-4 ">¿Estas seguro?</h2>
                 <p class="text-sm text-gray-500 px-8">Eliminaras a {newDataEliminar.username} {newDataEliminar.lastName}</p>    
         </div>
         <!--footer-->
         <div class="p-3  mt-2 text-center space-x-4 md:block">
             <button on:click={
                ()=>{
                    showModal = false;
                }
             } class="mb-2 md:mb-0 bg-white px-5 py-2 text-sm shadow-sm font-medium tracking-wider border text-gray-600 rounded-full hover:shadow-lg hover:bg-gray-100">
                 Cancelar
             </button>
             <button on:click={
                 ()=>{
                    let newUrl = url.concat(idDelete);
                    deleteUsser(newUrl);
                    showModal = false;
                }
             }  class="mb-2 md:mb-0 bg-red-500 border border-red-500 px-5 py-2 text-sm shadow-sm font-medium tracking-wider text-white rounded-full hover:shadow-lg hover:bg-red-600">Eliminar</button>
         </div>
       </div>
     </div>
    </div>
    {/if}

    <!--Modal Editar-->
    {#if showModalEditar}
        <!--Comienzo del modal-->
        <dh-component>
            <div  class="py-12  transition duration-150 ease-in-out z-10 absolute top-0 right-0 bottom-0 left-0" id="modal">
                <div role="alert" class="container mx-auto w-11/12 md:w-2/3 max-w-lg">
                    <div class="relative py-8 px-5 md:px-10 bg-white shadow-md rounded border border-gray-400">
                        <div class="w-full flex justify-start text-gray-600 mb-3">
                            <svg  xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-wallet" width="52" height="52" viewBox="0 0 24 24" stroke-width="1" stroke="currentColor" fill="none" stroke-linecap="round" stroke-linejoin="round">
                                <path stroke="none" d="M0 0h24v24H0z" />
                                <path d="M17 8v-3a1 1 0 0 0 -1 -1h-10a2 2 0 0 0 0 4h12a1 1 0 0 1 1 1v3m0 4v3a1 1 0 0 1 -1 1h-12a2 2 0 0 1 -2 -2v-12" />
                                <path d="M20 12v4h-4a2 2 0 0 1 0 -4h4" />
                            </svg>
                        </div>
                        <h1 class="text-gray-800 font-lg font-bold tracking-normal leading-tight mb-4">Editar un usuario</h1>
                        <span>Si no desea actualizar algun campo no lo modifique</span><br>
                        
                        <!--Nombre-->
                        <label for="name" class="text-gray-800 text-sm font-bold leading-tight tracking-normal">Nombre</label>
                        <input value="{newDataEliminar.username}" id="name" class="mb-5 mt-2 text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal w-full h-10 flex items-center pl-3 text-sm border-gray-300 rounded border" placeholder="James" />
                        
                        
                        <!--Apellido-->
                        <label for="apellido" class="text-gray-800 text-sm font-bold leading-tight tracking-normal">Apellido</label>
                        <input value="{newDataEliminar.lastName}" id="apellido" class="mb-5 mt-2 text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal w-full h-10 flex items-center pl-3 text-sm border-gray-300 rounded border" placeholder="Gonzales" />
                        
                        <!--Monto del prestamo-->
                        <label for="montoPrestamo" class="text-gray-800 text-sm font-bold leading-tight tracking-normal">Capital prestado</label>
                        <div class="relative mb-5 mt-2">
                            <div class="absolute text-gray-600 flex items-center px-4 border-r h-full">
                                <svg xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-credit-card" width="20" height="20" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" fill="none" stroke-linecap="round" stroke-linejoin="round">
                                    <path stroke="none" d="M0 0h24v24H0z" />
                                    <rect x="3" y="5" width="18" height="14" rx="3" />
                                    <line x1="3" y1="10" x2="21" y2="10" />
                                    <line x1="7" y1="15" x2="7.01" y2="15" />
                                    <line x1="11" y1="15" x2="13" y2="15" />
                                </svg>
                            </div>
                            <input value="{newDataEliminar.capitalPrestado}" type="number" id="montoPrestamo" class="text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal w-full h-10 flex items-center pl-16 text-sm border-gray-300 rounded border" placeholder="Monto total" />
                        </div>

                        <!--Fecha de prestamo-->
                        <label for="fechaPrestamo" class="text-gray-800 text-sm font-bold leading-tight tracking-normal">Fecha del prestamo</label>
                        <div class="relative mb-5 mt-2">
                            <div class="absolute right-0 text-gray-600 flex items-center pr-3 h-full cursor-pointer">
                                <svg xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-calendar-event" width="20" height="20" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" fill="none" stroke-linecap="round" stroke-linejoin="round">
                                    <path stroke="none" d="M0 0h24v24H0z" />
                                    <rect x="4" y="5" width="16" height="16" rx="2" />
                                    <line x1="16" y1="3" x2="16" y2="7" />
                                    <line x1="8" y1="3" x2="8" y2="7" />
                                    <line x1="4" y1="11" x2="20" y2="11" />
                                    <rect x="8" y="15" width="2" height="2" />
                                </svg>
                            </div>
                            <input value="{newDataEliminar.fechaPrestamo}" type="date" id="fechaPrestamo" class="text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal w-full h-10 flex items-center pl-3 text-sm border-gray-300 rounded border" placeholder="MM/YY" />
                        </div>

                        <!--Fecha maxima de pago-->
                        <label for="fechaMaximoPago" class="text-gray-800 text-sm font-bold leading-tight tracking-normal">Fecha maxima de pago</label>
                        <div class="relative mb-5 mt-2">
                            <div class="absolute right-0 text-gray-600 flex items-center pr-3 h-full cursor-pointer">
                                <svg xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-info-circle" width="20" height="20" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" fill="none" stroke-linecap="round" stroke-linejoin="round">
                                    <path stroke="none" d="M0 0h24v24H0z"></path>
                                    <circle cx="12" cy="12" r="9"></circle>
                                    <line x1="12" y1="8" x2="12.01" y2="8"></line>
                                    <polyline points="11 12 12 12 12 16 13 16"></polyline>
                                </svg>
                            </div>
                            <input value="{newDataEliminar.fechaPago}" type="date" id="fechaMaximoPago" class="mb-8 text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal w-full h-10 flex items-center pl-3 text-sm border-gray-300 rounded border" placeholder="MM/YY" />
                        </div>


                        <!--Nombre del banco-->
                        <label for="nombreBanco" class="text-gray-800 text-sm font-bold leading-tight tracking-normal">Nombre del banco</label>
                        <div class="relative mb-5 mt-2">
                            <div class="absolute text-gray-600 flex items-center px-4 border-r h-full">
                                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                                    <path stroke-linecap="round" stroke-linejoin="round" d="M2.25 18.75a60.07 60.07 0 0 1 15.797 2.101c.727.198 1.453-.342 1.453-1.096V18.75M3.75 4.5v.75A.75.75 0 0 1 3 6h-.75m0 0v-.375c0-.621.504-1.125 1.125-1.125H20.25M2.25 6v9m18-10.5v.75c0 .414.336.75.75.75h.75m-1.5-1.5h.375c.621 0 1.125.504 1.125 1.125v9.75c0 .621-.504 1.125-1.125 1.125h-.375m1.5-1.5H21a.75.75 0 0 0-.75.75v.75m0 0H3.75m0 0h-.375a1.125 1.125 0 0 1-1.125-1.125V15m1.5 1.5v-.75A.75.75 0 0 0 3 15h-.75M15 10.5a3 3 0 1 1-6 0 3 3 0 0 1 6 0Zm3 0h.008v.008H18V10.5Zm-12 0h.008v.008H6V10.5Z" />
                                </svg>
                            </div>
                            <input value="sebas" type="text" id="nombreBanco" class="text-gray-600 focus:outline-none focus:border focus:border-indigo-700 font-normal w-full h-10 flex items-center pl-16 text-sm border-gray-300 rounded border" placeholder="BVBA" />
                        </div>
                        <div class="flex items-center justify-start w-full">
                            <!--Btn guardar datos-->
                            <button on:click={
                                ()=>{
                                    let banco = document.getElementById("nombreBanco").value;
                                    // alert(banco)
                                }
                            } class="focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-700 transition duration-150 ease-in-out hover:bg-indigo-600 bg-indigo-700 rounded text-white px-8 py-2 text-sm">enviar</button>
                            <button class="focus:outline-none focus:ring-2 focus:ring-offset-2  focus:ring-gray-400 ml-3 bg-gray-100 transition duration-150 text-gray-600 ease-in-out hover:border-gray-400 hover:bg-gray-300 border rounded px-8 py-2 text-sm" onclick="modalHandler()" on:click={()=>{
                                showModalEditar = false
                            }}>Cancel</button>
                        </div>
                        <button class="cursor-pointer absolute top-0 right-0 mt-4 mr-5 text-gray-400 hover:text-gray-600 transition duration-150 ease-in-out rounded focus:ring-2 focus:outline-none focus:ring-gray-600" onclick="modalHandler()" aria-label="close modal" type="button">
                            <svg  xmlns="http://www.w3.org/2000/svg"  class="icon icon-tabler icon-tabler-x" width="20" height="20" viewBox="0 0 24 24" stroke-width="2.5" stroke="currentColor" fill="none" stroke-linecap="round" stroke-linejoin="round">
                                <path stroke="none" d="M0 0h24v24H0z" />
                                <line x1="18" y1="6" x2="6" y2="18" />
                                <line x1="6" y1="6" x2="18" y2="18" />
                            </svg>
                        </button>
                    </div>
                </div>
            </div>
            
            <script>
                let modal = document.getElementById("modal");
                function modalHandler(val) {
                    if (val) {
                        fadeIn(modal);
                        modal.style.display = "flex"
                    } else {
                        fadeOut(modal);
                    }
                }
                function fadeOut(el) {
                    el.style.opacity = 1;
                    (function fade() {
                        if ((el.style.opacity -= 0.1) < 0) {
                            el.style.display = "flex";
                        } else {
                            requestAnimationFrame(fade);
                        }
                    })();
                }
                function fadeIn(el, display) {
                    el.style.opacity = 0;
                    el.style.display = display || "flex";
                    (function fade() {
                        let val = parseFloat(el.style.opacity);
                        if (!((val += 0.2) > 1)) {
                            el.style.opacity = val;
                            requestAnimationFrame(fade);
                        }
                    })();
                }
            </script>
            
        </dh-component>
        <!-- Code block ends -->
    {/if}
    
{/each}


