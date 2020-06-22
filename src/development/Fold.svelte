<script>
import FoldButton from './FoldButton.svelte';

export let foldIn = true;

export let protrudingHeight = 24;
export let buttonSize = protrudingHeight - 10;
export let foldStyle = '';

let height = 100;
let heightIn = protrudingHeight; 
let heightOut = height;
let transitTime = '' + (height * 0.005) +'s';

$: foldInStyle = `height: ${protrudingHeight}px; transition: height  ${transitTime} ease-in; `;
$: foldOutStyle = `height: ${heightOut}px; transition: height ${transitTime} ease-in; `;

$: foldSlideStyle = (foldIn ? foldInStyle : foldOutStyle) + foldStyle;
</script>

<style>
  .fold {
    position: relative;
    width: 100%;
    top: 0px;
    border: 1px solid black;
    background-color: rgba(232, 170, 127, 1);
    overflow: hidden;
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

<div class='fold' width='100%' style={foldSlideStyle}>
  <div class='fold-content'><slot></slot></div>
  <div class='top-right'><FoldButton height={buttonSize} width={buttonSize} bind:pointUp={foldIn} /></div>
</div>


