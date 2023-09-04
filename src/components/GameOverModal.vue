<!-- State/Logic (JS-Like) -->
<script setup>
    import { ref, computed } from 'vue';

    const props = defineProps({
        win: Boolean,
        language: String
    });

    const restartGame = () => {
        emit('restartGame');
    }
    const emit = defineEmits(['restartGame']);

    const bodyText = computed(() => {
        if (props.win) {
            if (props.language === "English")
                return "You Win!"
            else
                return "赢了!"
        }
        else {
            if (props.language === "English")
                return "Game Over!"
            else
                return "失败!"
        }
            
    });

    const footerText = computed(() => {
        if (props.win) {
            if (props.language === "English")
                return "Play Again"
            else
                return "再玩一次"
        }
        else {
            if (props.language === "English")
                return "Try Again"
            else
                return "再试一次"
        }
            
    })


</script>

<!-- View Template (HTML-Like) -->
<template>
    <div class="modal-backdrop">
      <div class="modal">
  
        <section class="modal-body">
            {{ bodyText }}
         </section>
  
        <footer class="modal-footer">
          <slot name="footer">
            
          </slot>
          <button
            type="button"
            class="btn-start"
            @click="restartGame"
          >
            {{  footerText }}
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
    background-color: lightgray;
    box-shadow: 2px 2px 20px 1px;
    overflow-x: auto;
    display: flex;
    flex-direction: column;
    border-radius: 25px;
    padding: 0 20px;
    border: 5px solid black;
    outline: 7px solid darkgray;
  }

  .modal-footer {
    display: flex;
  }

  .modal-footer {
    border-top: 0px solid #bbbbbb;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    padding: 20px 0;
  }

  .modal-body {
    position: relative;
    padding-bottom: 10px;
    padding-top: 20px;
    font-size: 46px;
    align-items: center;
    text-align: center;
  }

  .btn-start {
    color: white;
    background: #4AAE9B;
    border: 1px solid #4AAE9B;
    border-radius: 30px;
    font-size: 24px;
    padding: 10px 20px;
    width: fit-content;
    
  }

  .btn-start:hover {
    cursor: pointer;
  }
</style>