<script>
import IUXFoldButton from './IUXFoldButton.svelte';
import IUXRevealArea from './IUXRevealArea.svelte';

// TODO: extend headingElement using <svelte:element> if implemented (see https://github.com/sveltejs/svelte/issues/2324)
// TODO: ??? export let height;

const minProtrusion = 50;
const buttonSpacing = 10;

export let reveal = false;
export let disabled = false;

export let heading = '';
export let headingReveal = '';
export let headingElement = 'b';  // Supports b, h1, h2, h3
export let headingStyle = 'text-align: center; ';

export let buttonLabel = '';
export let buttonLabelReveal = '';
export let buttonLabelDisabled = "not available";

export let protrudingHeight = minProtrusion;
export let buttonSize = minProtrusion - buttonSpacing;
export let foldStyle = '';

$: headingReveal = headingReveal === '' ? heading : headingReveal;
$: buttonLabelReveal = buttonLabelReveal === '' ? buttonLabel : buttonLabelReveal;
$: minHeight = Math.max(buttonSize + buttonSpacing, minProtrusion);

let disableDuration = 2;  // Duration of disabled/enabled transitions


let buttonDiv;

// Keyboard input when in focus
function handleKeydown(e) {
  if(e.keyCode === 32 && buttonDiv === window.document.activeElement) {
    reveal = !reveal;
    e.preventDefault();
  } 
}

</script>

<style>
.fold {
  position: relative;
  width: 100%;
  top: 0px;
  overflow: hidden;
}

div.fold-button {
  cursor: pointer;
  margin: 3px;
}

.fold-content {
  position: absolute;
  top: 0px;
  overflow: hidden;
  height: auto;
  width: inherit;
}

.top-right {
  position: absolute;
  top: 0px;
  right: 0px;
  margin: 0px;
  padding: 0px;
}

</style>

<svelte:window on:keydown={handleKeydown} />
    
<IUXRevealArea {disabled} 
  minHeight={protrudingHeight} 
  reveal={reveal}
  scrollIntoViewReveal='start'
  scrollIntoViewHide='start'
  >
  <div class='fold' style={foldStyle}>

    <div style={'display: flex; flex-direction: row-reverse; min-height: ' + protrudingHeight + 'px; width: 100%;'}>
      <div class='fold-button' bind:this={buttonDiv} tabindex={disabled ? -1 : 0} role='button' style={'display: block;height: ' + protrudingHeight + 'px; '}>
        <div style={'width: 100%; height: ' + (protrudingHeight/2-16) + 'px; '}></div>
        <div style={'float: right; width: min-content; height: ' + protrudingHeight/2 + 'px; '}>
            <IUXFoldButton 
              bind:pointUp={reveal} />
        </div>
        <div on:click={() => {reveal = !reveal}} style={'float: right; width: min-content; height: ' + protrudingHeight/2 + 'px; padding-right: 0.3em;'}>
          {disabled ? buttonLabelDisabled : (!reveal ? buttonLabel : buttonLabelReveal)}
        </div>
      </div>
      
      <div style={'height: ' + protrudingHeight + 'px; flex-grow: 1; ' + headingStyle}>
        {#if headingElement === 'b'}
          <b>{!reveal ? heading : headingReveal}</b>
        {:else if headingElement === 'h1'}
          <h1>{!reveal ? heading : headingReveal}</h1>
        {:else if headingElement === 'h2'}
          <h2>{!reveal ? heading : headingReveal}</h2>
        {:else if headingElement === 'h3'}
          <h3>{!reveal ? heading : headingReveal}</h3>
        {:else}
          {!reveal ? heading : headingReveal}
        {/if}
      </div>
    </div>

    <slot></slot>

  </div>
</IUXRevealArea>
