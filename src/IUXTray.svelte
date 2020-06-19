<script>
import IUXTrayButton from './IUXTrayButton.svelte';
import IUXRevealArea from './IUXRevealArea.svelte';

// TODO: extend headingElement using <svelte:element> if implemented (see https://github.com/sveltejs/svelte/issues/2324)
// TODO: ??? export let height;

const minProtrusion = 24;
const buttonSpacing = 10;

export let reveal = false;
export let disabled = false;

export let heading = '';
export let headingReveal = '';
export let headingElement = 'b';  // Supports b, h1, h2, h3
export let headingStyle = 'text-align: center; ';

export let buttonLabel = '';
export let buttonLabelReveal = '';

export let protrudingHeight = minProtrusion;
export let buttonSize = minProtrusion - buttonSpacing;
export let trayStyle = '';

$: headingReveal = headingReveal === '' ? heading : headingReveal;
$: buttonLabelReveal = buttonLabelReveal === '' ? buttonLabel : buttonLabelReveal;
$: useButtonSize = Math.max(buttonSize, minProtrusion - buttonSpacing);
$: minHeight = Math.max(buttonSize + buttonSpacing, minProtrusion);

let disableDuration = 2;  // Duration of disabled/enabled transitions
</script>

<style>
.tray {
  position: relative;
  width: 100%;
  top: 0px;
  overflow: hidden;
}

.tray-content {
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

.dimmer {
  width: 100%; 
  height: 100%; 
  position: absolute;
}

svg.dimmer {
  height: auto;
}

rect.dimmer {
  z-index: 100;
  fill: gray;
}

rect.dimmer-inactive {
  opacity: 0;
  transition: opacity 1.5s;
}

rect.dimmer-active {
  opacity: 0.2;
  transition: opacity 1.5s;
}

</style>

<IUXRevealArea minHeight={protrudingHeight} reveal={reveal}>
  <div {disabled} class='tray' style={trayStyle}>

    <div class='dimmer' style={disabled ? '' : 'pointer-events: none;'}>
      <svg class='dimmer' viewBox='0 0 100 100'>
        <rect class={'dimmer ' + (disabled ? 'dimmer-active' : 'dimmer-inactive')}/>
      </svg>
    </div>

    <div style={'display: flex; flex-direction: row-reverse; min-height: ' + protrudingHeight + 'px; width: 100%;'}>
      <div style={'cursor: pointer; display: block;height: ' + protrudingHeight + 'px; '}>
        <div style={'width: 100%; height: ' + (protrudingHeight/2-16) + 'px; '}></div>
        <div style={'float: right; width: min-content; height: ' + protrudingHeight/2 + 'px; '}>
          <IUXTrayButton height={useButtonSize} width={useButtonSize} bind:pointUp={reveal} />
        </div>
        <div on:click={() => {reveal = !reveal}} style={'float: right; width: min-content; height: ' + protrudingHeight/2 + 'px; padding-right: 0.3em;'}>
          {!reveal ? buttonLabel : buttonLabelReveal}
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
