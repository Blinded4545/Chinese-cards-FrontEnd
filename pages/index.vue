<template class="">
    <v-app class="" id="mainWrapper">
        <div class="flex justify-center content-center gap-4" id="searchWrapper">
            <div class="w-full gap-3" id="searchDiv">
                <v-text-field
                name="name"
                label="Word"
                id="searchField"
                density="compact"
                hide-details="auto"
                single-line
                clearable
                v-model="searchWord"
                ></v-text-field>
            </div>
            <v-btn color="success" class="my-auto" @click="showOverlay">Add Card</v-btn>
        </div>
        <div class="gap-3" id="cardWrapper" >
            <cardGen class="cardHandler" v-for="(i,k) in Object.keys(changingStack)" :key="reRenderComp" :word="i" :meaning="changingStack[i]" @deleted="deleteData"></cardGen>
        </div>

        <v-overlay v-model="overlayAddHandler" class="flex justify-center items-center">
            <overlayAdd @add="submitCard"></overlayAdd>
        </v-overlay>

    </v-app>
</template>

<script>

import cardGen from "~/components/cardGen.vue"
import overlayAdd from "~/components/overlayAdd.vue"

export default {
    name: "indexPage",
    components:{
        cardGen,
        overlayAdd
    },
    data(){
        return {
            words: {},
            searchWord: "",
            searchedStack: {},
            changingStack: {},
            overlayAddHandler: false,
            reRenderComp: false
        }
    },
    methods: {
        forceRerender(){
            this.reRenderComp = !this.reRenderComp
        },

        search(){
            
            if(this.searchWord == null){
                this.changingStack = this.searchedStack
                return
            }

            console.clear();
            console.log(this.searchedStack);
            this.changingStack = {};
            for(let clave in this.searchedStack){
                let valid = false;
                this.searchedStack[clave].forEach(e=>{
                    if(e!=null){
                        if(e.includes(this.searchWord)){
                            valid=true;
                        }
                    }
                });

                if(clave.includes(this.searchWord) || valid){
                    this.changingStack[clave] = this.searchedStack[clave];
                    //console.log(this.searchedStack[clave]);
                }
            }

            this.forceRerender();
            //console.log(Object.keys(this.changingStack));

            // this.changingStack = newStack;
        },

        deleteData(wordDeleted){
            console.log("Deleted, now fetching new data");
            console.log(wordDeleted);
            
            this.fetchData();

        },

        async fetchData(){
            let dataObtained = await useFetch('http://localhost:8000/getAll');
            let processed = JSON.parse(JSON.stringify(dataObtained["data"]));
            let finalData = Object.values(processed)[3];

            let passed = []
            finalData.forEach((e)=>{
                passed=[e["meaning1"],e["meaning2"],e["meaning3"],e["meaning4"],e["meaning5"]];
                this.searchedStack[e['word']] = passed;
            });

            console.log(this.searchedStack);
            this.changingStack = this.searchedStack;
            
        },

        showOverlay(){
            this.overlayAddHandler = !this.overlayAddHandler;
        },

        async submitCard(objectWord){
            let nword = objectWord["newWrd"];
            let means = objectWord["means"];
            console.log(nword, means);
            if(nword=="" || means[0]==""){
                return
            }else{
                const request2 = await $fetch('http://localhost:8000/add', {
                    method: "POST",
                    Headers:{"Content-type": "application/json"},
                    body:JSON.stringify({"word": nword, "meaning1": means[0], "meaning2": means[1], "meaning3": means[2], "meaning4": means[3], "meaning5": means[4]})
                });
                //this.overlayAdd = false;
                this.fetchData();
            }
        }
    },
    watch: {
        searchWord(){
            this.search();
        }
    },
    created() {
        this.searchedStack = this.words;
        this.fetchData();
    },
    
}

</script>

<style>

html{
    background-color: white;
}

#searchWrapper{
    margin: 2rem;
    width: 100%;
}

#searchDiv{
    grid-template-columns: auto min-content;
    height: min-content;
    width: 50%;
}

#searchField{
    height: 50%
}

#cardWrapper{
    width: 100%;
    height: 100%;
    display: grid;
    grid-template-columns: repeat(10, 7rem);
    grid-template-rows: repeat(auto,7rem);
    align-content: center; 
    justify-content: center;
    margin-bottom: 3rem;
}

#cardHandler{
    height: 7rem;
    width: 7rem;
    min-width: none;
}

.v-application__wrap {
    min-height: fit-content;
}

</style>
