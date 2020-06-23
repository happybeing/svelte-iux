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

let box, dimmerBox, boxW, boxH;

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
  dimmerBoxStyle = `width: ${box.scrollWidth}px; height: ${box.scrollHeight}px; `;

  boxTransition = `${initialHeightStyle + 'transition: ' + transition}`;

});

// Note: 'await tick()' allows each change to take effect before making another

let sizeTransitionBegin = false;
async function revealOrHide(reveal) {
  console.log('revealOrHide: ', reveal);
  if (disabled || reveal === lastReveal || !box || !dimmerBox) return;

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

$: resizeDimmer(boxW, boxH);

function resizeDimmer (w, h) {
  if (!dimmerBox || !box) return;

  dimmerBoxStyle = `width: ${box.scrollWidth}px; height: ${box.offsetHeight}px; `;
}

let dimmerBoxStyle = '';

function onTransitionEnd () {
  if (!sizeTransitionBegin) return;
  sizeTransitionBegin = false;

  // remove "height" from inline styles so it can return to its initial value
  if (reveal) {
    box.style.height = 'auto';
    box.style.width = 'auto';
    box.scrollIntoView({ behavior: 'smooth', block: 'center' })
  }

  initialHeightStyle = '';

  // Keep height in sync with container
  dimmerBoxStyle = `width: ${box.scrollWidth}px; height: ${box.offsetHeight}px; `;
}

</script>

<style>
.reveal-box {
  overflow: hidden;
  height: auto;
}

svg.dimmer {
  position: absolute;
  z-index: 100;
}

rect.dimmer {
  width: 100%;
  height: 100%;
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

<div class='reveal-box' 
  bind:this={box} 
  bind:clientWidth={boxW}
  bind:clientHeight={boxH}
  style={boxTransition + boxStyle} 
  on:transitionend={onTransitionEnd} >

  <!-- dimmer = overlay activated to disable content -->  
  <svg class='dimmer' bind:this={dimmerBox}
    style={dimmerBoxStyle + (disabled ? '' : 'pointer-events: none;')}
    viewBox='0 0 100 100'
    preserveAspectRatio="none">
    
    <rect class={'dimmer ' + (disabled ? 'dimmer-active' : 'dimmer-inactive')} />
  </svg>

  <!-- component content -->
  <slot></slot>
</div>