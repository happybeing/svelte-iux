<script>
// A simple reveal content box, using Svelte transforms.
//
// Based on REPL:
//   https://svelte.dev/repl/d32ecc5d5a6847148fb85acd44b93640?version=3.23.0
//
// This is sveltified version of RevealBoxJS.svelte which does the same thing
// but using JavaScript.

import { tick, onMount } from 'svelte';
import { tweened } from 'svelte/motion';
import { cubicInOut } from 'svelte/easing';

export let disabled = false;
export let reveal = false;
export let minHeight = 0;
export let boxStyle = '';
export let transition = 'height 0.8s ease-out; '
let box, dimmerBox;

// export let timePerPixel = 0.02;

// Need a default initial style before revealOrHide() can use 'box'
let initialHeightStyle;
let boxTransition;

let lastReveal = reveal;  // Reveal state going into revealOrHide()

$: boxContentHeight = box ? box.scrollHeight : 0;
$: revealOrHide(reveal);
$: finalHeight = (reveal ? Math.max(boxContentHeight, minHeight) : minHeight);


onMount( async () => {
  lastReveal = reveal;  // Reveal state going into revealOrHide()
  box.style.height = 'auto';
  await tick();

  console.dir(box);
  console.log('box.offsetTop: ', box.offsetTop);
  console.log('box.offsetHeight: ', box.offsetHeight);
  console.log('box.scrollHeight: ', box.scrollHeight);
  console.log('box.clientHeight: ', box.clientHeight);

  let initialHeight = reveal ? Math.max(box.scrollHeight, minHeight) : minHeight;
  initialHeightStyle = `height: ${initialHeight}px; `;
  box.style.height = initialHeight + 'px';
  boxTransition = `${initialHeightStyle + 'transition: ' + transition}`;

  dimmerBox.style.height = initialHeight + 'px';

  // initialHeightStyle = `height: ${reveal ? Math.max(box.scrollHeight, minHeight) : minHeight}px; `;
  // boxTransition = `${initialHeightStyle + 'transition: ' + transition}`;
  // dimmerBox.style.height = `${finalHeight}px`;
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

function onTransitionEnd () {
  if (!sizeTransitionBegin) return;
  sizeTransitionBegin = false;

  // remove "height" from inline styles so it can return to its initial value
  if (reveal) {
    console.log('setting auto');
    box.style.height = 'auto';
  }

  // Keep height in sync with container
  initialHeightStyle = '';
  dimmerBox.style.height = `${finalHeight}px`;
}

</script>

<style>
.reveal-box {
  overflow: hidden;
  height: auto;
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
  pointer-events: all;
}

.dimmer {
  width: 100%; 
  height: 100%;
  position: absolute;
  z-index: 100;
  overflow: hidden;
  pointer-events: none;
}

</style>

<div class='reveal-box' bind:this={box} style={boxTransition + boxStyle} on:transitionend={onTransitionEnd} >
  <div class='dimmer' bind:this={dimmerBox} >
    <svg class='dimmer' viewBox='0 0 100 100'>
      <rect class={'dimmer ' + (disabled ? 'dimmer-active' : 'dimmer-inactive')} style={disabled ? '' : 'pointer-events: none;'}/>
    </svg>
  </div>

  <slot></slot>
</div>