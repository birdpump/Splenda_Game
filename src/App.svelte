<script>
  // Inspired by https://svelte.dev/repl/810b0f1e16ac4bbd8af8ba25d5e0deff?version=3.4.2.
  import {flip} from 'svelte/animate';

  let baskets = [
    {
      "name": "stack 1",
      "items": ["1"]
    },
    {
      "name": "stack 2",
      "items": ["2", "3"]
    },
    {
      "name": "stack 3",
      "items": ["4", "5", "6"]
    },
    {
      "name": "stack 4",
      "items": ["7", "8", "9", "10"]
    },
    {
      "name": "stack 4",
      "items": []
    }
  ];

  let hoveringOverBasket;

  function dragStart(event, basketIndex, itemIndex) {
    // The data we want to make available when the element is dropped
    // is the index of the item being dragged and
    // the index of the basket from which it is leaving.
    const data = {basketIndex, itemIndex};
    event.dataTransfer.setData('text/plain', JSON.stringify(data));
  }

  function drop(event, basketIndex) {
    event.preventDefault();
    const json = event.dataTransfer.getData("text/plain");
    const data = JSON.parse(json);

    // Remove the item from one basket.
    // Splice returns an array of the deleted elements, just one in this case.
    const [item] = baskets[data.basketIndex].items.splice(data.itemIndex, 1);

    // Add the item to the drop target basket.
    if(basketIndex != 4){
      baskets[basketIndex].items.push(item);
    }
    baskets = baskets;

    hoveringOverBasket = null;
  }
</script>

<style>
  .test{
    display: flex;
    width: 100vw;
    height: 100vh;
    gap: 20px;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }
  .hovering {
    border-color: orange;
  }
  .item {
    display: inline; /* required for flip to work */
  }
  img{
    width: 80px;
  }
  .main {
    /*border: solid lightgray 2px;*/
    height: 160px; /* needed when empty */
    display: flex;
    align-items: center;
    justify-content: center;
    /*padding: 10px;*/
  }

  .trash{
    width: 200px;
    height: 200px;
    background-color: red;
  }

  .cont{
    display: flex;
    gap: 60px;
    align-items: center;
    justify-content: center;
  }

</style>

<div class="test">
  <div class="cont">


  {#each baskets as basket, basketIndex (basket)}
    <div animate:flip>
      <div class="main"
              class:hovering={hoveringOverBasket === basket.name}
              on:dragenter={() => hoveringOverBasket = basket.name}
              on:dragleave={() => hoveringOverBasket = null}
              on:drop={event => drop(event, basketIndex)}
              ondragover="return true"
      >
        {#each basket.items as item, itemIndex (item)}
          <div class="item" animate:flip>
            <img
                    src="./assets/splenda.png"
                    draggable={true}
                    on:dragstart={event => dragStart(event, basketIndex, itemIndex)}
            >
          </div>
        {/each}
      </div>
    </div>
  {/each}

  </div>
  <div class="trash"
       class:hovering={hoveringOverBasket === baskets[4].name}
       on:dragenter={() => hoveringOverBasket = baskets[4].name}
       on:dragleave={() => hoveringOverBasket = null}
       on:drop={event => drop(event, 4)}
       ondragover="return false">

  </div>
</div>



