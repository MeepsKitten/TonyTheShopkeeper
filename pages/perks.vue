<template>
    <div>
        <div class="header-container">
            <h1>Perks</h1>
        </div>
        <v-autocomplete ref="searchInput" label="Search Perks" :items="allPerkNames" v-model="selectedPerk"
            @update:model-value="scrollToPerk" clearable color="#60b3e3" class="SearchBar" variant="solo">
        </v-autocomplete>
        <v-expansion-panels v-if="perksData" multiple v-model="activePanels" class="expansions">
            <v-expansion-panel v-for="(perksByRarity, pool) in perksData.perks.pools" :key="pool" :ref="pool">
                <v-expansion-panel-title class="pool-header">
                    {{ pool }}
                </v-expansion-panel-title>
                <v-expansion-panel-text>
                    <div v-for="(perks, rarity) in perksByRarity" :key="rarity">
                        <template v-if="perks.length">
                            <v-expansion-panels multiple variant="popout" style="margin-top: 20px; margin-bottom: 20px;">
                                <v-expansion-panel v-for="perkObj in perks" :key="Object.keys(perkObj)[0]"
                                    :class="getRarityGlowClass(rarity)" :id="sanitizeId(Object.keys(perkObj)[0])">
                                    <v-expansion-panel-title class="perk-header" :ref="Object.keys(perkObj)[0]">
                                        {{ Object.keys(perkObj)[0] }}
                                    </v-expansion-panel-title>
                                    <v-expansion-panel-text>
                                        <!-- Display formatted perk description -->
                                        <div v-if="perkObj[Object.keys(perkObj)[0]].description"
                                            v-html="highlightNumbers(perkObj[Object.keys(perkObj)[0]].description, true, 'Description', rarity)">
                                        </div>
                                        <!-- Display formatted perk details -->
                                        <div v-if="perkObj[Object.keys(perkObj)[0]].details"
                                            v-html="highlightNumbers(perkObj[Object.keys(perkObj)[0]].details, true, 'Details', rarity)">
                                        </div>
                                    </v-expansion-panel-text>
                                </v-expansion-panel>
                            </v-expansion-panels>
                        </template>
                    </div>
                </v-expansion-panel-text>
            </v-expansion-panel>
        </v-expansion-panels>
    </div>
</template>
  
  <!-- rest of the code remains the same -->
  
  
