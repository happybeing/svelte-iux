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

export let reveal = false;
export let boxStyle = '';

// Need a default initial style until revealOrHide() can use 'box'
let initialStyle = 'height: ' + (reveal ? 'auto;' : '0px'); 

let revealBox;

function doHide(element) {
  // get the height of the element's inner content, regardless of its actual size
  var contentHeight = element.scrollHeight;
  
  var elementTransition = element.style.transition;  // disable all css transitions
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
  var contentHeight = element.scrollHeight;
  
  element.style.height = contentHeight + 'px';

  element.addEventListener('transitionend', function(e) {
    // remove this event listener so it only gets triggered once
    element.removeEventListener('transitionend', arguments.callee);    
    // remove "height" from the element's inline styles, so it can return to its initial value
    element.style.height = null;
  });
  
  element.setAttribute('reveal-box-hidden', 'false');
}

$: revealOrHide(reveal);

function revealOrHide(reveal) {
  var box = document.querySelector('.reveal-box');
  if (box) {
    initialStyle = '';
    var isHidden = box.getAttribute('reveal-box-hidden') === 'true';
    
    if(isHidden && reveal) {
      doReveal(box)
      revealBox.setAttribute('reveal-box-hidden', 'false')
    } else if (!isHidden && !reveal) {
      doHide(box);
    }
  }
}
</script>

<style>
.reveal-box {
  overflow:hidden;
  transition: height 0.8s ease-out;
  height:auto;
}
</style>

<div class='reveal-box' bind:this={revealBox} style={boxStyle+initialStyle} >
  <slot></slot>
</div>