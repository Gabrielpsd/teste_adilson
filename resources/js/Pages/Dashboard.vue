<template>
    <Head title="DashBoard" />
    <AuthenticatedLayout>
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">Dashboard</h2>
        </template>
         <!-- Options Section -->
         <div class="options">
            <div class="d-flex flex-column align-items-center justify-content-center">
                <h3>Total do período</h3>
                <inputFloatNumber v-model="totalPorPerido" :disabled="true" :size="'large'"></inputFloatNumber>
            </div>
            <div class="d-flex flex-column align-items-center justify-content-center">
                <h3>Filtro de período</h3>
                <datePickerInterval v-model="FiltrosData" :Label="''" :size="'large'"></datePickerInterval>
            </div>
        </div>

        <!-- Charts Section -->
        <div class="charts-container">
            <div class="chart-row">
                <div class="chart-col card">
                    <VendasPorSexo :data="vendasPorSexo" v-if="vendasPorSexo" />
                </div>
                <div class="chart-col card">
                    <VendasPorPeriodo :data="vendasPorPeriodo" v-if="vendasPorPeriodo" />
                </div>
                <div class="chart-col card">
                    <VendasValorPorServico :data="VendasValorPorServico" v-if="VendasValorPorServico" />
                </div>
            </div>
            <div class="chart-row">
                <div class="chart-col card">
                    <totalIdadePorSexo :data="totalIdadePorSexo" v-if="totalIdadePorSexo" />
                </div>
                <div class="chart-col card">
                    <vendasTotalPorMarca :data="vendasTotalPorMarca" v-if="vendasTotalPorMarca" />
                </div>
                <div class="chart-col card">
                    <vendasPorCliente :data="vendasPorCliente" v-if="vendasPorCliente" />
                </div>
            </div>
            <div class="chart-row">
                <div class="chart-col card">
                    <vendasTotalPorSexo :data="vendasTotalPorSexo" v-if="vendasTotalPorSexo" />
                </div>
                <div class="chart-col card">
                    <VendasPorServico :data="VendasPorServico" v-if="VendasPorServico" />
                </div>
            </div>
        </div>
        <div class="d-flex flex-column align-items-center justify-content-center">
            <h3> Vendas do Periodo selecionado</h3>
        </div>
        <table class="table table-striped table-hover ">
                        <thead>
                            <tr>
                                <th scope="col">Id</th>
                                <th scope="col">Cliente</th>
                                <th scope="col">Veiculo</th>
                                <th scope="col">Data serviço</th>
                                <th scope="col">Quantidade Servicos</th>
                                <th scope="col">Valor Total</th>
                            </tr>
                        </thead>
                    <tbody>
                    <tr v-for="ordemServico in ordemServico">
                        <th scope="row">{{ordemServico.id}}</th>
                            <td>{{ordemServico.nome}}</td>
                            <td>{{ordemServico.descricao_veiculo}}</td>
                            <td>{{ordemServico.data}}</td>
                            <td>{{ordemServico.quantidade_servicos}}</td>
                            <td>R$ {{ ordemServico.valor_total }}</td>
                            <td>
                    </td>
                    </tr>
                </tbody>
                
            </table>
    </AuthenticatedLayout>
</template>

