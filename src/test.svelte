<script>
  import { fade, fly } from 'svelte/transition';

  export let width = '500px';
  export let height = '300px';
</script>

<style>
.iux-test {
  font-family: sans-serif;
  padding: .5em 1em;
  text-align: center;
}

.parent {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
}

div.resizeable {
    resize:both;
    overflow:auto; /* something other than visible */
}

.child {
  width: 100px;  /* Or whatever */
  height: 100px; /* Or whatever */
  margin: auto;  /* Magic! */
}

</style>

<div class='iux-test parent resizeable' style='width: {width}; height: {height};'>
  <p class='child' in:fly="{{ y: 200, duration: 2000 }}" out:fade>
    <slot>I am IUXTest 1</slot>
  </p>
  <p class='child'>
    <slot>I am IUXTest 2</slot>
  </p>
  <p class='child'>
    <slot>I am IUXTest 3</slot>
  </p>
</div>
