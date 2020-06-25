<script>
// A content holder with reveal/hide feature and support for disabling the content.
//
// Originally based on: https://svelte.dev/repl/d32ecc5d5a6847148fb85acd44b93640?version=3.23.0
//
// This is sveltified, enhanced version of deprecated IUXRevealBoxJS.svelte which
// used standard JavaScript (e.g. DOM attributes, events and animation frames) and
// lacks 'disabled' functionality (which could be added).
//
// Notes on deprecated IUXRevealBoxJS and related JS components:
// - deleted after commit: e954913
// - inspired by JS techniques from: https://css-tricks.com/using-css-transitions-auto-dimensions/

import { tick, onMount } from 'svelte';
import { tweened } from 'svelte/motion';
import { cubicInOut } from 'svelte/easing';

export let disabled = false;
export let reveal = false;
export let minHeight = 0;
export let boxStyle = '';
export let transition = 'height 0.8s ease-out; '
// export let scrollIntoViewReveal = '';
// export let scrollIntoViewHide = '';

let box;

// export let timePerPixel = 0.02;

// Need a default initial style before revealOrHide() can use 'box'
let initialHeightStyle;
let boxTransition;

let lastReveal = reveal;  // Reveal state going into revealOrHide()

$: boxContentHeight = box ? box.offsetHeight : 0;
$: revealOrHide(reveal);
$: finalHeight = (reveal ? Math.max(box ? box.scrollHeight : 0, minHeight) : minHeight);


onMount( async () => {
  lastReveal = reveal;  // Reveal state going into revealOrHide()
  box.style.height = 'auto';
  await tick();
  
  let initialHeight = reveal ? Math.max(box.offsetHeight, minHeight) : minHeight;
  initialHeightStyle = `height: ${initialHeight}px; `;
  box.style.height = initialHeight + 'px';
  await tick();
  dimmerBoxStyle = `width: ${box.scrollWidth}px; height: ${initialHeight}px; `;
});

// Note: 'await tick()' allows each change to take effect before making another

let sizeTransitionBegin = false;

async function revealOrHide(reveal) {
  console.log('revealOrHide: ', reveal);
  if (disabled || reveal === lastReveal || !box) return;

  dimmerBoxStyle = ''; //???
  lastReveal = reveal;
  sizeTransitionBegin = true;
  if (reveal) {
    box.style.transition = '';
    await tick();
    box.style.height = '' + minHeight + 'px';
    await tick();
    boxTransition = `height: ${finalHeight}px; transition: ${transition}`;
  } else {
    box.style.transition = '';
    await tick();
    box.style.height = `${Math.max(boxContentHeight, minHeight)}px`;
    await tick();
    boxTransition = `height: ${minHeight}px; transition: ${transition}`;
  }
}

let dimmerBoxStyle = '';

async function onTransitionEnd () {
  if (!sizeTransitionBegin) return;
  sizeTransitionBegin = false;

  // remove "height" from inline styles so it can return to its initial value
  if (reveal) {
    box.style.height = 'auto';
    box.style.width = 'auto';
    await tick();
    // if (scrollIntoViewReveal !== '' )
    //   box.scrollIntoView({ behavior: 'smooth', block: scrollIntoViewReveal })
  }
  else {
    // if (scrollIntoViewHide !== '' )
    //   box.scrollIntoView({ behavior: 'smooth', block: scrollIntoViewHide })
  }

  initialHeightStyle = '';
  resizeDimmer();
}

function resizeDimmer () {
  if (reveal) {
    dimmerBoxStyle = `height: ${Math.max(box.offsetHeight, minHeight)}px; width: ${box.offsetWidth}px; `;
  } else {
    dimmerBoxStyle = `height: ${minHeight}px; width: ${box.offsetWidth}px; `;
  }
}

</script>

<style>

fieldset {
  /* Fieldset defaults:
  display: block;
  margin-inline-start: 2px;
  margin-inline-end: 2px;
  padding-block-start: 0.35em;
  padding-inline-start: 0.75em;
  padding-inline-end: 0.75em;
  padding-block-end: 0.625em;
  min-inline-size: min-content;
  border-width: 2px;
  border-style: groove;
  border-color: threedface;
  border-image: initial; 
  */

  margin-inline-start: 0px;
  margin-inline-end: 0px;
  padding-block-start: 0em;
  padding-inline-start: 0em;
  padding-inline-end: 0em;
  padding-block-end: 0em;

  min-inline-size: min-content;

  border-width: 0px;
}
.reveal-box {
  overflow: hidden;
  height: auto;
}

svg.dimmer {
  position: absolute;
  z-index: 100;
}

rect.dimmer {
  fill: gray;
}

rect.dimmer-inactive {
  opacity: 0;
  transition: opacity 1.5s;
  pointer-events: none;
}

rect.dimmer-active {
  opacity: 0.2;
  transition: opacity 1.5s;
  pointer-events: all;
}

</style>

<svelte:window on:resize={resizeDimmer}/>
<div class='reveal-box' 
  bind:this={box}
  style={boxTransition + boxStyle} 
  on:transitionend={onTransitionEnd} >

  <!-- dimmer = overlay activated to disable content -->  
  <svg class='dimmer'
    style={dimmerBoxStyle + (disabled ? '' : 'pointer-events: none;')}
    viewBox='0 0 100 100'
    preserveAspectRatio="none">
    
    <rect width=100 height=100 class={'dimmer ' + (disabled ? 'dimmer-active' : 'dimmer-inactive')} />
  </svg>

  <!-- component content -->
  <fieldset disabled={disabled || !reveal ? 'disabled' : false}>
    <slot></slot>
  </fieldset>
</div>