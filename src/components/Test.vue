<template>
    <div>
        test
    </div>
</template>

<script>
    import { onMounted } from 'vue';

    export default {
        name: 'test',
        setup() {

            onMounted(() => {
                // Vous pouvez effectuer des actions après le montage ici, si nécessaire
                console.log('test');
            });

            return {
            };
        },
    };
</script>

<style>
    /* Ajoutez vos styles ici si nécessaire */
</style>
