<script>
    export let currentStep;
    import Scrolly from "../helpers/Scrolly.svelte";
  
    $: steps = [
    `The city of Fushun had a population of 1.75 million last year. Each dot represents 1,000 people in this graphic.`,
    `There were 5,541 <span style="background-color:#A4D393;padding:3.5px;margin:1px;border-radius:4px;font-weight:500;color:black">newborns</span>.`,
    `Wayne County in Michigan, which the city of Detroit locates, had roughly the same amount of population.`,
    `Its <span style="background-color:#A4D393;padding:3.5px;margin:1px;border-radius:4px;font-weight:500;color:black">newborns</span> last year, however, were 20,065.
    <br><br><span style="color: #727272;font-weight: 300; font-family: Retina, sans-serif;font-size: 13px;line-height: 17px; margin-top:6px;">Sources: Fushun Municipal Government; CDC (Wayne County)</span>`
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