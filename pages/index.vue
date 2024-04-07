<template class="">
    <v-app class="" id="mainWrapper" v-if="loggedIn">
        <div class="flex justify-center items-center my-7 gap-4 w-full" id="searchWrapper">
            <v-btn color="secondary" class="my-auto" @click="showAccountOverlay">
                <v-icon icon="mdi-account"></v-icon>
            </v-btn>
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
            <v-btn color="success" class="my-auto" @click="showAddOverlay">Add</v-btn>
        </div>
        
        <div class="p-2 gap-5 flex flex-wrap justify-center h-full w-full" >
            <cardGen class="h-28 w-28 break-words" v-for="(i,k) in Object.keys(changingStack)" :key="reRenderComp" :word="i" :meaning="changingStack[i]" @deleted="deleteData"></cardGen>
        </div>


        <v-overlay v-model="overlayAddHandler" class="flex justify-center items-center">
            <overlayAdd @add="submitCard"></overlayAdd>
        </v-overlay>

        <v-overlay v-model="overlayAccountHandler" class="flex justify-center items-center">
            <overlayAccount :loggedInAcc="loggedIn" @logging="setLogin" @logout="setLogout"></overlayAccount>
        </v-overlay>
    </v-app>
    <v-app v-else>
            <overlayAccount :loggedInAcc="false" @logging="setLogin"></overlayAccount>
    </v-app>
</template>

<script>

import cardGen from "~/components/cardGen.vue"
import overlayAdd from "~/components/overlayAdd.vue"
import overlayAccount from "~/components/overlayAccount.vue"

export default {
    name: "indexPage",
    components:{
        cardGen,
        overlayAdd,
        overlayAccount
    },
    data(){
        return {
            words: {},
            searchWord: "",
            searchedStack: {},
            changingStack: {},
            overlayAddHandler: false,
            overlayAccountHandler: false,
            reRenderComp: false,
            loggedIn: false
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
            
            this.fetchData()

        },

        async fetchData(){
            const c2 = useCookie("jwt")
            console.log("Cookie value: ", c2.value);
            let dataObtained = await useFetch("http://localhost:8000/getAll", {
                method: "post",
                credentials:"include",
                Headers:{"Content-type": "application/json"},
                body:{
                    "token": c2.value
                }
            })
            let processed = JSON.parse(JSON.stringify(dataObtained["data"]));
            let finalData = Object.values(processed)[3];

            let passed = []
            finalData.forEach((e)=>{
                passed=[e["Username"],e["meaning1"],e["meaning2"],e["meaning3"],e["meaning4"],e["meaning5"]];
                this.searchedStack[e['word']] = passed;
            });

            console.log(this.searchedStack);
            this.changingStack = this.searchedStack;
            
        },

        showAddOverlay(){
            this.overlayAddHandler = !this.overlayAddHandler;
        },
        showAccountOverlay(){
            this.overlayAccountHandler = !this.overlayAccoundHandler;
        },

        async submitCard(objectWord){
            let nword = objectWord["newWrd"];
            let means = objectWord["means"];
            console.log(nword, means);
            if(nword=="" || means[0]==""){
                return
            }else{
                const c3 = useCookie("jwt")
                const request2 = await $fetch('http://localhost:8000/add', {
                    method: "POST",
                    Headers:{"Content-type": "application/json"},
                    body:JSON.stringify({"word": nword, "token": c3.value, "meaning1": means[0], "meaning2": means[1], "meaning3": means[2], "meaning4": means[3], "meaning5": means[4]})
                });
                //this.overlayAdd = false;
                this.fetchData();
            }
        },

        setLogin(){
            this.loggedIn=true;
            this.fetchData();
        },
        async setLogout(){
            this.loggedIn=false;
            this.searchedStack = {}
            this.changingStack = {}
            this.overlayAccountHandler = false
            let c = useCookie("jwt")
            c.value=null
            await $fetch("http://localhost:8000/logout", {
                method: "post",
                credentials: "same-origin"
            })
            this.$forceUpdate()
        }

    },
    watch: {
        searchWord(){
            this.search();
        }
    },
    created() {
        this.searchedStack = this.words;
        const c1 = useCookie("jwt");
        console.log(c1.value);
        if(c1.value){
            this.loggedIn=true;
            this.fetchData();
        }else{
            this.loggedIn=false;
        }
    },
    
}

</script>

<style>

html{
    background-color: white;
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
