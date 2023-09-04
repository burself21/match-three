<!-- State/Logic (JS-like) -->
<script setup>
    import { ref, reactive, computed } from 'vue'

    const props = defineProps({
        id: Number,
        x: Number,
        y: Number,
        uri: String,
        name: String,
        cardType: Number,
        cardsSelected: Number,
        selectedIndex: Number,
        isSelected: Boolean,
        isCollapsed: Boolean,
        isCovered: Boolean,
        gridWidth: Number,
        gridHeight: Number
    });

    // const color = computed(() => {
    //     return card.value ? card.value.color : defaultColor;
    // })

    //card.value = cards[props.id];

    //const isSelected = ref(false);

    //const selectedIndex = ref(0);

    const size = '7%';
    const paddingSize = '0.83%';
    const borderSize = '0.33vw';
    const appWidth = 0.5;
    const borderRadius = '0.66vw';
    const totalSize = 8.66 + 0.33 / appWidth;
    const aspectRatio = 4/3;
    const cardholderHeight = totalSize * aspectRatio + 2 * 0.6 * aspectRatio / appWidth;

    const bgClass = computed(() => props.name == "Semantic" ? "semantic_bg" : "default_bg");

    const select = () => {
        if (!props.isSelected) {
            emit('selectCard', props.id, props.cardType);
        }
    }


    const emit = defineEmits(['selectCard']);

    // collapseIds should contain the selectedIndex of the 3 collapsed cards
    /*const processCollapse = (collapseIds) => {
        if (!props.isSelected || collapseIds.includes(props.selectedIndex))
            return;
        let decrement = 0;
        for (const collapseId of collapseIds) {
            if (collapseId < props.selectedIndex) {
                decrement++;
            }
        }
        console.log(selectedIndex);
        console.log(collapseIds);
        console.log(decrement);
        props.selectedIndex -= decrement;
    } */

    //defineExpose({});

</script>

<!-- View (HTML-like) -->
<template>
    <Transition name="root">
        <!--<div v-if="!isCollapsed" class="root__card"
            :class="[props.color, {selected: isSelected, collapsed: isCollapsed}]"
            @click="select">

        </div> -->

        <div v-if="!isCollapsed" class="root__card noselect"
            :class="bgClass, {selected: isSelected, collapsed: isCollapsed, covered: isCovered }"
            @click="select">
            <img :src="props.uri" :alt="`${props.name} logo`" />
            <Transition name="overlay">
                <div v-if="isCovered" class="card_overlay"></div>
            </Transition>
        </div>
    </Transition>
</template>

<!-- Styles (CSS-like) -->
<style scoped>
    .root__card {
        width: v-bind('size');
        aspect-ratio: 1 / 1;
        border-radius: v-bind('borderRadius');
        border-bottom: v-bind('borderSize') solid black;
        border-right: v-bind('borderSize') solid black;
        position: absolute;
        left: calc(v-bind('props.x * (100 - totalSize) / (props.gridWidth - 1)') * 1%);
        bottom: calc(v-bind('props.y * (100 - totalSize * aspectRatio - cardholderHeight - 6) / (props.gridHeight - 1) + cardholderHeight + 6') * 1%);
        padding: v-bind('paddingSize');
    }

    .root__card > img {
        width: 100%;
        height: 100%;
        display: block;
        pointer-events: none;
    }

    .root__card:hover {
        cursor: pointer;
    }

    .card_overlay {
        width: 100%;
        height: 100%;
        position: absolute;
        background-color: darkslategray;
        opacity: 0.7;
        z-index: 0;
        bottom: 0;
        left: 0;
        top: 0;
        right: 0;
        border-radius: 0.66vw 0.45vw 0.58vw 0.42vw;
        padding: v-bind('paddingSize');
        
    }

    .default_bg {
        background-color: lightgray;
    }
    .semantic_bg {
        background-color: #35bdb3;
    }

    .selected {
        bottom: 0;
        transform: translateY(-0.6vw);
        left: calc(v-bind('props.selectedIndex * totalSize + 17.38') * 1% + 0.6vw - 0.3vw);
        transition: all 0.5s;
        pointer-events: none;
        z-index: 1;
    }

    .collapsed {
        display: none;
        transition: all 1.5s;
    }

    .root-leave-active {
        transition: opacity 1s ease;
    }

    .root-leave-to {
        opacity: 0;
    }

    .overlay-leave-active {
        transition: opacity 0.3s ease;
    }

    .overlay-leave-to {
        opacity: 0;
    }

    .covered {
        pointer-events: none;
    }

    .noselect {
        -webkit-touch-callout: none;
        /* iOS Safari */
        -webkit-user-select: none;
        /* Safari */
        -khtml-user-select: none;
        /* Konqueror HTML */
        -moz-user-select: none;
        /* Firefox */
        -ms-user-select: none;
        /* Internet Explorer / Edge */
        user-select: none;
        /* Non-prefixed version */
    }

</style>
