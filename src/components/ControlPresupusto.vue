<script setup>
    import imagen from '../assets/img/grafico.jpg'
    import {formatearCantidad} from '../helpers'

    const props = defineProps({
        presupuesto: {
            type:Number,
            required:true
        },
        disponible: {
            type:Number,
            required:true
        }
    })

</script>

<template>
    <div class="dos-columnas">
        <div class="contenedor-grafico">
            <img 
                :src="imagen" 
            >
        </div>

        <div class="contender-presupuesto">
            <button
                class="reset-app"
            >Reseterar App</button>
            
            <p>
                <span>Presupuesto:</span>
                {{formatearCantidad(presupuesto) }}
            </p>

            <p>
                <span>Disponible:</span>
                {{ formatearCantidad(disponible) }}
            </p>

            <p>
                <span>Gastado:</span>
                $0
            </p>

        </div>

        
    </div>
</template>

<style scoped>
    .dos-columnas {
        display: flex;
        flex-direction: column;
    }

    .dos-columnas > :first-child {
        margin-bottom: 3rem;
    }

    @media (min-width: 768px){
        .dos-columnas{
            flex-direction: row;
            gap: 4rem;
            align-items: center;
        }

        .dos-columnas > :first-child {
        margin-bottom: 0;
        }
    }

    .reset-app {
        background-color: #db2777;
        border: none;
        padding: 1rem;
        width: 100%;
        color: white;
        font-weight: 900;
        text-transform: 1rem;
        transition-property: background-color;
        transition-duration: 300ms;
        border-radius: 1rem;
    }

    .reset-app:hover {
        background-color: #a0074c;
        cursor: pointer;
    }

    .contender-presupuesto{
        width: 100%;
    }

    .contender-presupuesto p {
        font-size:  2.4rem;
        text-align: center;
        color: var(--gris-oscuro)
    }
    @media (min-width: 768px) {
         .contender-presupuesto{
            text-align: left ;
         }
    }
    .contender-presupuesto span {
        font-weight: 900;
        color: var(--azul);
    }


</style>