<script>
// A simple reveal content box, using JavaScript to do the transform.
//
// It uses JavaScript because you can't use a CSS transition based on non absolute
// property values (e.g. 'height: auto').
//
// The technique used is from https://css-tricks.com/using-css-transitions-auto-dimensions/#comments
// with minor changes.
//
// Other techniques are to transition from fixed values (e.g. setting max-height), or calculating
// the element height from the containered elements. Another may be possible with flexbox/flex-grow
// but I've not looked into that yet, and there were more ideas in the article comments.

import {onMount} from 'svelte';

export let reveal = false;
export let minHeight = 0;
export let boxStyle = '';
export let transition = 'height 0.8s ease-out; ';

$: minHeightStyle = `min-height: ${minHeight}px; `;

let revealBox;

function doHide(element) {
  console.log('doHide');
  // get the height of the element's inner content, regardless of its actual size
  const contentHeight = element.scrollHeight;
  
  const elementTransition = element.style.transition;  // disable all css transitions
  element.style.transition = '';
  
  // When the above style change has taken set the height to transition from
  requestAnimationFrame(function() {
    element.style.height = contentHeight + 'px';
    element.style.transition = elementTransition;
    
    // When the above has taken effect, transition to height: 0
    requestAnimationFrame(function() {
      element.style.height = 0 + 'px';
    });
  });
  
  element.setAttribute('reveal-box-hidden', 'true');
}

function doReveal(element) {
  console.log('doReveal');
  var contentHeight = element.scrollHeight;
  
  element.style.height = contentHeight + 'px';

  function onTransitioned(e) {
      // remove this event listener so it only gets triggered once
      element.removeEventListener('transitionend', onTransitioned);
      // remove "height" from the element's inline styles, so it can return to its initial value
      element.style.height = null;
    }

    // when the next css transition finishes (which should be the one we just triggered)
    element.addEventListener('transitionend', onTransitioned);

  element.setAttribute('reveal-box-hidden', 'false');
}

onMount(() =>{
  if (reveal) {
    revealBox.setAttribute('reveal-box-hidden', 'false' );
    revealBox.style.height = null;
  } else {
    revealBox.setAttribute('reveal-box-hidden', 'true' );
    revealBox.style.height = 0 + 'px';
  }
})

$: revealOrHide(reveal);

function revealOrHide(reveal) {
  if (revealBox) {
    var isHidden = revealBox.getAttribute('reveal-box-hidden') === 'true';
    
    if(isHidden && reveal) {
      doReveal(revealBox)
      revealBox.setAttribute('reveal-box-hidden', 'false')
    } else if (!isHidden && !reveal) {
      doHide(revealBox);
    }
  }
}
</script>

<style>
.reveal-box-js {
  overflow: hidden;
  /* transition: height 0.8s ease-out; */
  height: auto;
}
</style>

<div class='reveal-box-js' bind:this={revealBox} style={minHeightStyle+boxStyle+'transition: '+transition} >
  <slot></slot>
</div>