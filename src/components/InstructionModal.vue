<!-- State/Logic (JS-Like) -->
<script setup>
    import { ref, computed } from 'vue';


    const props = defineProps({
        win: Boolean,
        language: String
    });
    const emit = defineEmits(['startGame', 'changeLanguage']);
    const startGame = () => {
        emit('startGame');
    }

    const toggleColor1 = computed(() => {
        return props.language === "English" ? "#494d7e" : "#fff";
    })
    const toggleColor2 = computed(() => {
        return props.language === "Chinese" ? "#494d7e" : "#fff";
    })

    const headerText = computed(() => {
        return props.language === "English" ? "How to Play" : "玩法";
    })

    const bodyText1 = computed(() => {
        return props.language === "English" ? "Choose 3 of a kind to wipe them out!" : "三张相同的牌即可消除。";
    });
    const bodyText2 = computed(() => {
        return props.language === "English" ? "Eliminate all cards to win!" : "如果你把牌都消除了，就赢了。";
    });
    const bodyText3 = computed(() => {
        return props.language === "English" ? "Your hand can only hold 7 cards!" : "如果槽位都被填满了，就失败了。";
    }); 

    const bodyFontSize = computed(() => {
        return props.language === "English" ? "1.3125vw" : "1.155vw";
    })

    const buttonText = computed(() => {
        return props.language === "English" ? "Start Game" : "开始";
    })
    
</script>

<!-- View Template (HTML-Like) -->
<template>
    <div class="modal-backdrop">
      <div class="modal">
        <header class="modal-header">
            <span>{{ headerText }}</span>

            <div class="switch">
                <input id="language-toggle" class="check-toggle check-toggle-round-flat" 
                        type="checkbox"
                        :checked="language === 'English'"
                        @input="emit('changeLanguage')"
                >
                <label for="language-toggle"></label>
                <span class="english">EN</span>
                <span class="chinese">中文</span>
            </div>
        </header>
  
        <section class="modal-body">
            1. {{ bodyText1 }} <br>
            2. {{ bodyText2 }} <br>
            3. {{ bodyText3 }}
         </section>
  
        <footer class="modal-footer">
          <slot name="footer">
            
          </slot>
          <button
            type="button"
            class="btn-start"
            @click="startGame"
          >
            {{ buttonText }}
          </button>
        </footer>
      </div>
    </div>
  </template>

  <!-- Styles (CSS-Like) -->
<style scoped>
   
  .modal-backdrop {
    position: fixed;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    background-color: rgba(0, 0, 0, 0.3);
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .modal {
    background-color: #cccccc;
    box-shadow: 2px 2px 20px 1px;
    overflow-x: auto;
    display: flex;
    flex-direction: column;
    border-radius: calc(13px + 0.625vw); /*15px*25px;*/
    padding: 0 calc(8px + 0.625vw);/*20*10px; */
    border: 3px solid black;
    box-shadow: 0 0 0 5px darkgray;
    width: calc(183px + 11.5625vw);
  }

  @media all and (min-width:641px) {
    .modal {
        border: calc(2px + 0.15625vw) solid black;
        box-shadow: 0 0 0 calc(4px + 0.15625vw) darkgray;
    }
  }
  /*@media all and (min-width:1024px) {
    .modal {
        width: 21vw;
    }
    
  }

  @media all and (min-width:600px) {
    .modal {
        width: 40vw;
    }
    
  } */



  .modal-header,
  .modal-footer {
    display: flex;
  }

  .modal-header {
    position: relative;
    border-bottom: 1px solid #8b6d9c;
    color: #272744;
    font-weight: 400;
    font-size: calc(26px + 0.625vw);

    padding: calc(9px + 0.3125vw) 0; /*15*10px 0;*/

    justify-content: space-between;

    height: calc(31px + 0.625vw);

  }

  .modal-header > span {
    font-family: 'Times Custom', Times, serif;
  }

  .modal-footer {
    border-top: 1px solid #8b6d9c;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    padding: 20px 0;
  }

  .modal-body {
    position: relative;
    padding-bottom: calc(10px + 1.5625vw);/*4015px;*/
    padding-top: calc(8px + 0.625vw); /*20*10px;*/
    /*font-size: v-bind('bodyFontSize');*/
    font-size: calc(11.2px + 0.71875vw);
    
  }

  .btn-start {
    color: #f0f3eb;
    background: #8b6d9c;
    border: 1px solid #8b6d9c;
    border-radius: calc(24px + 0.3125vw);/*30*25*/
    width: 50%;
    font-size: calc(16.4px + 0.1875vw); /*20*17px;*/
    padding: calc(7.2px + 0.25vw) /*12*8px*/ 0;
    
  }

  .btn-start:hover {
    cursor: pointer;
  }

  .switch {
        display: inline-block;
        position: relative;
        border: 2px solid #494d7e;
        background-color: #494d7e;
        width: 30%;
        aspect-ratio: 5 / 1;
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
        top: calc(5.5px + 0.18vw);
        color: v-bind(toggleColor2);
        font-size: calc(12.5px + 0.55vw);
        font-family: sans-serif;
    }

    .switch > span.chinese {
        right: 0;
        color: v-bind(toggleColor1);
        top: calc(4px - 0.05vw);
        font-size: calc(12.5px + 0.55vw);
        
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
        background-color: #494d7e;
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
        -webkit-transition: margin 0.2s;
        -moz-transition: margin 0.2s;
        -o-transition: margin 0.2s;
        transition: margin 0.2s;
        margin-left: 50%;
    }


    input.check-toggle-round-flat:checked + label:after {
        margin-left: 0;
    }
</style>