<script>
    //Obtencion de valores del api
    let data = [];
    async function api() {
        try {
            const url = await fetch("http://localhost:3000/cliente")
            //recibiendo la promosea de la api
            let response = await url.json()
            //data = a la respuesta de la api
            data = response;
            numeroClientes = data.length
            
            
            
        } catch (error) {
            //console.error()
            return error
        }
    }
    //llamado de la api
    api()
    //variable reactiva en tiempo real
    $: url = "http://localhost:3000/cliente/"

    
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
    
    //var para ctivar el modal de elimar al usser
    let showModal;

    //let para activar modal de visualizar datos
    let showModalvisualizar;
    //id del usser a elimar
    let idDelete;

    //donde se guardaran los datos recibidos
    let newDataEliminar = [];

    //metodo get para recibir los datos del usser para editar, visualizar u eliminar
    async function btnusser() {
        try {
            const metodoGet = await fetch("http://localhost:3000/cliente/" + idDelete,{
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


<!--Recorredor de datos de la base de datos(api)-->
{#each data as element}
        <tr>
            <!--Id clientes-->
            <td class="px-4 py-4 text-sm font-medium whitespace-nowrap">
                <div>
                    <h2 class="font-medium text-gray-800 dark:text-white ">{element.id}</h2>
                </div>
            </td>
            <!--Nombre cliente-->
            <td class="px-12 py-4 text-sm font-medium whitespace-nowrap">
                <div class="inline px-3 py-1 text-sm font-normal rounded-full text-emerald-500 gap-x-2 bg-emerald-100/60 dark:bg-gray-800">
                    <h4 class="text-gray-700 dark:text-gray-200">{element.nombre}</h4>
                </div>
            </td>
            <!--Apellido cliente-->
            <td class="px-4 py-4 text-sm whitespace-nowrap">
                <div>
                    <h4 class="text-gray-700 dark:text-gray-200">{element.apellido}</h4>
                </div>
            </td>
            <!--Monto cliente cliente-->
            <td class="px-4 py-4 text-sm whitespace-nowrap">
                <div>
                    <h4 class="text-gray-700 dark:text-gray-200">{element.total}</h4>
                </div>
            </td>
            <!--Barra porcentaje de pago-->
            <td class="px-4 py-4 text-sm whitespace-nowrap">
                <div class="w-full bg-gray-200 rounded-full h-2.5 dark:bg-gray-600">
                    <div class="bg-orange-500 h-2.5 rounded-full" style="width: 65%"></div>
                </div>
            </td>
            <!--Grupos de botones manipulables por el admin-->
            <td class="px-4 py-4 text-sm whitespace-nowrap">
                <div class="inline-flex items-center rounded-md shadow-sm">
                    <!--Editar usuario-->
                    <button class="text-slate-800 hover:text-blue-600 text-sm bg-white hover:bg-slate-100 border border-slate-200 rounded-l-lg font-medium px-4 py-2 inline-flex space-x-1 items-center">
                        <span><svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-4 h-4">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M16.862 4.487l1.687-1.688a1.875 1.875 0 112.652 2.652L10.582 16.07a4.5 4.5 0 01-1.897 1.13L6 18l.8-2.685a4.5 4.5 0 011.13-1.897l8.932-8.931zm0 0L19.5 7.125M18 14v4.75A2.25 2.25 0 0115.75 21H5.25A2.25 2.25 0 013 18.75V8.25A2.25 2.25 0 015.25 6H10" />
                        </svg>
                        </span>
                    </button>
                    <!--Vista previa del usser-->
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
                    <!--Eliminar usser-->
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
        </tr>

    <!--Modal delte usser-->
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
                 <p class="text-sm text-gray-500 px-8">Eliminaras a {newDataEliminar.nombre} {newDataEliminar.apellido}</p>    
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
    <!--Modal visualizar-->
    
    
{/each}


