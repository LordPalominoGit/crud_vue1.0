<template>
    <div>
        <h1 class="text-center">Gestionar Articulos</h1>
        <hr>


<!-- Button trigger modal -->
<button @click="modificar=false;abrirModal();" type="button" class="btn btn-primary my-4">
  Nuevo Articulo
</button>

<!-- Modal -->
<div class="modal" :class="{mostrar:modal}">
  <div class="modal-dialog">
    <div class="modal-content">

      <div class="modal-header">
        <h5 class="modal-title">{{titulo_modal}}</h5>
        <button @click="cerrarModal();" type="button" class="close" data-dismiss="modal" aria-label="Close">
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      <div class="modal-body">
          <div class="my-4">
            <label for="nombre">Nombre</label>
            <input v-model="articulo.nombre" type="text" class="form-control" id="nombre" placeholder="Nombre del articulo">
          </div>
           <div class="my-4">
            <label for="Descripcion">Descripcion</label>
            <input v-model="articulo.descripcion" type="text" class="form-control" id="Descripcion" placeholder="Descripcion del articulo">
          </div>
           <div class="my-4">
            <label for="Stock">Stock</label>
            <input v-model="articulo.stock" type="number" class="form-control" id="Stock" placeholder="stock">
          </div>
       




      </div>
      <div class="modal-footer">
        <button @click="cerrarModal();" type="button" class="btn btn-secondary" data-dismiss="modal">Cancelar</button>
        <button @click="guardar();" type="button" class="btn btn-success">Guardar</button>
      </div>
    </div>
  </div>
</div>




        <table class="table table-striped table-hover">
  <thead>
    <tr>
      <th scope="col">#</th>
      <th scope="col">Nombre</th>
      <th scope="col">Descripcion</th>
      <th scope="col">Stock</th>
      <th scope="col" colspan="2" class="text-center">Accion</th>
      
    </tr>
  </thead>
  <tbody>
    <tr v-for="art in articulos" :key="art.id">
      <th scope="row">{{art.id}}</th>
      <td>{{art.nombre}}</td>
      <td>{{art.descripcion}}</td>
      <td>{{art.stock}}</td>
      <td><button  @click="modificar=true;abrirModal(art);" class="btn btn-warning">Editar</button></td>
      <td><button @click="eliminar(art.id)" class="btn btn-danger">Eliminar</button></td>

    </tr>
    
  </tbody>
</table>

    <nav  aria-label="Page navigation example">
      <ul class="pagination">
        <li v-if="pagination.current_page > 1" class="page-item">
          <a href="#" @click.prevent="changePage(pagination.current_page-1)" class="page-link">
            <span>
            Atras
            </span>
            </a>
        </li>



        <li v-for="page in pagesNumber" v-bind:class="[page==isActived ?'active':'']" :key="page.id"  class="page-item">
          <a href="#" @click.prevent="changePage(page)" class="page-link">
           {{page}}
            </a>
        </li>



        <li v-if="pagination.current_page < pagination.last_page"  class="page-item">
          <a href="#" @click.prevent="changePage(pagination.current_page+1)" class="page-link">
            <span>
            Siguiente
            </span>
            </a>
        </li>



      </ul>
    </nav>
  
    </div>
</template>
<script>
export default {
    data(){
      
        return{
          
          pagination:{
             'total':0,
              'current_page':0,
              'per_page':0,
              'last_page':0,
              'from':0,
              'to':0
          },
          offset:2,
            articulo:{
            nombre:'yy',
            descripcion:'yyy',
            stock:1,
            },
            id:0,
            modificar:true,
            modal:0,
            titulo_modal:'',
           articulos:[],           
        }
        
    },
    computed:{
      isActived:function(){
        return this.pagination.current_page;
      },
      pagesNumber:function(){
        if(!this.pagination.to){
          return[];
        }
        var from = this.pagination.current_page - this.offset;
        if(from<1){
          from=1;
        }

        var to=from+ (this.offset*2);

        if(to>= this.pagination.last_page){
          to=this.pagination.last_page;

        }

        var pagesArray=[];
        while(from<=to){
          pagesArray.push(from);
          from++;
        }
        return pagesArray;

      }
    },
    methods:{
        async listar(page){
          const res=await axios.get('articulos?page='+page);
            this.articulos=res.data.articulos.data;
            this.pagination=res.data.pagination;
            console.log(res.data.pagination);
        },
        async eliminar(id){
          const res=await axios.delete('articulos/'+id);
          this.listar();  
        },

        async guardar(){
            if(this.modificar){
            const res=await axios.put('articulos/'+this.id,this.articulo);
            }else{
            const res=await axios.post('articulos',this.articulo);
            
            }
            this.cerrarModal();
            this.listar(); 
        },


        async abrirModal(data={}){
          this.modal=1; 
          if(this.modificar){
              this.id=data.id;
              this.titulo_modal="Modificar Articulo";
              this.articulo.nombre=data.nombre;
              this.articulo.descripcion=data.descripcion;
              this.articulo.stock=data.stock;

          }else{
              this.data=0,
            this.titulo_modal="Crear Articulo";
            this.articulo.nombre='';
              this.articulo.descripcion='';
              this.articulo.stock=1;
          }
         
        },
         async cerrarModal(){
          this.modal=0;  
        },
        changePage:function(page){
          this.pagination.current_page=page;
          this.listar(page);
        },

    },

    created(){
        this.listar();
        console.log(this.pagination);
       
    },
    
}
</script>
<style>
.mostrar{
    display: list-item;
    opacity: 100%;
    background: #707070;
}

</style>