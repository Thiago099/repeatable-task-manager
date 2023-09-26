<script>

  import moment from "moment"
  import Modal from "./components/Modal.svelte";
  let list = JSON.parse(localStorage.getItem("data")??"[]")
  for(const item of list)
  {
    if(item.Date != null)
    {
      item.Date = new Date(item.Date)
    }
  }
  let modalVisible = false; 
  let modalCallback;
  let modalTitle;

  let mode = "use"
  function Add()
  {
    list.push({
      Name: "",
      Date: null,
    })
    save()
    list = list
  }
  function setNow(item)
  {
    modalTitle = `Have you done "${item?.Name}" task now?`
    modalCallback = function()
    {
      item.Date = new Date()
      save()
      list = list
    }
    modalVisible = true;
  }
  function remove(item,index)
  {
    modalTitle = `Do you want to remove "${item?.Name}"?`
    modalCallback = function()
    {
      list.splice(index, 1)
      list = list
      save()
      list = list
    }
    modalVisible = true;

  }
  function confirm()
  {
    modalCallback()
    modalVisible = false;
  }
  function cancel()
  {
    modalVisible = false;
  }

  function save()
  {
    localStorage.setItem("data", JSON.stringify(list))
  }
</script>
<Modal bind:visible={modalVisible} width="600px">
  <span slot="header">
    {modalTitle}
  </span>
  <div style="display: flex;justify-content:space-between">
    <button class="red" on:click={cancel}><i class="fa-solid fa-xmark"></i> Cancel</button>
    <button class="green" on:click={confirm}><i class="fa-solid fa-check"></i> Confirm</button>
  </div>
</Modal>
<div class="app">
  <h1>Repeatable task manager</h1>
  <div>
    {#if mode == "use"}
      <button on:click={()=>mode = "edit"}><i class="fa-solid fa-pen-to-square"></i> Edit</button>
    {:else}
      <button on:click={()=>mode = "use"}><i class="fa-solid fa-user"></i> Use</button>
      <button on:click={Add}><i class="fa-solid fa-plus"></i> Add</button>
    {/if}
  </div>
  <div class="content">
    {#each list as item,index}
    <div class="item">
      {#if mode == "edit"}
        <button on:click={()=>remove(item, index)} class="red"><i class="fa-solid fa-trash"></i> Delete</button>
        <input type="text" bind:value={item.Name} on:blur={save}>
      {:else if mode == "use"}
        <button on:click={()=>setNow(item)} class="green"><i class="fa-solid fa-check"></i> Did it now</button>
        <div style="width:200px;overflow:hidden;text-overflow:ellipsis;">{item.Name}</div>
        {/if}
        <div style="width:100px">
          {#if item.Date == null}
            Never
          {:else}
              <div>{moment(item.Date).fromNow()}</div>
              <div>{item.Date.toLocaleDateString()}</div>
              <div>{item.Date.toLocaleTimeString()} </div>
          {/if}
        </div>
    </div>
    {/each}
  </div>
</div>

<style>
  .app{
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
  }
  .app > *{
    margin-top: 15px;
  }

  .content{
    display: flex;
    flex-direction: column;
  }
  .item{
    display: flex;
    display: flex;
    align-items: center;
  }
  .item > *{
    margin: 5px;
  }
</style>