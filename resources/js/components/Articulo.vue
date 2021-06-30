<template>
    <div>
        <h1 class="text-center"> Gestor de articulo</h1>
        <hr>
        <!-- Button to Open the Modal -->
        <button @click="modificar=false; abrirModal();" type="button" class="btn btn-primary">
        Agregar
        </button>

        <!-- The Modal -->
        <div class="modal" :class="{mostrar:modal}">
            <div class="modal-dialog">
                <div class="modal-content">

                <!-- Modal Header -->
                <div class="modal-header">
                    <h4 class="modal-title">{{tituloModal}}</h4>
                    <button @click="cerrarModal();" type="button" class="close" data-dismiss="modal">&times;</button>
                </div>

                <!-- Modal body -->
                <div class="modal-body">
                    <div>
                        <label for="nombre">Nombre</label>
                        <input v-model="articulo.nombre" type="text" class="form-control" id="nombre" name="nombre" placeholder="Nombre del articulo">
                    </div>
                    <div>
                        <label for="descripcion">Descripcion</label>
                        <input v-model="articulo.descripcion" type="text" class="form-control" id="descripcion" name="descripcion" placeholder="Descripcion">
                    </div>
                    <div>
                        <label for="stock">Stock</label>
                        <input v-model="articulo.stock" type="number" class="form-control" id="stock" name="stock" placeholder="Stock">
                    </div>
                </div>

                <!-- Modal footer -->
                <div class="modal-footer">
                    <button @click="cerrarModal();" type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                    <button @click="guardar();" type="button" class="btn btn-success" data-dismiss="modal">Guardar</button>
                </div>

                </div>
            </div>
        </div>

        <hr>

        <table class="table table-striped">
            <thead class="thead-dark">
                <tr>
                <th scope="col">#</th>
                <th scope="col">Nombre</th>
                <th scope="col">Descripcion</th>
                <th scope="col">Stock</th>
                <th scope="col" colspan="2">Accion</th>
                </tr>
            </thead>
            <tbody v-for="art in articulos" :key="art.id">
                <tr>
                <th scope="row">{{art.id}}</th>
                <td>{{art.nombre}}</td>
                <td>{{art.descripcion}}</td>
                <td>{{art.stock}}</td>
                <td>
                    <button @click="modificar=true; abrirModal(art);" class="btn btn-warning">Editar</button>
                </td>
                <td>
                    <button @click="eliminar(art.id)" class="btn btn-danger">Eliminar</button>
                </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
export default {
    data(){
        return {
            articulo:{
                nombre:'',
                descripcion:'',
                stock:1,

            },
            id:0,
            modificar:true,
            modal:0,
            tituloModal:'',
            articulos:[],
        }
    },
    methods: {
        async listar (){
           const res=await axios.get('/articulos');
            this.articulos=res.data;
        }, 
        
        async eliminar(id){
            const res=await axios.delete('/articulos/'+id);
            this.listar();
        },

        async guardar(){
            if(this.modificar){
                const res=await axios.put('/articulos/'+this.id, this.articulo);
            }else{
                const res=await axios.post('/articulos', this.articulo);
            }

            this.cerrarModal();
            this.listar();
        },

        abrirModal(data=[]){
            this.modal=1;
            if(this.modificar){
                this.id=data.id;
                this.tituloModal="Modificar Articulo";
                this.articulo.nombre=data.nombre;
                this.articulo.descripcion=data.descripcion;
                this.articulo.stock=data.stock;
            }else{
                this.id=0;
                this.tituloModal="Crear Articulo";

                this.articulo.nombre='';
                this.articulo.descripcion='';
                this.articulo.stock=1;
            }
        },
        cerrarModal(){
            this.modal=0;
        },
    },

    created() {
        this.listar();
    },

}
</script>

<style>
.mostrar{
    display: list-item;
    opacity: 1;
    background: rgb(39, 37, 37);
}

</style>