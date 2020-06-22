<!-- An animated button for opening and closing a container 'fold'

The idea is for a down arrown (open fold) which animates to become an up 
arrow (close fold) at during the animation of the fold sliding out, and
vice versa.

-->
<script>
export let width = 14;
export let height = 12;

export let pointUp = true;

export let fill = '#65b81d';
export let stroke = '#ffd700';
export let strokeWidth = 1;

$: colourStyles = `fill: ${fill}; 
                    stroke: ${stroke}; 
                    stroke-width: ${strokeWidth}; 
                    width: ${width}; 
                    height: ${height};`;

$: triangleStyle = colourStyles + `
                      margin: 1 auto; padding:1;
                      width: ${width+strokeWidth}; 
                      height: ${height};`;

</script>

<style>
</style>

<!-- Note: including on:click without a handler forwards the event to the component so it can be handled by the component user -->
<div style="height: {height+height/2+4}px;" on:click on:click={() => pointUp = !pointUp}>
  <svg width={width} height={height+height/2}>
    <polygon
      points='{strokeWidth},{-height/2}, {width},{-height/2} {(width+strokeWidth)/2},{height/2}' 
      style="{triangleStyle} transform: translate(0px, {height}px); transition: transform 0.8s;{pointUp ? 'transform: translate(0px,' + height + 'px) rotateX(180deg)' : ''}" />
  </svg>
</div>
