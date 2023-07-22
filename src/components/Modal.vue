<script setup>
    import { ref } from 'vue';
    import cerrarModal from '../assets/img/cerrar.svg'
    import Alerta from './alerta.vue';


    const emit = defineEmits(["ocultar-modal", 'update:nombre',
        'update:cantidad', 'update:categoria',
        'guardar-gasto', 'eliminar-gastos'])

    const error = ref('')


    const props = defineProps({
        modal: {
            type: Object,
            required: true
        },
        nombre: {
            type: String,
            required: true
        },
        cantidad: {
            type: [String, Number],
            required: true
        },
        categoria: {
            type: String,
            required: true
        },
        modal: {
            type: Object,
            required: true
        },
        disponible: {
            type: Number,
            required: true
        },
        id: {
            type: [String, null],
            required: true
        }
    })


    const old = props.cantidad
    // ESTE METODO RESIVE EL OBJETO DEL FORMULARIO
    // LO QUE SIGNIFICA QUE LE  emit('guardar-gato')
    // TIENE EL OBJETO DEFINIDO EN EL FORMULARIO
    const agregarGasto = () => {
        //validar que no hayan campos vacios
        const { nombre, cantidad, categoria, disponible, id } = props
        if ([nombre, cantidad, categoria].includes('')) {
            error.value = 'Todos los campos son obligatorios'
            setTimeout(() => {
                error.value = ''
            }, 3000);
            return

        } if (cantidad <= 0) {
            error.value = 'Cantidad no valida'
            setTimeout(() => {
                error.value = ''
            }, 3000);
            return
        }

        //Validar que le usuario no gaste ma de los disponible
        if (id) {
            //Tomar el gasto ya realizado
            if(cantidad > old + disponible) {
                error.value = 'Has exedido el presupuesto'
                setTimeout(() => {
                    error.value = ''
                }, 3000);
                return
            }
        } else {
            if (cantidad > disponible) {
                error.value = 'has excedido el presupuesto'
                setTimeout(() => {
                    error.value = ''
                }, 3000);
                return
            }
        }


        emit('guardar-gasto')
        // validar la cantidad
    }

</script>

<template>
    <div class="modal">
        <div class="cerrar-modal">
            <img :src="cerrarModal" @click="$emit('ocultar-modal')">
        </div>
        <div class="contenedor contenedor-formulario" :class="[modal.animar ? 'animar' : 'cerrar']">
            <form class="nuevoGasto" @submit.prevent="agregarGasto">

                <legend> {{ id ? 'Guardar Cambios': 'Añadir gasto' }}</legend>

                <Alerta v-if="error">{{ error }}</Alerta>

                <div class="campo">
                    <label for="nombre">Nombre de Gasto:</label>
                    <input type="text" id="gasto" placeholder="Añade el nombre del gasto" :value="nombre"
                        @input="$emit('update:nombre', $event.target.value)" />

                    <label for="nombre">Cantidad:</label>
                    <input type="number" id="gasto" placeholder="Añade la cantidad de Gastto, ej: 300" :value="cantidad"
                        @input="$emit('update:cantidad', +$event.target.value)" />

                    <label for="nombre">Categorioa:</label>
                    <select name="Categorias" id="" :value="categoria"
                        @input="$emit('update:categoria', $event.target.value)">
                        <option value="">-- Seleccione --</option>
                        <option value="ahorro">Ahorro</option>
                        <option value="comida">Comida</option>
                        <option value="casa">Casa</option>
                        <option value="gastos">Gastos Varios</option>
                        <option value="ocio">Ocio</option>
                        <option value="salud">Salud</option>
                        <option value="suscripciones">Suscripciones</option>
                    </select>
                </div>
                <input type="submit" :value="[id ? 'Guardar Cambios' : 'Añadir Gasto']" >"
            </form>
            <button
                type: = "button"  
                class="btn-eliminar"  
                v-if="id"
                @click="$emit('eliminar-gastos')"
            >
                Eliminar Gasto
            </button>
        </div>
    </div>
</template>

<style scoped>
.modal {
    position: absolute;
    background-color: rgb(0 0 0/ 0.9);
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
}

.cerrar-modal {
    position: absolute;
    right: 3rem;
    top: 3rem;
}

.cerrar-modal img {
    width: 3rem;
    cursor: pointer;
}

.contenedor-formulario {
    transition-property: all;
    transition-duration: 300ms;
    transition-timing-function: ease-in;
    opacity: 0;
}

.contenedor-formulario.animar {
    opacity: 1;
}

.contenedor-formulario.cerrar {
    opacity: 0;
}

.nuevoGasto {
    margin: 10rem auto 0 auto;
    display: grid;
    gap: 2rem;
}

.campo {
    display: grid;
    gap: 2rem;
}

.nuevoGasto legend {
    text-align: center;
    color: var(--blanco);
    font-size: 3rem;
    font-weight: 700;
}

.nuevoGasto input,
.nuevoGasto select {
    background-color: var(--gris-claro);
    border-radius: 1rem;
    padding: 1rem;
    border: none;
    font-size: 2.2rem;
}

.nuevoGasto label {
    color: var(--blanco);
    font-size: 2.2rem;
}

.nuevoGasto input[type=submit] {
    background-color: var(--azul);
    color: var(--blanco);
    cursor: pointer;
}

.btn-eliminar {
    border: none;
    padding:  1rem;
    width: 100%;
    background-color: #ef4444;
    font-weight: 700;
    font-size:  1.2rem;
    color: var(--blanco);
    margin-top: 10rem;
    cursor: pointer;
}
</style>