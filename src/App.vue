<script setup>
    import { ref, reactive, watch, computed, onMounted} from 'vue'
    import Presupuesto from './components/Presupuesto.vue';
    import ControlPresupusto from './components/ControlPresupusto.vue';
    import iconNuevoGasto from './assets/img/nuevo-gasto.svg'
    import Modal from './components/Modal.vue'
    import alerta from './components/alerta.vue';
    import {generarId} from './helpers/index'
    import Gastos from './components/Gastos.vue'
    import Filtros from './components/Filtros.vue'


    const modal = reactive({
        mostrar :   false,
        animar  :   false
    })

    const presupusto = ref(0)
    const gastos = ref([])
    const gastado = ref([])
    const disponible = ref(0)
    const filtro = ref('')


    const gasto = reactive({
        nombre: '',
        cantidad: '',
        categoria: '',
        id: null,
        fecha: Date.now()
    })

    watch(gastos, () => {
        const totalGastado = gastos.value.reduce((total, gasto) => gasto.cantidad + total, 0)
        gastado.value = totalGastado

        const totalDisponible = presupusto.value - gastado.value
        disponible.value = totalDisponible

        localStorage.setItem('gastos', JSON.stringify(gastos.value))

    }, {
        deep:true
    })

    watch(modal, () => {
        if(!modal.mostrar) {
            //Reiniciar objeto
            reiniciarStateGasto()
        }
    },{
       deep:true 
    })
   
    watch(presupusto, () => {
        localStorage.setItem('presupuesto', presupusto.value)
    })

    onMounted(() => {
        const presupuestoStorage = localStorage.getItem('presupuesto')
        if(presupuestoStorage){
            presupusto.value = Number(presupuestoStorage)
            disponible.value = Number(presupuestoStorage)
        }

        const gastosStorage = localStorage.getItem('gastos')
        if (gastosStorage) {
            
            gastos.value = JSON.parse(gastosStorage)
        }
    })

    const definirPresupuesto = (cantidad) => {
        presupusto.value = cantidad
        disponible.value = cantidad
    }

    const mostrarModal = () => {
        modal.mostrar = true
        setTimeout(() => {
            modal.animar = true
        }, 300);
    }

    const ocultarModal = () => {
        modal.animar = false
        setTimeout(() => {
            modal.mostrar = false
        }, 300);
    }

    const guardarGasto = () => {
        
        if(gasto.id){
            // Editanto
            const {id} = gasto
            const i = gastos.value.findIndex((gasto => gasto.id === id))
            gastos.value[i] = {...gasto}

        } else {
            //Registro nuevo
            gastos.value.push({
                ...gasto,
                id:generarId()
            })

        }

        ocultarModal() ///..................................
        reiniciarStateGasto()

    }

    const reiniciarStateGasto = () => {
        //Reiniciar objeto
        Object.assign(gasto, {
            nombre: '',
            cantidad: '',
            categoria: '',
            id: null,
            fecha: Date.now()
        })
    }

    const selecccionarGasto = (id) => {
        const gastoEditar = gastos.value.filter(gasto => gasto.id === id )[0]
        Object.assign(gasto, gastoEditar)
        mostrarModal()
    }

    const eliminarGasto = () => {
        if(confirm('Eliminar?')){

            gastos.value = gastos.value.filter(gastarState => gastarState.id !== gasto.id)
            ocultarModal()
        }
    }

    const gastosFiltrados = computed(() => {
        if(filtro.value) {
            return gastos.value.filter(gasto => gasto.categoria === filtro.value)
        } else {
            return gastos.value 
        }
    })

    const resetApp = () => {
        if(confirm('Desea eliminar presupuesto y gastos?')){
            gastos.value = []
            presupusto.value = []
        }
    }


</script>

<template>
    <div
        :class="{fijar: modal.mostrar}"
    >

        <header>
            <h1>Planificador de Gastos</h1>

            <div class="contenedor-header contenedor sombra">
                <Presupuesto 
                    @definir-presupuesto = "definirPresupuesto"
                    
                    v-if="presupusto === 0"
                />
                <ControlPresupusto
                    v-else
                    :presupuesto    =   "presupusto"
                    :disponible     =   "disponible"
                    :gastado        =   "gastado"
                    @reset-app       =   "resetApp"
                />
                
            </div>
        </header>

        <main v-if="presupusto > 0">
            <Filtros
                v-model:filtro = 'filtro'
            />

            <div class="listado-gastos contenedor">
                <h2>{{ gastosFiltrados.length > 0 ? 'Gastos':'No hay gastos' }}</h2>
                
                <Gastos 
                    v-for="gasto in gastosFiltrados"
                    :key="gasto.id"
                    :gasto="gasto"
                    @selecccionar-gasto = "selecccionarGasto"
                />
           
            </div>
            <div class="crear-gasto">
                <img
                    :src="iconNuevoGasto"
                    @click="mostrarModal"
                    
                >
            </div>
        </main>

        <Modal 
            v-if="modal.mostrar"
            @ocultar-modal="ocultarModal"
            @guardar-gasto="guardarGasto"
            @eliminar-gastos="eliminarGasto"
            :modal = "modal"
            :disponible = "disponible"
            :id = "gasto.id"
            v-model:nombre="gasto.nombre"
            v-model:cantidad="gasto.cantidad"
            v-model:categoria="gasto.categoria"
        /> 
    </div>
</template>


<style>
:root {
    --azul: #3b82f6;
    --blanco: #FFF;
    --gris-claro: #f5f5f5;
    --gris: #949494;
    --gris-oscuro: #64748b;
    --negro: #000
}

html {
    font-size: 62.5%;
    box-sizing: border-box;
}

*,
*:before,
*:after {
    box-sizing: inherit;
}

body {
    font-size: 1.6rem;
    font-family: "Lato", sans-serif;
    background-color: var(--gris-claro);
}

h1 {
    font-size: 4rem;
}

h2 {
    font-size: 3rem;
}

.fijar {
    overflow: hidden;
    height: 100vh;
}

header {
    background-color: var(--azul);
}

header h1 {
    padding: 3rem 0;
    margin: 0;
    color: var(--blanco);
    text-align: center;
}

.contenedor {
    width: 90%;
    max-width: 80rem;
    margin: 0 auto;
}

.contenedor-header {
    margin-top: -5rem;
    transform: translateY(5rem);
    padding: 5rem;
}

.sombra {
    box-shadow: 0px 10px 15px -3px rgba(0, 0, 0, 0.1);
    background-color: white;
    border-radius: 1.2rem;
    padding: 5rem;

}

.crear-gasto {
    position: fixed;
    bottom: 5rem;
    right: 5rem;
}

.crear-gasto img {
    width: 5rem;
    cursor: pointer;
}

.listado-gastos{
    margin-top: 10rem;
}

.listado-gastos h2 {
    font-weight: 900;
    color: var(--gris-oscuro);
}


</style>

