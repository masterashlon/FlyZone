<template>
 <main>
    <SearchOne></SearchOne>
    <ResultOne></ResultOne>
 </main>
</template>

<script>
    import SearchOne from './SearchOne.vue';
    import ResultOne from './ResultOne.vue';
    export default {
        components: {
            SearchOne,
            ResultOne
        },
        methods: {
            now() {
                console.log(SearchOne.methods.searchPressed());
            }
        }
    }
</script>

<style scoped>
    main {
        height: max-content;
        width: 100vw;
    }
</style>