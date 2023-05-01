<template>
    <div>
        <div class="header-container">
            <h1>Weapon Mods</h1>
        </div>
        <v-autocomplete ref="searchInput" label="Search Weapon Mods" :items="allPerkNames" v-model="selectedPerk"
            @update:model-value="scrollToPerk" variant="solo" clearable color="#60b3e3" class="SearchBar"></v-autocomplete>
        <v-combobox label="Select Weapons" :items="allWeaponNames" v-model="selectedWeapons" variant="solo" chips clearable
            color="#60b3e3" class="wmodsBar" multiple >
            <!-- Custom chip slot -->
            <template v-slot:selection="{ item, index, selectItem }">
                <v-chip class="custom-chip" :key="JSON.stringify(item)" size="x-small" color="#60b3e3">
                    {{ item }}
                </v-chip>
            </template>>
        </v-combobox>
        <v-expansion-panels v-if="perksData" multiple v-model="activePanels" class="expansions">
            <v-expansion-panel v-for="(perksByRarity, pool) in perksData.wmods.pools" :key="pool" :ref="pool">
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
                                        <!-- Display chips for selected weapons with dmgMod -->
                                        <div
                                            v-if="clientReady && perkObj[Object.keys(perkObj)[0]].dmgMod && selectedWeapons.length">
                                            <v-chip
                                                v-for="weapon in getSortedWeapons(perkObj[Object.keys(perkObj)[0]].dmgMod)"
                                                :key="weapon"
                                                :style="{ background: calculateChipColor(perkObj[Object.keys(perkObj)[0]].dmgMod[weapon], perkObj[Object.keys(perkObj)[0]].dmgMod) }"
                                                class="dmgmod-chip" size="x-small">
                                                {{ weapon }}: {{ perkObj[Object.keys(perkObj)[0]].dmgMod[weapon] }}%
                                            </v-chip>
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
        <!-- Navigation links -->
    </div>
</template>
  
  <!-- rest of the code remains the same -->
  
  
<script>
export default {

    inject: ['selectedVersion', 'selectedData'],

    mounted() {
        // Update clientReady to true in the mounted hook
        this.clientReady = true;

        // Check if window is defined (client-side environment)
        if (typeof window !== 'undefined') {
            // Load selected weapons from localStorage
            const cachedSelectedWeapons = localStorage.getItem('selectedWeapons');
            if (cachedSelectedWeapons && JSON.parse(cachedSelectedWeapons).length > 0) {
                this.selectedWeapons = JSON.parse(cachedSelectedWeapons);
            } else {
                // If there are no selected weapons in localStorage or the array is empty, select all weapons by default
                this.selectedWeapons = this.allWeaponNames;
            }
        }
    },

    data() {
        return {
            isLoading: false,
            selectedPerk: null,
            activePanels: [],
            selectedWeapons: [],
            clientReady: false,
        };
    },
    computed: {
        allPerkNames() {
            // Create an array of all perk names
            const perkNames = [];
            if (this.perksData && this.perksData.wmods && this.perksData.wmods.pools) {
                for (const perksByRarity of Object.values(this.perksData.wmods.pools)) {
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
        allWeaponNames() {
            // Create a set to store unique weapon names
            const weaponNamesSet = new Set();
            if (this.perksData && this.perksData.wmods && this.perksData.wmods.pools) {
                for (const perksByRarity of Object.values(this.perksData.wmods.pools)) {
                    for (const perks of Object.values(perksByRarity)) {
                        for (const perkObj of perks) {
                            const perkDmgMod = perkObj[Object.keys(perkObj)[0]].dmgMod;
                            if (perkDmgMod) {
                                for (const weaponName of Object.keys(perkDmgMod)) {
                                    weaponNamesSet.add(weaponName);
                                }
                            }
                        }
                    }
                }
            }
            // Convert the set to an array and return it
            return Array.from(weaponNamesSet);
        },
        perksData() {
            return this.selectedData.value;
        },
    },
    watch: {
        // Save selected weapons to localStorage whenever there is a change
        selectedWeapons: {
            deep: true,
            handler(newValue) {
                // Check if window is defined (client-side environment)
                if (typeof window !== 'undefined') {
                    localStorage.setItem('selectedWeapons', JSON.stringify(newValue));
                }
            }
        }
    },
    methods: {
        getSortedWeapons(perkDmgMod) {
            console.log('getSortedWeapons is called:', perkDmgMod); // Debugging information
            return this.selectedWeapons
                .filter(weapon => perkDmgMod[weapon]) // Filter out weapons that don't have dmgMod value
                .sort((a, b) => perkDmgMod[b] - perkDmgMod[a]); // Sort in descending order based on dmgMod value
        },
        sanitizeId(perkName) {
            // Remove spaces and special characters to create a valid ID
            return perkName.replace(/[^a-zA-Z0-9]/g, '_');
        },
        calculateChipColor(value, dmgMod) {
            // Filter dmgMod to include only the selected weapons
            const filteredDmgMod = this.selectedWeapons.reduce((acc, weapon) => {
                if (dmgMod[weapon]) {
                    acc[weapon] = dmgMod[weapon];
                }
                return acc;
            }, {});

            // Extract values from filteredDmgMod and calculate min and max
            const values = Object.values(filteredDmgMod);
            const min = Math.min(...values);
            const max = Math.max(...values);

            // Handle the edge case where all values are the same
            if (max === min) return '#60b3e3';

            // Define the RGB values for the highest and lowest colors
            const highestColor = { r: 96, g: 179, b: 227 }; // RGB values for #60b3e3
            const lowestColor = { r: 42, g: 42, b: 42 }; // RGB values for #1E1E1E

            // Normalize the value to range [0, 1]
            const percentage = (value - min) / (max - min);

            // Interpolate the RGB values based on the percentage
            const r = Math.round(lowestColor.r + (highestColor.r - lowestColor.r) * percentage);
            const g = Math.round(lowestColor.g + (highestColor.g - lowestColor.g) * percentage);
            const b = Math.round(lowestColor.b + (highestColor.b - lowestColor.b) * percentage);

            return `rgb(${r}, ${g}, ${b})`;
        },
        scrollToPerk(perkName) {
            if (!perkName || !this.perksData) return;

            // Determine the pool and panel index containing the perk
            let panelIndex = -1;
            let poolName = '';
            const pools = this.perksData.wmods.pools;
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
                    this.$refs.searchInput.$refs.input.blur();
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
.expansions {
    max-width: 90%;
    justify-content: center;
    align-items: center;
    display: flex;
    margin: auto;
}

@keyframes flashing {
    0% {
        opacity: 1;
        width: 100%;
    }

    50% {
        opacity: 0.3;
    }

    100% {
        opacity: 1;
    }
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

.v-expansion-panel-title {
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



/* Custom chip styles */
.custom-chip {
    font-size: xx-small;
}
</style>

<style>
.SearchBar .v-input__control {
    width: 70%;
    margin: auto;
}

.wmodsBar .v-input__control {
    height: 100%;
    width: 80%;
    margin: auto;
}

.wmodsBar .v-input__control .v-field {
    color: #60b3e3 !important;

}
</style>
  
<style></style>