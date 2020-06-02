<script>
// A simple reveal content box, using Svelte transforms.
//
// Based on REPL:
//   https://svelte.dev/repl/d32ecc5d5a6847148fb85acd44b93640?version=3.23.0
//
// This is sveltified version of RevealBoxJS.svelte which does the same thing
// but using JavaScript.

import { tick } from 'svelte';
import { tweened } from 'svelte/motion';
import { cubicInOut } from 'svelte/easing';

export let reveal = false;
let box;

export let timePerPixel = 0.02;

let boxStyle = '';
let styleHide = `height: 0px; transition: height 0.8s ease-in;`;

$: boxContentHeight = box ? box.scrollHeight : 0;
$: styleReveal = `height: ${boxContentHeight}px; transition: height 0.8s ease-out;`;
let styleRevealDone = `height: auto;`;

$: revealOrHide(reveal);

// Note: 'await tick()' allows each change to take effect before making another
async function revealOrHide(reveal) {
  console.log('revealOrHide: ', reveal);
  if (!box) return;

  if (reveal) {
    box.style.height = '0px';
    boxStyle = styleReveal;
    await tick();
  } else {
    const boxTransition = box.style.transition;
    box.style.transition = '';
    await tick();
    box.style.height = '' + box.scrollHeight + 'px';
    box.style.transition = boxTransition;
    await tick();
    boxStyle = styleHide;
  }
}

function onTransitionEnd () {
    // remove "height" from inline styles so it can return to its initial value
    console.log('ontransitionend()')
    if (reveal) {
      console.log('setting auto');
      box.style.height = 'auto';
    }
}

</script>

<style>
.reveal-box {
  overflow: hidden;
  height: auto;
}

</style>

<div class='reveal-box' bind:this={box} style={boxStyle} on:transitionend={onTransitionEnd} >
  <slot></slot>
</div>