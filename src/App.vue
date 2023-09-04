<script setup>
    import PlayArea from './components/PlayArea.vue';
    import GameOverModal from './components/GameOverModal.vue';
    import InstructionModal from './components/InstructionModal.vue/';

    import { ref, computed } from 'vue';

    const appArea = 0.5;

    const language = ref("English");
    const toggleColor1 = computed(() => {
        return language.value === "English" ? "#494d7e" : "#fff";
    })
    const toggleColor2 = computed(() => {
        return language.value === "Chinese" ? "#494d7e" : "#fff";
    })

    const playAreaRef = ref(null);

    const isInstructionModalVisible = ref(true);
    const isGameOverModalVisible = ref(false);
    const hasWon = ref(false);

    const toggleLanguage = () => {
        if (language === "English")
            language = "Chinese"
        else
            language = "English"
    }

    const initiateGameOver = (won) => {
        hasWon.value = won;
        isGameOverModalVisible.value = true;
    }

    const restartGame = () => {
        hasWon.value = false;
        isGameOverModalVisible.value = false;
        playAreaRef.value.restart();
    }

    const closeInstructionModal = () => {
        isInstructionModalVisible.value = false;
    }

    // for testing purposes
    const testLoss = () => {
        hasWon.value = false;
        isGameOverModalVisible.value = true;
    }

    const testWin = () => {
        hasWon.value = true;
        isGameOverModalVisible.value = true;
    }

</script>

<template>
    <div id="container">
        <div id="header_area">
            <div id="header_title">
                Match Three
            </div>
            <div id="header_language">
                <div class="switch">
                    <input id="language-toggle" class="check-toggle check-toggle-round-flat" 
                           type="checkbox"
                           v-model="language"
                           true-value="English"
                           false-value="Chinese">
                    <label for="language-toggle"></label>
                    <span class="english">EN</span>
                    <span class="chinese">中文</span>
                </div>
            </div>
        </div>
        <div id="wrapper">
            <div id="main">
                <PlayArea 
                    ref="playAreaRef"
                    :appArea="appArea" @gameOver="initiateGameOver" 
                />
                <div id="cardholder"></div>
            </div>
        </div>
    </div>
    <InstructionModal 
        v-show="isInstructionModalVisible"
        @startGame="closeInstructionModal"
        @changeLanguage="toggleLanguage"
        :language="language"
    />
    <GameOverModal
        v-show="isGameOverModalVisible"
        @restartGame="restartGame"
        :win="hasWon"
        :language="language"
    />
</template>

<style>

    body {
        margin: 0;
    }

    #container {
        background-color: #272744;
        height: 96vh;
        padding-top: 4vh;
    }
    
    #header_area {
        height: 10vh;
        width: calc(37.5vw + 9px + 15px);
        margin-left: calc(37.5vw);
        display: flex;
        flex-direction: row;
        justify-content: space-between;
        margin-bottom: 5px;
    }

    #header_title {
        border-radius: 12px 12px 0px 0px;
        background-color: #494d7e;/*#8b6d9c; /*#4AAE9B;/*#add8e6;*/
        height: 100%;
        width: 25vw;
        text-align: center;
        font-size: 3.5vw;
        display: flex;
        justify-content: center;
        align-items: center;
        font-family: sans-serif;
        border: 5px solid black;
        border-bottom: 0;
        box-sizing: border-box;
        color: rgb(250, 198, 206);
        user-select: none;
    }

    #header_language {
        height: 100%;
        display: flex;
        justify-content: center;
        align-items: end;
        font-family: sans-serif;
    }

    
    #header_language > div {
        height: 2.1vw;
    }

    #wrapper {
        width: calc(v-bind('appArea') * 100vw);
        margin-left: calc(v-bind('1 - appArea') * 50vw - 20px - 5px - 4px);
        height: fit-content;
        padding: 20px 20px;
        border: 4px solid black;
        outline: 5px solid #8b6d9c;
        background-color: #f0f3eb;
    }

    #main {
        width: 100%;
        aspect-ratio: 4/3;
        position: relative;
    }

    #cardholder {
        width: calc((65.24%));  /* (8.66% + 0.33vw) * 7 */
        /*padding-bottom: 9.09%;*/
        height: calc(11.54667% + 0.33vw);     /* (9.09 + 0.33 / appWidth) * aspectRatio */
        border: 0.6vw solid black;  /* 0.6vw = 0.75% width = 1% height*/
        border-radius: 0;
        position: absolute;
        left: calc(17.38% - 0.3vw);
        bottom: 0;
        background-color: #494d7e;
        z-index: 0;
    }

    .switch {
        display: inline-block;
        position: relative;
    }


    .switch > span {
        position: absolute;
        pointer-events: none;
        user-select: none;
        font-weight: bold;
        
        text-shadow: 0 1px 0 rgba(0, 0, 0, .06);
        width: 50%;
        text-align: center;
    }

    .switch > span.english {
        left: 0;
        top: 0.5vw;
        color: v-bind(toggleColor2);
        font-size: 1.08vw;
        font-family: sans-serif;
    }

    .switch > span.chinese {
        right: 0;
        color: v-bind(toggleColor1);
        font-size: 0.98vw;
        top: 0.35vw;
        
    }

    .check-toggle {
        display: none;
    }

    .check-toggle + label {
        display: block;
        position: relative;
        cursor: pointer;
        outline: none;
        -webkit-user-select: none;
        -moz-user-select: none;
        -ms-user-select: none;
        user-select: none;
    }

    input.check-toggle-round-flat + label {
        width: 6vw;
        height: 100%;
        background-color: #b0b0b0;
        /*-webkit-border-radius: 60px;
        -moz-border-radius: 60px;
        -ms-border-radius: 60px;
        -o-border-radius: 60px;
        border-radius: 60px;*/
    }

    input.check-toggle-round-flat + label:before, input.check-toggle-round-flat + label:after {
        display: block;
        position: absolute;
        content: "";
    }

    input.check-toggle-round-flat + label:before {
        top: 0px;
        left: 0px;
        bottom: 0px;
        right: 0px;
        background-color: #b0b0b0;
        /*-moz-border-radius: 60px;
        -ms-border-radius: 60px;
        -o-border-radius: 60px;
        border-radius: 60px;*/
    }

    input.check-toggle-round-flat + label:after {
        top: 0px;
        left: 0px;
        bottom: 0px;
        width: 50%;
        background-color: #494d7e;
        /*-webkit-border-radius: 52px;
        -moz-border-radius: 52px;
        -ms-border-radius: 52px;
        -o-border-radius: 52px;
        border-radius: 52px;*/
        margin-left: 50%;
        -webkit-transition: margin 0.2s;
        -moz-transition: margin 0.2s;
        -o-transition: margin 0.2s;
        transition: margin 0.2s;
    }

    input.check-toggle-round-flat:checked + label:after {
        margin-left: 0;
    }
</style>

