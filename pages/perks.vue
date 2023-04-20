<template>
    <div>
        <div class="header-container">
            <h1>Perks</h1>
        </div>
        <v-expansion-panels v-if="perksData" multiple>
            <v-expansion-panel v-for="(perksByRarity, pool) in perksData.perks.pools" :key="pool">
                <v-expansion-panel-header class="pool-header">{{ pool }}</v-expansion-panel-header>
                <v-expansion-panel-content>
                    <div v-for="(perks, rarity) in perksByRarity" :key="rarity">
                        <v-expansion-panels multiple>
                            <v-expansion-panel v-for="perkObj in perks" :key="Object.keys(perkObj)[0]"
                                :class="getRarityGlowClass(rarity)">
                                <v-expansion-panel-header class="perk-header">
                                    {{ Object.keys(perkObj)[0] }}
                                </v-expansion-panel-header>

                                <v-expansion-panel-content>
                                    <!-- Display formatted perk description -->
                                    <div v-if="perkObj[Object.keys(perkObj)[0]].description"
                                        v-html="highlightNumbers(perkObj[Object.keys(perkObj)[0]].description, true, 'Description', rarity)">
                                    </div>
                                    <!-- Display formatted perk details -->
                                    <div v-if="perkObj[Object.keys(perkObj)[0]].details"
                                        v-html="highlightNumbers(perkObj[Object.keys(perkObj)[0]].details, true, 'Details', rarity)">
                                    </div>
                                </v-expansion-panel-content>


                            </v-expansion-panel>
                        </v-expansion-panels>
                    </div>
                </v-expansion-panel-content>
            </v-expansion-panel>
        </v-expansion-panels>
        <!-- Navigation links -->
    </div>
</template>
  
  <!-- rest of the code remains the same -->
  
  
<script>
import data from '~/assets/data/data.json';
export default {

    inject: ['selectedVersion'],

    created() {
        //log the selected version

        this.perksData = data[this.selectedVersion];
    },

    data() {
        return {
            perksData: null,
            isLoading: false,
        };
    },
    methods: {
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

<style>
.header-container {
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 2rem;
}

.number-highlight {
    width: 200px;
    height: 200px;

    /* Set border radius to 50% to create an ellipse */
    border-radius: 100%;

    /* Set the background color of the ellipse */
    background-color: #60b3e363;

    /* Add a blur effect to the ellipse */
    box-shadow: 0 0 20px 0px #60b3e3;
}

.number-highlight-greed {
    width: 200px;
    height: 200px;

    /* Set border radius to 50% to create an ellipse */
    border-radius: 100%;

    /* Set the background color of the ellipse */
    background-color: #ff00002d;

    /* Add a blur effect to the ellipse */
    box-shadow: 0 0 20px 0px #ff0000;
}

.number-highlight-epic {
    width: 200px;
    height: 200px;

    /* Set border radius to 50% to create an ellipse */
    border-radius: 100%;

    /* Set the background color of the ellipse */
    background-color: #b300b338;

    /* Add a blur effect to the ellipse */
    box-shadow: 0 0 20px 0px #b300b3;
}

.number-highlight-legendary {
    width: 200px;
    height: 200px;

    /* Set border radius to 50% to create an ellipse */
    border-radius: 100%;

    /* Set the background color of the ellipse */
    background-color: #ffff0027;

    /* Add a blur effect to the ellipse */
    box-shadow: 0 0 20px 0px #ffff00;
}

.number-highlight-rare {
    width: 200px;
    height: 200px;

    /* Set border radius to 50% to create an ellipse */
    border-radius: 100%;

    /* Set the background color of the ellipse */
    background-color: #0000ff5b;

    /* Add a blur effect to the ellipse */
    box-shadow: 0 0 20px 0px #0000ff;
}

.perk-card {
    border-radius: 10px;
    margin: 13px;
}

.rarity-rare {
    box-shadow: 0 0 10px 3px blue, inset 0 0 5px 2px blue;
}

.rarity-epic {
    box-shadow: 0 0 10px 3px purple, inset 0 0 5px 2px purple;
}

.rarity-legendary {
    box-shadow: 0 0 10px 3px yellow, inset 0 0 5px 2px yellow;
}

.rarity-greed {
    box-shadow: 0 0 10px 3px red, inset 0 0 5px 2px red;
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
}</style>
  