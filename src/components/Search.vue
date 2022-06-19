<template>
    <div id="search-bar-container">
        <div class="input-group">
                <input ref="input" class="form-control"
                        id="search-field" type="search"
                        :placeholder="placeholderTag" 
                        v-model="inputAdress"
                        @keyup="onKeypress($event)" 
                        @keydown.down="onKeyDown"
                        @keydown.up="onKeyUp"
                        @keyup.enter="onEnter">
        </div>
        <div class="list-group" v-if="isOpen">
            <div class="list-group-item" v-for="(suggestion, i) in suggestionsList"
                :key="i"
                @click="onClickSuggest(suggestion)"
                @keydown.esc="isOpen=false"
                @mouseover="onMouseover(i)"
                @mouseout="onMouseLeave"
                :class="{ 'is-active': i === index }">
                <span class="search-result-label">
                    {{ suggestion.properties.label }}
                </span><br>
                <span class="search-result-context">
                    {{ suggestion.properties.context }}
                </span>
                <span class="search-result-type">
                    {{ suggestion.properties.type }}
                </span>
            </div>
        </div>
    </div>        
</template>

<script>
export default {
    name:'SearchBar',
        data() {
        return {
            apiAdresse:"https://api-adresse.data.gouv.fr/search/?q=",
            searchType:'address',
            inputAdress:'',
            isOpen:false,
            index:0,
            suggestionsList:[]
        }
    },
    computed: {
        placeholderTag() {
            if(this.searchType == "address") {
                return "Saisissez une adresse ..."
            } else {
                return "Saisissez un nom ou numéro de département ..."
            }
        }
    },
    watch: {
        inputAdress() {
            if(!this.inputAdress) {
                this.isOpen = !this.isOpen;
                this.index = 0;
                this.suggestionsList = [];
            }
        }
    },
    methods: {
        returnType(type) {
            switch (type) {
                case "housenumber":
                    return type = "Numéro";
                case "street":
                    return type = "Rue";
                case "locality":
                    return type = "Lieu-dit";
                case "municipality":
                    return type = "Commune";
            }
        },
        onChange(e) {
            this.searchType = e.target.name;
            this.inputAdress = '';
            this.$emit('searchType', this.searchType)
        },
        onKeypress() {
            this.isOpen = true;
            let val = this.inputAdress;
            
            if(val === '') { this.isOpen = false }
            if (val != undefined && val != '') {
                fetch(`${this.apiAdresse}${val}&autocomplete=1`)
                    .then(res => res.json())
                    .then(res => {
                        let suggestions = [];
                        if(res && res.features) {
                            let features = res.features;
                            features.forEach(e => {
                                e.properties.type = this.returnType(e.properties.type)
                                suggestions.push(e);
                            });
                        }
                        this.suggestionsList = suggestions;
                        }).catch(error => console.error(error));
            }
        },
        onKeyUp() {
            if (this.index > 0) {
                this.index = this.index - 1;
            }
        },
        onKeyDown() {
            if (this.index < this.suggestionsList.length) {
                this.index = this.index + 1;
            }
        },
        onMouseover(e) {
            this.index = e;
        },
        onMouseLeave() {
            this.index = -1;
        },
        onEnter() {
            this.isOpen = !this.isOpen;
            if(this.suggestionsList.length != 0) {
                let suggestion = this.suggestionsList[this.index];
                    this.inputAdress = suggestion.properties.label;
                    // send data
                    this.$emit("searchResult", {
                        resultType:'address',
                        resultCoords: [suggestion.geometry.coordinates[1],suggestion.geometry.coordinates[0]], 
                        resultLabel: suggestion.properties.label
                    });
                    
                this.suggestionsList = [];
                this.index = -1;
            }
        },
        onClickSuggest(suggestion) {            
            // reset search
            this.inputAdress = suggestion.properties.label;
            // get address coordinates to pass to map
            let coordinates = suggestion.geometry.coordinates;
            let latlng_adress = [coordinates[1], coordinates[0]];

            // send data
            this.$emit("searchResult", {
                resultType:'address',
                resultCoords: latlng_adress, 
                resultLabel: this.inputAdress
            });
            this.suggestionsList = [];
            this.isOpen = !this.isOpen;

        },
        handleClickOutside(evt) {
            if (!this.$el.contains(evt.target)) {
              this.isOpen = false;
              this.index = -1;
            }
        },
        clearSearch() {
            this.inputAdress = '';
            document.getElementById("search-field").value = "";
            this.$emit('clearSearch')
        }
    },
    mounted() {
        document.addEventListener("click", this.handleClickOutside);
        document.addEventListener("keyup", (e) => {
            if(e.key === "Escape") {
                this.isOpen = false;
                this.index = -1;

            }
        });
        
    },
    unmounted() {
        document.removeEventListener("click", this.handleClickOutside);
        document.removeEventListener("keyup", (e) => {
            if(e.key === "Escape") {
                this.isOpen = false;
                this.index = -1
                this.handleClickOutside();
            }
        });
    }
}
</script>

<style scoped>
:root {
	--bleu-anct:rgb(41, 49, 115);
	--bleu-second:#5770be;
	--font-size-global:1.1em;
}


#search-bar-container {
	position: relative;
	margin-bottom: 10px;
}


.input-group-prepend {
	position: relative;
}



.list-group {
	width: 100%;
	position: absolute;
	z-index: 1;
    overflow-y: scroll;
}


.list-group-item {
	box-shadow: 0 10px 20px rgba(0,0,0,.12), 0 4px 8px rgba(0,0,0,.06);
	background-color: white;
	cursor: pointer;
	transition: .05	s;
}

.list-group-item.is-active {
	background-color: rgb(41, 49, 115);
	font-weight: bolder;
	color:white;
	display: block;
}

.search-result-label {
	/* font-family: 'Marianne-Bold'; */
    font-weight: bold;
}

.search-result-context {
	font-weight: lighter;
	font-size: .9em;
}

.search-result-type {
	float: right;
	font-size: .85em;
}
</style>

