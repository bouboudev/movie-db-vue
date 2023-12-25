<template>
    <div>
        <apexchart  :width="widthDynamic" type="radialBar" :options="chartOptions" :series="chartSeries"></apexchart>
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
            widthDynamic: {
                type: String,
                required: false,
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
            
            const chartSeries = ref([Math.round(props.voteAverage * 10)]);
            
            return {
                chartOptions,
                chartSeries,
                
            };
        },
    };
</script>

<style scoped>
    
</style>
