<script setup>
    import PlayArea from './components/PlayArea.vue';
    import GameOverModal from './components/GameOverModal.vue';
    import InstructionModal from './components/InstructionModal.vue/';

    import { ref, computed } from 'vue';

    //const appArea = 0.5;

    const language = ref("English");
    
    const gameName = computed(() => {
        return language.value === "English" ? "Match Three" : "消消爆";
    })

    const nameSpacing = computed(() => {
        return language.value === "English" ? "0" : "2px";
    })

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
        if (language.value === "English")
            language.value = "Chinese"
        else
            language.value = "English"
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
                {{ gameName }}
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
                    @gameOver="initiateGameOver" 
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
        height: fit-content;
        min-height: calc(100vh - 50px);
        padding-top: 50px;
        padding-bottom: 30px;
    }
    
    #header_area {
        display: flex;
        flex-direction: row;
        justify-content: space-between;
        margin-bottom: 5px;
    }

    #header_title {
        
        background-color: #494d7e;/*#8b6d9c; /*#4AAE9B;/*#add8e6;*/
        height: 100%;
        text-align: center;
        display: flex;
        justify-content: center;
        align-items: center;
        font-family: sans-serif;
        border: 5px solid black;
        border-bottom: 0;
        box-sizing: border-box;
        color: #fac6ce;
        user-select: none;
    }

    #header_language {
        height: 100%;
        display: flex;
        justify-content: center;
        align-items: end;
        font-family: sans-serif;
    }


    #wrapper {
        /*width: calc(v-bind('appArea') * 100vw);
        margin-left: calc(v-bind('1 - appArea') * 50vw - 20px - 5px - 4px);*/
        height: fit-content;
        padding: 20px 20px;
        border: 4px solid black;
        outline: 5px solid #8b6d9c;
        background-color: #f0f3eb;
    }  

    /* Phones and small tablets */
    @media all and (max-width:640px) {
        #wrapper {
            outline: none;
        }

        #header_area {
            margin-bottom: 0;
            width: 100%;
            height: 60px;
        }

        #header_title {
            border-radius: 0;
            border-right: none;
            width: 60%;
            box-sizing: border-box;
            font-size: calc(20px + 1.25vw);
            font-weight: 600;
            letter-spacing: v-bind('nameSpacing');
        }
        .switch {
            width: 40vw;
            height: 100%;
            box-sizing: border-box;
            border: 5px solid black;
            border-bottom: none;
        }
        .english {
            top: 13px;
            font-size: 28px;
        }

        .chinese {
            top: 10px;
            font-size: 25px;
        }
    }

    @media all and (max-width:450px) {
        .english {
            top: calc(16.4615385px - 0.769230769vw); /*14px;*/
            font-size: calc(10.6923px + 3.846153846vw); /*23px;*/
        }

        .chinese {
            top: calc(17px - 1.538461538vw);
            font-size: calc(11.15385px + 3.076923076vw);
        }
    }

    /* Tablet and smaller laptops */
    @media all and (min-width:641px) {
        #wrapper {
            width: calc(384px + 30vw);
            margin-left: calc((35vw - 192px) - 20px - 4px);
        }
        #header_area {
            width: calc(65vw + 192px - 37.5vw + 9px + 15px + 5px);
            margin-left: calc(37.5vw);

            height: 60px;
        }

        #header_title {
            width: 25vw;
            font-size: calc(13px + 1.40625vw);
            border-radius: 12px 12px 0px 0px;
        }

        .switch {
            width: 180px;
            aspect-ratio: 4 / 1;
        }
        
        .english {
            top: 9px;
            font-size: 24px;
        }

        .chinese {
            font-size: 21px;
            top:8px;
        }
    }

    /* Large PC */
    @media all and (min-width:1920px) {
        #wrapper {
            width: 50vw;
            margin-left: calc(25vw - 20px - 5px - 4px);
        }

        #header_area {
            width: calc(37.5vw + 9px + 15px);
            margin-left: calc(37.5vw);
        }

        #header_title {
            font-size: 40px;
        }

        /*.switch {
            width: 12vw;
            
        }

        .english {
            top: 0.5vw;
            font-size: 1.08vw;
        }

        .chinese {
            font-size: 0.98vw;
            top: 0.35vw;
        }*/
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
        color: v-bind(toggleColor2);
        font-family: sans-serif;
    }

    .switch > span.chinese {
        right: 0;
        color: v-bind(toggleColor1);
        
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
        width: 100%;
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

