<template>
    <div class="estado-list">
        <!-- <h1>{{ mode }}</h1> -->
        <!-- <h1>{{ url }}</h1> -->
        <h1>Lista de Estados</h1>
        <!-- <button v-if="!formVisible" @click="novoEstado">Novo</button> -->
        <button type="button" class="btn btn-primary btn-sm" v-if="!formVisible" @click="novoEstado">Novo</button>
        <hr/>
        <FormEstado v-if="formVisible" :propsEstado="estadoEscolhido" @cancelar="limpar" @salvar_estado="buscarEstados"/>
        
      <div class="table-responsive">
        <table class="table table-dark table-striped">
            <thead>
            <tr>
                <th>ID</th>
                <th>Nome</th>
                <th></th>
            </tr>
            </thead>
            <tbody>
            <tr v-for="estado in listaEstados" :key="estado.id" scope="row">
                <td>
                    {{ estado.id }}
                </td>
                <td>
                    {{ estado.nome }}
                </td>
                <td>
                    <!-- <button @click="alterarEstado(estado)">Alterar</button> -->
                    <button type="button" class="btn btn-warning btn-sm" @click="alterarEstado(estado)">Alterar</button>
                    <!-- <button @click="excluirEstado(estado.id)">Excluir</button> -->
                    <button type="button" class="btn btn-danger btn-sm" @click="excluirEstado(estado.id)">Excluir</button>
                </td>
            </tr>
            </tbody>
        </table>
      </div>

      <div class="container">
  <div class="row justify-content-center">
    <div class="col-auto">
      <button v-for="pagina in totalPages" :key="pagina" @click.prevent="irPara(pagina)" class="btn btn-light ms-1">{{ pagina }}</button>
    </div>
    <div class="col-auto">
      <input type="text" v-model="pageNumber" placeholder="Número da página" class="form-control" />
    </div>
    <div class="col-auto">
      <select v-model="pageSize" class="form-select">
        <option value="1">1</option>
        <option value="10">10</option>
        <option value="20">20</option>
        <option value="50">50</option>
      </select>
    </div>
    <div class="col-auto">
      <select v-model="property" class="form-select">
        <option value="id">ID</option>
        <option value="nome">Nome</option>
      </select>
    </div>
    <div class="col-auto">
      <select v-model="direction" class="form-select">
        <option value="ASC">Crescente</option>
        <option value="DESC">Decrescente</option>
      </select>
    </div>
    <div class="col-auto">
      <button type="button" class="btn btn-success" @click.prevent="buscarEstados">
        <i class="bi bi-binoculars"></i> Buscar
      </button>
    </div>
  </div>
</div>
    </div>
</template>

<script> 
import FormEstado from './FormEstado.vue';
import axios from "axios";
    export default {
        components:{
            FormEstado
        },
        data(){
            return{
                listaEstados:[],
                estadoEscolhido:null,
                formVisible:false,
                mode: import.meta.env.MODE,
                url: import.meta.env.VITE_APP_URL_API,
                pageNumber:1,
                pageSize:10,
                direction:'ASC',
                property:'id',
                totalPages:0
            };
        },
        methods:{
            async buscarEstados(){
                this.estadoEscolhido = null;
                this.formVisible = false;
                //buscar a lista de estados no servidor
                // http://localhost:8080/estados 
                const response = await axios.get(`http://localhost:8080/estados?pageNumber=${this.pageNumber}&pageSize=${this.pageSize}&direction=${this.direction}&property=${this.property}`);
                console.log(response.data);
                this.listaEstados = response.data.content;
                this.totalPages = response.data.totalPages;
            },
            limpar(){
                this.estadoEscolhido = null;
                this.formVisible = !this.formVisible;
            }, 
            novoEstado(){
                this.formVisible = !this.formVisible;
            },
            alterarEstado(estado){
                this.estadoEscolhido = estado;
                this.formVisible = true;
                // console.log(estado);
            },
            async excluirEstado(id){
                const response = await axios.delete(`http://localhost:8080/estado/${id}`);
                console.log(response.data);
                this.buscarEstados();
            },
            irPara(pagina) {
            this.pageNumber = pagina;
            this.buscarEstados();
            },
        } ,  
        mounted() {
            this.buscarEstados();
        }     
    }
</script>

<style scoped>
.estado-list {
  padding: 100px;
}

.table-responsive {
  overflow-x: auto;
}

.text-center {  
  text-align: center;
}
</style>
