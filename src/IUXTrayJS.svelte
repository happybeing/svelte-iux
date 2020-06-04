<script>
import IUXTrayButton from './IUXTrayButton.svelte';
import IUXRevealAreaJS from './IUXRevealAreaJS.svelte';

export let trayIn = true;

$: reveal = !trayIn;  // Controls the IUXRevealAreaJS

// export let height;// TODO
const minProtusion = 24;
const buttonSpacing = 10;

export let protrudingHeight = minProtusion;
export let buttonSize = minProtusion - buttonSpacing;
export let trayStyle = '';

$: useButtonSize = Math.max(buttonSize, minProtusion - buttonSpacing);
$: minHeight = Math.max(buttonSize + buttonSpacing, minProtusion);

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

<IUXRevealAreaJS minHeight={minHeight} reveal={reveal}>
  <div class='tray' style={trayStyle}>
    <div class='top-right'><IUXTrayButton height={useButtonSize} width={useButtonSize} bind:pointDown={trayIn} /></div>
    <slot></slot>
  </div>
</IUXRevealAreaJS>