<script>
import { Head } from '@inertiajs/vue3';
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import config from '../Pages/Assets/ArquivosConfiguracao/apiconfig'
import VendasPorSexo from '../Pages/Graficos/VendasPorSexo.vue'
import vendasPorPeriodo from './Graficos/vendasPorPeriodo.vue';
import datePickerInterval from './Utils/datePickerInterval.vue';
import VendasPorPeriodo from './Graficos/vendasPorPeriodo.vue';
import inputFloatNumber from './Utils/inputFloatNumber.vue';
import totalIdadePorSexo from './Graficos/totalIdadePorSexo.vue';
import vendasTotalPorMarca from './Graficos/vendasTotalPorMarca.vue';
import VendasPorServico from './Graficos/VendasPorServico.vue';
import VendasValorPorServico from './Graficos/VendasValorPorServico.vue';
import vendasTotalPorSexo from './Graficos/vendasTotalPorSexo.vue';
import vendasPorCliente from './Graficos/vendasPorCliente.vue';
export default {

    components: {
        Head,
        AuthenticatedLayout,
        VendasPorSexo,
        VendasPorPeriodo,
        datePickerInterval,
        inputFloatNumber,
        totalIdadePorSexo,
        vendasTotalPorMarca,
        VendasPorServico,
        VendasValorPorServico,
        vendasTotalPorSexo,
        vendasPorCliente
    },
  data() {
    return {
        vendasPorSexo: null, 
        vendasPorPeriodo: null,
        totalPorPerido: null,
        vendasTotalPorMarca: null,
        dataInicial: '',
        dataFinal: '',
        FiltrosData: null,
        totalIdadePorSexo: null,
        ordemServico: null,
        VendasPorServico: null,
        VendasValorPorServico: null,
        vendasTotalPorSexo: null,
        vendasPorCliente: null,
        hasReloaded: localStorage.getItem('hasReloaded') === 'true',
    };
  },
  watch:{
    FiltrosData(){
        this.requisitaDados()
    }
  },
  mounted(){
    if (!this.hasReloaded) {
      window.location.reload();
      localStorage.setItem('hasReloaded', 'true');
      this.hasReloaded = true;
    }
  },
  methods:{
    async requisitaDados(){
        
        if(this.FiltrosData)
        {
            this.dataInicial = this.FiltrosData[0]
            this.dataFinal = this.FiltrosData[1]
        }

        const csrfToken = document.getElementsByName("_token")[0].value; // Get CSRF token

        console.log(csrfToken)
        const datas = {
            data_inicial: this.dataInicial,
            data_final: this.dataFinal
        }

        const parametros = {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                    'X-CSRF-TOKEN': csrfToken, // Include CSRF token in headers
                },
                body: JSON.stringify(datas), // Convert data to JSON
            }

         // Grafico de vendas por Sexo
        await fetch(config.dashboard.vendasPorSexo,parametros)
            .then((response) => response.json())
            .then((data) => {
                this.vendasPorSexo = data
        });

        await fetch(config.dashboard.quantidadeVendasPorPeriodo,parametros)
            .then((response) => response.json())
            .then((data) => {
                this.vendasPorPeriodo = data
        });

        await fetch(config.dashboard.totalPorPerido,parametros)
            .then((response) => response.json())
            .then((data) => {
                this.totalPorPerido = data[0].value
        });

        await fetch(config.dashboard.totalIdadePorSexo,parametros)
            .then((response) => response.json())
            .then((data) => {
                this.totalIdadePorSexo = data
        });

        await fetch(config.dashboard.totalValorPorMarca,parametros)
            .then((response) => response.json())
            .then((data) => {
                this.vendasTotalPorMarca = data
        });

        await fetch(config.dashboard.VendasPorPeriodo,parametros)
            .then((response) => response.json())
            .then((data) => {
                console.log(data) 
                this.ordemServico = data
        });

        await fetch(config.dashboard.totalServicosPorPeriodo,parametros)
            .then((response) => response.json())
            .then((data) => {

                this.VendasValorPorServico = data.map(
                    item=>
                    ({value: item.value, // Replace value with quantidade
                    name: item.name   }))

                this.VendasPorServico = data.map(
                    item=>
                    ({value: item.quantidade, // Replace value with quantidade
                    name: item.name   }))
        });

        await fetch(config.dashboard.totalVendasPorSexo,parametros)
            .then((response) => response.json())
            .then((data) => {
                this.vendasTotalPorSexo = data
            });
            
            await fetch(config.dashboard.totalVendasPorCliente,parametros)
            .then((response) => response.json())
            .then((data) => {
                this.vendasPorCliente = data
        });

    }
  },
  async beforeMount() {
    const hoje = new Date()
    const trintaDiasAtras = new Date()

    trintaDiasAtras.setDate(hoje.getDate() - 30)

    this.dataFinal = hoje.toISOString().split("T")[0]
    this.dataInicial = trintaDiasAtras.toISOString().split("T")[0]

    this.FiltrosData = [this.dataInicial,this.dataFinal]
  },

};
</script>

<style scoped>
/* Base styles for options section */
.options {
    display: flex;
    flex-wrap: wrap;
    gap: 20px;
    margin-bottom: 20px;
}

.options > div {
    flex: 1 1 100%; /* Full width by default */
}

.table, .card{
    margin-left: 10px;
    margin-right: 10px;
}

/* Base styles for charts section */
.charts-container {
    display: flex;
    flex-direction: column;
    gap: 20px;
}

.chart-row {
    display: flex;
    flex-wrap: wrap;
    gap: 20px;
}

.chart-col {
    flex: 1 1 100%; /* Full width by default */
}

/* Media query for smartphones */
@media (max-width: 767px) {
    .options > div {
        flex: 1 1 100%; /* Stack options vertically on small screens */
    }

    .chart-row {
        flex-direction: column; /* Stack charts vertically on small screens */
    }

    .chart-col {
        flex: 1 1 100%; /* Full width for each chart */
    }
}

/* Media query for tablets and larger devices */
@media (min-width: 768px) {
    .options > div {
        flex: 1 1 calc(50% - 20px); /* Two columns for options */
    }

    .chart-col {
        flex: 1 1 calc(50% - 20px); /* Two columns for charts */
    }
}

/* Media query for desktops */
@media (min-width: 1024px) {
    .options > div {
        flex: 1 1 calc(33.33% - 20px); /* Three columns for options */
    }

    .chart-col {
        flex: 1 1 calc(33.33% - 20px); /* Three columns for charts */
    }
}
</style>