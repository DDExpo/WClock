<script lang="ts">
  import type { Timer, PropsCard } from "$lib/types/StoreComponentsTypes";

  import AddForm from "./AddForm.svelte";
  import CircleButton from "../CircleButton.svelte";
  import CircleProgress from './CircleProgress.svelte';
  import TopRightButton from "../TopRightButton.svelte";
  import { appTheme } from "$lib/stores/sideBarAndTheme.svelte";
  import { deleteCard } from "$lib/stores/timerWatch.svelte";
  import { makeMiniWindow } from "$lib/stores/utils.svelte";
  
  let { card, ind }: PropsCard = $props()
  let showCardForm: boolean = $state(false)
  let cardStateCompact: boolean = $state(false)

  function makeWindowCompact() {
    cardStateCompact = !cardStateCompact
    makeMiniWindow(cardStateCompact)
    console.log(cardStateCompact)
  }

  function hideShowCardForm() {
    showCardForm = !showCardForm
    resetTimer()
  }

  function startStopWatch() {
    if (!card.running) {card.timer.start();
    } else {card.timer.stop();};
    card.running = !card.running;
  };

  function resetTimer() {
    card.running = false
    card.timer.reset()
  }

</script>

<div class={["card", { light: appTheme.light, "compact": cardStateCompact}]}>
  <div class="card-header">
    <div class="card-name">{card.name}</div>
    <div class="top-buttons">
      <TopRightButton onClick={ makeWindowCompact } icon="icons/buttons/arrow-up-right-from-square.svg" alt="Compact mode" compact={cardStateCompact} --end=16px/>
      {#if !cardStateCompact}
        <TopRightButton onClick={ hideShowCardForm } icon="icons/buttons/edit.svg" alt="edit" --end=16px/>
        <div draggable="true" role="form" ondragstart={(e) => { e.preventDefault(); e.stopPropagation();}}>
          {#if showCardForm }
          <AddForm closeForm={ hideShowCardForm } formName="Save" cardName={card.name} cardDial={card.initialTime} change={true} cardInd={ind}/>
          {/if}
        </div>  
        <TopRightButton onClick={() => { deleteCard(card.id) }} icon="icons/topbar/cross.svg" alt="delete" --end=16px/>
      {/if}
    </div>
  </div>
  <div class="circle-progress-bar">
    <CircleProgress progress={card.timeLeft} cardTime={card.time}/>
  </div>
  <div class="bottom-buttons">
    <CircleButton
      onClick={ startStopWatch }
      icon={card.running ? "icons/buttons/pause.svg" : "icons/buttons/play.svg"}
      alt={card.running ? "pause" : "start"}
      isRunning={card.running} />
    <CircleButton onClick={ resetTimer } icon="icons/buttons/reset.svg" alt="reset" />
  </div>
</div>

<style>

.card {
  display: flex;
  flex-direction: column;
  justify-content: space-between;;
  background: #373737;
  box-shadow: -4px 8px 8px rgba(23, 23, 23, 0.5);
  box-sizing: border-box;
  padding: 1rem;
  width: clamp(180px, 30vw, 260px);
  height: clamp(240px, 30vh, 60vh, 360px);
  font-family: dark-theme-font;
  gap: 1rem;
  border-radius: 3px;
  position: relative;
}

.card.compact {
  position: absolute;
  background: #373737;
  width: 200px;
  height: 180px;
  z-index: 10;
  top: 0px;
  left: 0px;
  border-radius: 0;
  padding: 10px;
}

.card.compact.light {
  background: #FFA44A;
  border-radius: 0;
}

.card.compact .card-header {
  margin-top: 25px;
}

.card.compact .card-name {
  margin-left: 5px;
  z-index: 11;
  font-size: 12px;
}

.card.compact .top-buttons {
  z-index: 10;
  margin-right: -5px;
}

.card.compact .circle-progress-bar {
  top: -30px;
  height: 135px;
}

.card.compact .bottom-buttons {
  position: absolute;
  z-index: 11;
  top: 130px;
  left: 6px;
  gap: 127px;
}

.card.light {
  background: #FFA44A;
  border-radius: 1rem;
  box-shadow: -4px 8px 8px rgba(97, 97, 97, 0.2);
  font-family: serif;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: -5px;
  margin-left: -5px;
}

.card-name {
  font-size: clamp(14px, 2vw, 22px);
  font-weight: bold;
  color: #ffffff;
  word-break: break-all;
}

.top-buttons {
  display: flex;
  margin-right: -14px;
}

.circle-progress-bar {
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
  height: clamp(140px, 25vw, 220px);
  flex-shrink: 0;
}

.bottom-buttons {
  display: flex;
  justify-content: center;
  gap: 20px;
  flex-wrap: wrap;
  flex-shrink: 1;
  max-height: 70px;
  margin-top: 0.5rem;
}

</style>