<script>
export default {

    inject: ['selectedVersion' , 'selectedData'],


    data() {
        return {
            isLoading: false,
            selectedPerk: null,
            activePanels: [],
        };
    },
    computed: {
        allPerkNames() {
            // Create an array of all perk names
            const perkNames = [];
            if (this.perksData && this.perksData.perks && this.perksData.perks.pools) {
                for (const perksByRarity of Object.values(this.perksData.perks.pools)) {
                    for (const perks of Object.values(perksByRarity)) {
                        for (const perkObj of perks) {
                            const perkName = Object.keys(perkObj)[0];
                            perkNames.push(perkName);
                        }
                    }
                }
            }
            return perkNames;
        },
        perksData() {
            return this.selectedData.value;
        },
    },
    methods: {
        sanitizeId(perkName) {
            // Remove spaces and special characters to create a valid ID
            return perkName.replace(/[^a-zA-Z0-9]/g, '_');
        },

        scrollToPerk(perkName) {
            console.log('scrollToPerk', perkName);
            if (!perkName || !this.perksData) return;

            // Determine the pool and panel index containing the perk
            let panelIndex = -1;
            let poolName = '';
            const pools = this.perksData.perks.pools;
            const poolKeys = Object.keys(pools);
            for (let i = 0; i < poolKeys.length; i++) {
                const poolKey = poolKeys[i];
                const rarities = pools[poolKey];
                if (Object.values(rarities).flat().some(perkObj => Object.keys(perkObj)[0] === perkName)) {
                    panelIndex = i;
                    poolName = poolKey;
                    break;
                }
            }

            // Set the activePanels data property to the index of the panel
            this.activePanels = [panelIndex];

            // Use the $nextTick method to wait for the DOM to update
            this.$nextTick(() => {
                setTimeout(() => {
                    // Determine the perk element
                    const perkElementId = this.sanitizeId(perkName);
                    const perkElement = document.getElementById(perkElementId);

                    if (perkElement) {
                        // Calculate the top position of the perk element with an offset (e.g., 50 pixels)
                        const topPosition = perkElement.getBoundingClientRect().top + window.pageYOffset - 100;

                        // Scroll to the adjusted position
                        window.scrollTo({ top: topPosition, behavior: 'smooth' });

                        // Add the flash-glow class to the perk element
                        perkElement.classList.add('flash-glow');

                        // Remove the flash-glow class after a certain duration (e.g., 5 seconds)
                        setTimeout(() => {
                            perkElement.classList.remove('flash-glow');
                        }, 5000);
                    } else {
                        console.log('Could not find perk element:', perkName);
                    }
                    if (this.$refs.searchInput.$refs.input) {
                this.$refs.searchInput.$refs.input.blur();
            }
                }, 400);
            });
        },

        getRarityGlowClass(rarity) {
            return `perk-card rarity-${rarity.toLowerCase()}`;
        },

        getRarityGlowStyle(rarity) {
            // Define a mapping of rarity to the desired box-shadow color
            const boxShadowColors = {
                rare: 'blue',
                epic: 'purple',
                legendary: 'yellow',
                greed: 'red',
            };
            const color = boxShadowColors[rarity.toLowerCase()] || 'grey';
            // Return the box-shadow style with the appropriate color
            return {
                boxShadow: `0 0 10px 3px ${color}, inset 0 0 5px 2px ${color}`,
            };
        },

        highlightNumbers(text, isList, header = '', rarity = '') {
            // Determine the appropriate class based on rarity
            const className = `number-highlight-${rarity.toLowerCase()}`;

            // Use regular expression to match numbers and wrap them in a span element with the appropriate class
            const highlightedText = text.replace(/(\d+)/g, `<span class="${className}">$1</span>`);

            // If the text is a list, wrap it with <ul> tags
            return isList
                ? `<strong>${header}:</strong><ul><li>${highlightedText.split('*').filter(line => line.trim()).join('</li><li>')}</li></ul>`
                : highlightedText;
        },

    },
};
</script>


<style scoped>
@keyframes flashing {
    0% {
        opacity: 1;
    }

    50% {
        opacity: 0.3;
    }

    100% {
        opacity: 1;
    }
}

.expansions {
    max-width: 90%;
    justify-content: center;
    align-items: center;
    display: flex;
    margin: auto;
}

.flash-glow {
    animation: flashing 1s linear infinite;
}

.header-container {
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 2rem;
}

.number-highlight {
    text-shadow: 
        0 0 5px #777777,
        0 0 15px #777777,
        0 0 25px #777777;
}

.number-highlight-greed {
    text-shadow: 
        0 0 5px #ff0000,
        0 0 15px #ff0000,
        0 0 25px #ff0000;
}

.number-highlight-epic {
    text-shadow: 
        0 0 5px purple,
        0 0 15px purple,
        0 0 25px purple;
}

.number-highlight-legendary {
    text-shadow: 
        0 0 5px #ffff00,
        0 0 15px #ffff00,
        0 0 25px #ffff00;
}

.number-highlight-rare {
    text-shadow: 
        0 0 5px #0000ff,
        0 0 15px #0000ff,
        0 0 25px #0000ff;
}

.perk-card {
    border-radius: 3px;
    
}


.rarity-rare {
    box-shadow: 0 0 15px -1px blue;
}

.rarity-epic {
    box-shadow: 0 0 15px -1px purple;
}

.rarity-legendary {
    box-shadow: 0 0 15px -1px yellow;
}

.rarity-greed {
    box-shadow: 0 0 15px -1px red;
}

.v-expansion-panel.perk-card::after {
    border: none;
    /* If the line is caused by a border */
    background: none;
    /* If the line is caused by a background */
}

.v-expansion-panel-header {
    user-select: text;
    /* Allow text selection */
}

.perk-header {
    font-size: 25px;
    /* Change to the desired font size */
}

.pool-header {
    font-size: 35px;
    /* Change to the desired font size */
}
</style>

<style>
.SearchBar .v-input__control {
    
    width: 70%;
    margin: auto;
}
</style>
  