<script setup>
defineProps({
    resultats: Array
})
</script>

<template>
    <div class="mt-5">

        <h3 class="mb-3">Resultados de la partida</h3>

        <table class="table table-striped table-bordered">

            <thead>
                <tr>
                    <th>Tecla</th>
                    <th>Tiempo objetivo</th>
                    <th>Tiempo real</th>
                    <th>Error</th>
                </tr>
            </thead>

            <tbody>
                <tr v-for="(resultado, index) in resultats" :key="index">
                    <td>{{ resultado.tecla }}</td>
                    <td>{{ resultado.objectiu }} s</td>
                    <td>{{ resultado.tempsReal }} s</td>
                    <td>{{ resultado.error }} s</td>
                </tr>
            </tbody>

        </table>

    </div>
</template>