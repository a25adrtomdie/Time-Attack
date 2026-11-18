<script setup>
import { computed } from 'vue'
import { useGameStore } from '@/stores/game'

const store = useGameStore()

// Ordenar la clasificación de menor a mayor error
const classificacioOrdenada = computed(() => {
    return [...store.classificacio].sort(
        (a, b) => a.errorTotal - b.errorTotal
    )
})

// Mostrar una copa según la posición
function obtenirCopa(index) {
    if (index === 0) return '🏆'
    if (index === 1) return '🥈'
    if (index === 2) return '🥉'
    return index + 1
}
</script>

<template>
    <div>

        <!-- HISTORIAL LOCAL -->
        <div class="card scoreboard-card mt-4">

            <div class="card-header">
                <h4 class="mb-0">Historial de partidas</h4>
            </div>

            <div class="card-body">

                <div v-if="store.partides.length === 0">
                    <p class="alert alert-info mb-0">
                        Todavía no hay ninguna partida registrada.
                    </p>
                </div>

                <div v-else>

                    <div style="max-height: 400px; overflow-y: auto;">

                        <table class="table table-striped table-sm">

                            <thead>
                                <tr>
                                    <th>Partida</th>
                                    <th>Jugador</th>
                                    <th>Error</th>
                                </tr>
                            </thead>

                            <tbody>
                                <tr
                                    v-for="partida in store.partides"
                                    :key="partida.id"
                                >
                                    <td>{{ partida.id }}</td>
                                    <td>{{ partida.nom }}</td>
                                    <td>{{ partida.errorTotal }} s</td>
                                </tr>
                            </tbody>

                        </table>

                    </div>

                    <button
                        class="btn btn-danger btn-sm w-100"
                        @click="store.esborrarPartides()"
                    >
                        Borrar historial
                    </button>

                </div>

            </div>

        </div>


        <!-- CLASIFICACION GLOBAL -->
        <div class="card scoreboard-card mt-4">

            <div class="card-header">
                <h4 class="mb-0">Clasificación global 🌍</h4>
            </div>

            <div class="card-body">

                <div v-if="classificacioOrdenada.length === 0">

                    <p class="alert alert-info mb-0">
                        Todavía no hay resultados. ¡Sé el primero en jugar!
                    </p>

                </div>

                <div v-else>

                    <div style="max-height: 400px; overflow-y: auto;">

                        <table class="table table-striped table-sm">

                            <thead>
                                <tr>
                                    <th>Posición</th>
                                    <th>Jugador</th>
                                    <th>Error</th>
                                </tr>
                            </thead>

                            <tbody>

                                <tr
                                    v-for="(resultado, index) in classificacioOrdenada"
                                    :key="index"
                                >
                                    <td>
                                        <span v-if="index < 3">
                                            {{ obtenirCopa(index) }}
                                        </span>
                                        <span v-else>
                                            {{ obtenirCopa(index) }}º
                                        </span>
                                    </td>

                                    <td>{{ resultado.nom }}</td>

                                    <td>{{ resultado.errorTotal }} s</td>

                                </tr>

                            </tbody>

                        </table>

                    </div>

                </div>

            </div>

        </div>

    </div>
</template>