<script>
import IUXTrayButton from './IUXTrayButton.svelte';

export let trayIn = true;

export let protrudingHeight = 24;
export let buttonSize = protrudingHeight - 10;
export let trayStyle = '';

let height = 100;
let heightIn = protrudingHeight; 
let heightOut = height;
let transitTime = '' + (height * 0.005) +'s';

$: trayInStyle = `height: ${protrudingHeight}px; transition: height  ${transitTime} ease-in; `;
$: trayOutStyle = `height: ${heightOut}px; transition: height ${transitTime} ease-in; `;

$: traySlideStyle = (trayIn ? trayInStyle : trayOutStyle) + trayStyle;
</script>

<style>
  .tray {
    position: relative;
    width: 100%;
    top: 0px;
    border: 1px solid black;
    background-color: rgba(232, 170, 127, 1);
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
</style>

<div class='tray' width='100%' style={traySlideStyle}>
  <div class='tray-content'><slot></slot></div>
  <div class='top-right'><IUXTrayButton height={buttonSize} width={buttonSize} bind:pointDown={trayIn} /></div>
</div>


