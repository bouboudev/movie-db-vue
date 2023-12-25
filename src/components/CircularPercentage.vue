<template>
    <div>
        <apexchart  width="55%" type="radialBar" :options="chartOptions" :series="chartSeries"></apexchart>
    </div>
</template>

<script>
    import { ref } from 'vue';
    import VueApexCharts from 'vue3-apexcharts';

    export default {
        components: {
            apexchart: VueApexCharts,
        },
        props: {
            voteAverage: {
                type: Number,
                required: true,
            },
        },
        setup(props) {
            const chartOptions = ref({
                chart: {
                    type: 'radialBar',
                },
                labels: [`${props.voteAverage * 10}%`],
                colors: ['#42B883'],
                plotOptions: {
                    radialBar: {
                        hollow: {
                            size: '50%',
                        },
                        dataLabels: {
                            showOn: 'always',
                            name: {
                                show: false,
                            },
                            value: {
                                show: true,
                                fontSize: '15px',
                                fontWeight: 'bold',
                                color: '#42B883',
                                offsetY: 8,
                            },
                        },
                    },
                },
                legend: {
                    show: false,
                },
            });

            // Calcul du pourcentage du vote moyen dans la plage de 0 à 100 et pas de chiffre après la virgule
            const chartSeries = ref([Math.round(props.voteAverage * 10)]);

            

            return {
                chartOptions,
                chartSeries,
            };
        },
    };
</script>

<style scoped>
    /* Ajoutez vos styles ici si nécessaire */
    
</style>
