<script setup>
import { ref } from 'vue'
import KeyPad from '../components/KeyPad.vue'
import ResultsTable from '../components/ResultsTable.vue'
import ScoreBoard from '../components/ScoreBoard.vue'
import { useGameStore } from '@/stores/game'
import { onMounted, onUnmounted } from 'vue'
import { socket } from '@/services/socket'

import '../assets/time-attack.css'

function connectat() {
    console.log('Connectat al servidor!', socket.id)
}

function rebreClassificacio(dades) {
    console.log('Classificació rebuda:', dades)

    store.actualitzarClassificacio(dades)
}

onMounted(() => {
    socket.on('connect', connectat)
    socket.on('classificacio', rebreClassificacio)

    socket.connect()
})

onUnmounted(() => {
    socket.off('connect', connectat)
    socket.off('classificacio', rebreClassificacio)

    socket.disconnect()
})

const store = useGameStore()
const nom = ref('')
const tecles = ['A', 'S', 'D', 'F']

const tempsObjectiu = {
    A: 2,
    S: 4,
    D: 5,
    F: 7
}

const jugant = ref(false)
const resultats = ref([])

let tempsInici = 0

function rebreTecla(tecla) {

    if (!jugant.value) {
        return
    }

    let tempsArribada = performance.now()
    let diferencia = (tempsArribada - tempsInici) / 1000

    // Detectar pulsaciones repetidas
    for (let index = 0; index < resultats.value.length; index++) {

        if (resultats.value[index].tecla == tecla) {
            console.log("Tecla repetida")
            return
        }
    }

    let nouResultat = {
        tecla: tecla,
        objectiu: tempsObjectiu[tecla],
        tempsReal: Number(diferencia.toFixed(2)),
        error: Number((tempsObjectiu[tecla] - diferencia).toFixed(2))
    }

    console.log(nouResultat)

    resultats.value.push(nouResultat)

    // Comprobar si hemos terminado la partida
    if (resultats.value.length === tecles.length) {
        jugant.value = false

        const errorTotal = resultats.value.reduce(
            (total, r) => total + r.error, 0
        )

        const errorFinal = Number(errorTotal.toFixed(2))

        // Guardamos la partida en Pinia
        store.guardarPartida(errorFinal)

        // Enviamos el resultado al servidor
        socket.emit('resultat', {
            nom: store.nomJugador,
            errorTotal: errorFinal
        })
    }
}

function entrar() {
    if (nom.value.trim() === '') return

    store.guardarNom(nom.value.trim())
}

function iniciarPartida() {
    resultats.value = []
    jugant.value = true
    tempsInici = performance.now()
}
</script>

<template>
    <div v-if="!store.nomJugador" class="time-attack-page">

        <div class="welcome-panel">

            <h1>TIME ATTACK</h1>

            <h2>¡Bienvenido!</h2>

            <p class="lead">
                Introduce tu nombre para comenzar.
            </p>

            <div class="mb-3">
                <input
                    v-model="nom"
                    class="form-control"
                    placeholder="Introduce tu nombre"
                >
            </div>

            <button
                @click="entrar"
                class="btn btn-primary w-100"
            >
                Entrar al juego
            </button>

        </div>

    </div>

    <div v-else class="time-attack-page">

        <h2>Hola, {{ store.nomJugador }}!</h2>

        <div class="row">

            <!-- Zona principal del juego -->
            <div class="col-md-8">

                <div class="game-panel text-center">

                    <h1 class="mb-3">TIME ATTACK</h1>

                    <p class="lead">
                        Intenta pulsar las teclas en el momento exacto.
                    </p>

                    <button
                        class="btn btn-primary mb-4"
                        @click="iniciarPartida"
                        :disabled="jugant"
                    >
                        Iniciar partida
                    </button>

                    <KeyPad
                        :tecles="tecles"
                        :bloquejat="!jugant"
                        :premudes="resultats.map(r => r.tecla)"
                        @premuda="rebreTecla"
                    />

                    <ResultsTable
                        v-if="!jugant && resultats.length === tecles.length"
                        :resultats="resultats"
                    />

                </div>

            </div>

            <!-- Barra lateral -->
            <div class="col-md-4">

                <ScoreBoard />

            </div>

        </div>

    </div>
</template>