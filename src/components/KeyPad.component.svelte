<script>
  import { createEventDispatcher } from "svelte";
  import { currentTimer } from "../store";

  const dispatch = createEventDispatcher();

  let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 0];
  let input = "000000";

  $: seconds = input.slice(4, 6).padStart(2, "0");
  $: minutes = input.slice(2, 4).padStart(2, "0");
  $: hours = input.slice(0, 2).padStart(2, "0");

  function handleNumberClick(number) {
    if (input.charAt(0) === "0") {
      input += number.toString();
      input = input.substr(-6);
    }
  }

  function handleCancel() {
    dispatch("cancel");
  }

  function handleConfirm() {
    dispatch("confirm", {
      time: parseInt(seconds) + parseInt(minutes) * 60 + parseInt(hours) * 3600
    });
  }
</script>

<style lang="scss">
  @import "../_utils/styles/main";

  .KeyPad {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-template-rows: 100px 1fr 80px;
    height: 100%;

    &-timePreview {
      @include flex-center;
      font-size: 40px;
      border-bottom: 1px solid $color-mystic;
      color: $color-mystic;
      margin: 0 16px;
      position: relative;
      grid-column: 1 / 4;

      &Label {
        font-size: 20px;
      }

      & > div {
        display: flex;
        align-items: baseline;
      }

      button {
        font-size: 10px;
        margin-left: 12px;
        color: $color-sail-blue;
        border: 1px solid;
        background: transparent;
        position: absolute;
        right: 0;
        color: $color-mystic;
      }
    }

    &-number {
      @include flex-center;
      font-size: 24px;

      &Container {
        display: grid;
        grid-template-columns: repeat(3, 1fr);
        grid-column: 1 / 4;
        padding: 0 40px;
      }

      &.isLast {
        grid-column: 1 / 4;
      }
    }

    &-cancel,
    &-confirm {
      @include flex-center;
    }
  }
</style>

<div class="KeyPad">
  <div class="KeyPad-timePreview">
    <div>
      <span>{hours}</span>
      <span class="KeyPad-timePreviewLabel">h</span>
    </div>
    &nbsp;
    <div>
      <span>{minutes}</span>
      <span class="KeyPad-timePreviewLabel">m</span>
    </div>
    &nbsp;
    <div>
      <span>{seconds}</span>
      <span class="KeyPad-timePreviewLabel">s</span>
    </div>
    <button on:click={() => (input = '000000')}>Limpar</button>
  </div>
  <div class="KeyPad-numberContainer">
    {#each numbers as number}
      <button
        class="KeyPad-number"
        class:isLast={number === 0}
        on:click={handleNumberClick.bind(null, number)}>
        {number}
      </button>
    {/each}
  </div>
  <button
    class="KeyPad-cancel"
    on:click={handleCancel}
    disabled={$currentTimer.secondsLeft === undefined}>
    Cancel
  </button>
  <button
    class="KeyPad-confirm"
    on:click={handleConfirm}
    disabled={input === '000000'}>
    Confirmar
  </button>
</div>
