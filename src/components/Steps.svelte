<script>
    export let currentStep;
    import Scrolly from "../helpers/Scrolly.svelte";
  
    $: steps = [
    `<p>In China, the 20-64 age cohort grew several percentage points faster than other age groups during the years after the country implemented the one-child policy, giving the economy a shot in the arm. The window closed a decade ago.</p>`,
    `A comparison with a broad group of 'less developed' countries shows a more extended but less dramatic window, lasting well into the middle of this century.`,
    `While other countries still have a favorable age distribution, China's working-age population is shrinking, with more elderly to support.`,
    `Population fluctuations, such as rolling effects of a 1960s famine, can create brief windows that have little effect on the long-term trend.
    <br><br><span style="color: #727272;font-weight: 300; font-family: Retina, sans-serif;font-size: 13px;line-height: 17px; margin-top:6px;">Note: Levels for Jan. 1 of each year <br> Source: U.N. Population Division and research by Danan Gu, a U.N. demographer</span>`
  ];
  
  </script>
  <div class="steps">
    <Scrolly bind:value={currentStep}>
      {#each steps as text, i}
      <div class="step" class:active={currentStep === i}>
        <div class="step-content">
          {@html text}
        </div>
      </div>
      {/each}
    </Scrolly>
  </div>
  
  <style>
    .step {
    height: 90vh;
    opacity: 0.3;
    transition: opacity 300ms ease;
  
  /* vertically and horizontally center the steps */
    display: flex;
    justify-content: center;
    place-items: center;
  
  }
  
  .step-content {
  position: relative;
  box-sizing: border-box;
  z-index: 1;
  -webkit-transform: translate3d(0, 0, 0); /*for safari z-index issue*/
  height: auto;
  width: 100%;
  max-width: 500px; 
  margin: 0 auto;
  font-family: RetinaLight, sans-serif;
  font-size: 17px;
  line-height: 27px;
  font-weight: 400;
  color: #333;
  padding: 20px 25px 20px;
  box-shadow: 0 0 1px 0 rgba(0, 0, 0, .4);
  overflow: visible;
  background-color: rgba(255, 255, 255, .9);
  }
  
  .step.active {
  opacity: 1;
  }
  
  .steps {
  position: relative;
  z-index: 2;
  pointer-events: none;
  /* display: flex;
  justify-content: center;
  place-items: center; */
  }
  
  </style>