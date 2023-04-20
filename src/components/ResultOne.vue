<template>
 <div class="result-container" v-if="visible">
    <button>CIAO</button>
 </div>
</template>

<script>
    import SearchOne from './SearchOne.vue';
    export default {
        
        components: {
        },
        data() {
            return {
                visible: false
            }
        },
        methods: {
            now()  {
                if(SearchOne.methods.searchPressed().includes("undefined")){
                    console.log("non undefined");
                
                }
                    
                else
                    console.log("undefined");
            },
            
        },
        mounted() {
        }
}
</script>

<style scoped>
</style>