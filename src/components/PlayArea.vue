<!-- State/Logic (JS-like) -->
<script setup>
    import { ref } from 'vue';
    import Card from './Card.vue';
    import cards from '../data/cards.js'

    // setup helper functions
    const getNewCards = () => {
        // randomly selecting cards
        const typesToChoose = Array.from(Array(numTypes).keys());
        const newCards = Array(numCards);

        for (let i = 0; i < numCards; i++) {

            let index = Math.floor(Math.random() * typesToChoose.length)
            let cardType = typesToChoose[index];
            let x = Math.floor(Math.random() * gridWidth);
            let y = Math.floor(Math.random() * gridHeight);
            
            const cardsCovering = [];
            for (let id = 0; id < i ; id++) {
                let currentCard = newCards[id];
                let xDiff = x - currentCard.x;
                let yDiff = y - currentCard.y;
                if ((xDiff <=2) && (xDiff >= -2) && (yDiff <= 4) && (yDiff >= -4)) {
                    cardsCovering.push(id);
                    currentCard.cardsCoveredBy.push(i);
                }
            }
            

            newCards[i] = {
                uri: cards[cardType].uri,
                name: cards[cardType].name,
                x,
                y,
                cardType,
                selectedIndex: 0,
                isSelected: false,
                isCollapsed: false,
                cardsCoveredBy: [],
                cardsCovering
            };

            cardTotals[cardType]++;
            if (cardTotals[cardType] === numCardsPerType) {
                typesToChoose.splice(index, 1);
            }

            //cardCompRefs[i] = ref(null);
        }

        return newCards
    }

    // messages we can emit to parent (App)
    const emit = defineEmits(['gameOver']);

    const cardTotals = {
        0: 0,
        1: 0,
        2: 0,
        3: 0,
        4: 0,
        5: 0,
        6: 0,
        7: 0,
        8: 0,
        9: 0,
    };

    const selectedCardsByType = {
        0: [],
        1: [],
        2: [],
        3: [],
        4: [],
        5: [],
        6: [],
        7: [],
        8: [],
        9: [],
    }

    //const selectedCardsOrdered = ref([]);
    const gridWidth = 30;
    const gridHeight = 27;

    const numTypes = 10;
    // must be divisible by 3;
    const numCardsPerType = 9;

    const numCards = numTypes * numCardsPerType;
    const numCardsRemaining = ref(numCards);

    const selectedCardLimit = 7;
    const totalCardsSelected = ref(0);
    const totalCardsSelectedTrue = ref(0);
    
    // cardPool is an array of card objects
    const cardPool = ref(getNewCards());

    // events
    const onSelectCard = (id) => {
        if (totalCardsSelected.value === selectedCardLimit)
            return;
        let selectedCard = cardPool.value[id];
        let cardType = selectedCard.cardType;
        selectedCard.selectedIndex = totalCardsSelected.value;
        // set isSelected flag
        selectedCard.isSelected = true;

        // increment totalCardsSelected and populate selectedcards array
        totalCardsSelected.value += 1;
        totalCardsSelectedTrue.value += 1;
        numCardsRemaining.value -= 1;
        selectedCardsByType[cardType].push(id);
        
        // stop card from covering other cards
        for (let currentId of selectedCard.cardsCovering) {
            let coveredArr = cardPool.value[currentId].cardsCoveredBy;
            coveredArr.splice(coveredArr.indexOf(id), 1);
        }

    
        //let collapseIds = Array(3);
        if (selectedCardsByType[cardType].length === 3) {
            totalCardsSelectedTrue.value -= 3;
            // collapse
            /*for (let i = 0; i < 3; i++) {
                let cardId = selectedCardsByType[cardType][i];
                let card = cardPool.value[cardId];
                collapseIds[i] = card.selectedIndex;
                setTimeout(() => {
                    card.isCollapsed = true;
                    
                },
                    1200);
            } */
            
            setTimeout(() => {
                // decrement totalCardsSelected
                
                for (let i = 0; i < 3; i++) {
                    let cardId = selectedCardsByType[cardType][i];
                    let card = cardPool.value[cardId];
                    card.isCollapsed = true;
                }
                
                setTimeout(() => {
                    totalCardsSelected.value -= 3;

                    for (let j = 0; j < numTypes; j++) {
                        for (let k = 0; k < selectedCardsByType[j].length; k++) {
                            if (j !== cardType || k >= 3) {
                                let cardId = selectedCardsByType[j][k];
                                let decrement = 0;
                                for (let i = 0; i < 3; i++) {
                                    let collapsedCardId = selectedCardsByType[cardType][i];
                                    //let card = cardPool.value[cardId];
                                    if (cardPool.value[collapsedCardId].selectedIndex < cardPool.value[cardId].selectedIndex) {
                                        decrement++;
                                    }
                                }
                                cardPool.value[cardId].selectedIndex -= decrement;
                            }
                        }
                    }
                    for (let i = 0; i < 3; i++) {
                        let cardId = selectedCardsByType[cardType][i];
                        let card = cardPool.value[cardId];
                        //collapseIds[i] = card.selectedIndex;

                        // adjust selectedIndex of selected cards
                        // for (let j = 0; j < numTypes; j++) {
                        //     if (j !== cardType) {
                        //         for (const k of selectedCardsByType[j]) {
                        //             if (cardPool.value[cardId].selectedIndex < cardPool.value[k].selectedIndex) {
                        //                 cardPool.value[k].selectedIndex--;
                        //             }
                        //         }
                        //     }
                        // }
                    }
                    for (let dummy = 0; dummy < 3; dummy++)
                        selectedCardsByType[cardType].shift();

                    // if we have no cards left, we win!
                    if (numCardsRemaining.value === 0)
                        emit('gameOver', true);
                }, 250);
            }, 600);
            // adjust selectedIndex of selected cards
            /*setTimeout(() => {
                totalCardsSelected.value -= 3;
                for (const ref of cardCompRefs.value) {
                    ref.processCollapse(collapseIds);
                }
                for (let dummy = 0; dummy < 3; dummy++)
                    selectedCardsByType[cardType].shift();
            }, 1250); */
        }
        
        // if we didn't get a match and our hand is full, it's game over!
        else if (totalCardsSelectedTrue.value === selectedCardLimit) {
            emit('gameOver', false);
        }
        
    }

    const restart = () => {
        // reset data in static-like objects
        for (let i = 0; i < numTypes; i++) {
            cardTotals[i] = 0;
            selectedCardsByType[i] = [];
        }
        // reset refs
        numCardsRemaining.value = numCards;
        totalCardsSelected.value = 0;
        totalCardsSelectedTrue.value = 0;

        // get and setup new set of cards
        Object.assign(cardPool.value, getNewCards());

    }

    defineExpose({ restart });

    </script>


<!-- View (HTML-like) -->
<template>
    <div class="root__pa">
        <Card v-for="n in numCards" 
                @selectCard="onSelectCard"
                :id="n-1" :x="cardPool[n-1].x" :y="cardPool[n-1].y"
                :uri="cardPool[n-1].uri" :name="cardPool[n-1].name" :cardType="cardPool[n-1].cardType"
                :cardsSelected="totalCardsSelected" :selectedIndex="cardPool[n-1].selectedIndex"
                :isSelected="cardPool[n-1].isSelected" :isCollapsed="cardPool[n-1].isCollapsed"
                :isCovered="cardPool[n-1].cardsCoveredBy.length > 0"
                :gridWidth="gridWidth" :gridHeight="gridHeight"
        />

    </div>
</template>

<!-- Styles (CSS-like) -->
<style scoped>
    .root__pa {
        position: absolute;
        height: 100%;
        left: 0;
        width: 100%;
        bottom: 0;
    }
</style>