<script>
     import { fade, fly } from 'svelte/transition';
     import { bounceIn } from 'svelte/easing';
     import '../style.css';
     import { writable } from 'svelte/store';
     let todoItem = '';
     let storedList;
     let urgent, important, low, someday, id; 
     let todoList = writable([]);
     let normal = true;
     let selected = 'normal';

     if (typeof window !== 'undefined' && typeof localStorage !== 'undefined') {
          storedList = localStorage.getItem('storedList');
          if(storedList) {
               $todoList = (JSON.parse(storedList));
          }
     }

     $: isDone = $todoList.filter(item => item.done);
     $: urgentDone = urgentList.filter(item => item.done);
     $: importantDone = importantList.filter(item => item.done);
     $: normalDone = normalList.filter(item => item.done);
     $: lowDone = lowList.filter(item => item.done);
     $: somedayDone = somedayList.filter(item => item.done);
     $: urgentList = $todoList.filter(item => item.urgent);
     $: importantList = $todoList.filter(item => item.important);
     $: normalList = $todoList.filter(item => item.normal);
     $: lowList = $todoList.filter(item => item.low);
     $: somedayList = $todoList.filter(item => item.someday);

     function updateList() {
          return storedList = localStorage.setItem('storedList', JSON.stringify($todoList));
     }
     function addToArray() {
          if (todoItem == '') {
               return;
          }
          //todoList.push(todoItem);
          //todoList = todoList;
          id = Math.floor(100000 + Math.random() * 900000);
          $todoList = [...$todoList, {
               id: id,
               text: todoItem,
               done: false,
               urgent: urgent,
               important: important,
               normal: normal,
               low: low,
               someday: someday,
          }];
          console.log($todoList);
          updateList();
          todoItem = '';
          urgent = false;
          important = false;
          normal = true;
          low = false;
          someday = false;
          selected = 'normal';
          console.log(selected); 
     }
     function removeThis(item) {
          $todoList = $todoList.filter((t) => t.id !== item.id);
          updateList();
     }
     function clearDone() {
          $todoList = $todoList.filter(item => !item.done)
          updateList();
     }
     function clearUrgent() {
          urgentList = urgentList.filter(item => !item.urgent)
          $todoList = $todoList.filter(item => !item.urgent)
          updateList();
     }
     function clearImportant() {
          importantList = importantList.filter(item => !item.important)
          $todoList = $todoList.filter(item => !item.important)
          updateList();
     }
     function clearNormal() {
          normalList = normalList.filter(item => !item.normal)
          $todoList = $todoList.filter(item => !item.normal)
          updateList();
     }
     function clearLow() {
          lowList = lowList.filter(item => !item.low)
          $todoList = $todoList.filter(item => !item.low)
          updateList();
     }
     function clearSomeday() {
          somedayList = somedayList.filter(item => !item.someday)
          $todoList = $todoList.filter(item => !item.someday)
          updateList();
     }
     function clearAll() {
          $todoList = [];
          localStorage.clear();
     }
     function itemImportance(event) {
          selected = event.currentTarget.value;
          switch(selected) {
               case 'urgent':
                    urgent = true;
                    important = false;
                    normal = false;
                    low = false;
                    someday = false;
                    break;
               case 'important':
                    urgent = false;
                    important = true;
                    normal = false;
                    low = false;
                    someday = false;
                    break;
               case 'normal':
                    urgent = false;
                    important = false;
                    normal = true;
                    low = false;
                    someday = false;
                    break;
               case 'low':
                    urgent = false;
                    important = false;
                    normal = false;
                    low = true;
                    someday = false;
                    break;
               case 'someday':
                    urgent = false;
                    important = false;
                    normal = false;
                    low = false;
                    someday = true;
                    break;
          }
          console.log(selected);
     }
</script>

<div class="agendazen tasks">
     <h1>AgendaZen</h1>
     <form on:submit|preventDefault={addToArray}>
          <h3>
               <!-- svelte-ignore a11y-label-has-associated-control -->
               <label>New Task:</label>
          </h3>
          <input type="text" class="input__lg" bind:value={todoItem}>
          <h3>
               <!-- svelte-ignore a11y-label-has-associated-control -->
               <legend>Importance:</legend>
          </h3>
          <div class="importance">
               <label for="urgent" class="urgent">
                    <input type="radio" name="importance" id="urgent" value="urgent" on:change={itemImportance}> Urgent
               </label>
               <label for="important" class="important">
                    <input type="radio" name="importance" id="important" value="important" on:change={itemImportance}> Important
               </label>
               <label for="normal" class="normal">
                    <input bind:group={selected} type="radio" name="importance" id="normal" value="normal" on:change={itemImportance} checked> Normal
               </label>
               <label for="low" class="low">
                    <input type="radio" name="importance" id="low" value="low" on:change={itemImportance}> Low
               </label>
               <label for="someday" class="someday">
                    <input type="radio" name="importance" id="someday" value="someday" on:change={itemImportance}> Someday
               </label>
          </div>
          <div>
               <button type="submit" class="btn">ADD</button>
          </div>
     </form>
     <!--<ul role="list" class="todoList">
          {#each $todoList as item, index}
               <li in:fly={{ y:10, duration: 500 }} eased = bounceIn(500) out:fade={{duration: 500}} class:urgent={item.urgent} class:important={item.important} class:normal={item.normal} class:low={item.low} class:someday={item.someday}>
                    <input type="checkbox" bind:checked={item.done} on:change={updateList}>
                    <span class:done={item.done}>{item.text}</span>-->
                    <!-- svelte-ignore a11y-click-events-have-key-events -->
                    <!--<span on:click={() => removeThis(index)} 
                    class="remove" role="button" aria-pressed="false" tabindex="0"><span class="visually-hidden">Remove This</span>&times;</span>
               </li>
          {/each}
     </ul>-->
</div>

{#if urgentList.length > 0}
<div class="agendazen urgentList" in:fly={{ y:10, duration: 500 }} eased = bounceIn(500) out:fade={{duration: 500}}>
     <h2 class="urgent">Urgent</h2>
     <ul role="list" class="todoList">
          {#each urgentList as item, index}
               <li in:fly={{ y:10, duration: 500 }} eased = bounceIn(500) out:fade={{duration: 500}}>
                    <input type="checkbox" bind:checked={item.done} on:change={updateList}>
                    <span class:done={item.done}>{item.text}</span>
                    <!-- svelte-ignore a11y-click-events-have-key-events -->
                    <span on:click={() => removeThis(item)} class="remove" role="button" aria-pressed="false" tabindex="0"><span class="visually-hidden">Remove This</span>&#128465</span> 
               </li>
          {/each}
     </ul>
     {#if urgentDone.length > 0}
     <button on:click={clearUrgent} class="removeBtn">REMOVE COMPLETED URGENT TASKS</button>
     {/if}
</div>
{/if}

{#if importantList.length > 0}
<div class="agendazen importantList" in:fly={{ y:10, duration: 500 }} eased = bounceIn(500) out:fade={{duration: 500}}>
     <h2 class="important">Important</h2>
     <ul role="list" class="todoList">
          {#each importantList as item, index}
               <li in:fly={{ y:10, duration: 500 }} eased = bounceIn(500) out:fade={{duration: 500}} >
                    <input type="checkbox" bind:checked={item.done} on:change={updateList}>
                    <span class:done={item.done}>{item.text}</span>
                    <!-- svelte-ignore a11y-click-events-have-key-events -->
                    <span on:click={() => removeThis(item)} class="remove" role="button" aria-pressed="false" tabindex="0"><span class="visually-hidden">Remove This</span>&#128465</span> 
               </li>
          {/each}
     </ul>
     {#if importantDone.length > 0}
     <button on:click={clearImportant} class="removeBtn">REMOVE COMPLETED IMPORTANT TASKS</button>
     {/if}
</div>
{/if}

{#if normalList.length > 0}
<div class="agendazen normalList" in:fly={{ y:10, duration: 500 }} eased = bounceIn(500) out:fade={{duration: 500}}>
     <h2 class="normal">Normal</h2>
     <ul role="list" class="todoList">
          {#each normalList as item, index}
               <li in:fly={{ y:10, duration: 500 }} eased = bounceIn(500) out:fade={{duration: 500}}>
                    <input type="checkbox" bind:checked={item.done} on:change={updateList}>
                    <span class:done={item.done}>{item.text}</span>
                    <!-- svelte-ignore a11y-click-events-have-key-events -->
                    <span on:click={() => removeThis(item)} class="remove" role="button" aria-pressed="false" tabindex="0"><span class="visually-hidden">Remove This</span>&#128465</span> 
               </li>
          {/each}
     </ul>
     {#if normalDone.length > 0}
     <button on:click={clearNormal} class="removeBtn">REMOVE COMPLETED NORMAL TASKS</button>
     {/if}
</div>
{/if}

{#if lowList.length > 0}
<div class="agendazen lowList" in:fly={{ y:10, duration: 500 }} eased = bounceIn(500) out:fade={{duration: 500}}>
     <h2 class="low">Low</h2>
     <ul role="list" class="todoList">
          {#each lowList as item, index}
               <li in:fly={{ y:10, duration: 500 }} eased = bounceIn(500) out:fade={{duration: 500}} >
                    <input type="checkbox" bind:checked={item.done} on:change={updateList}>
                    <span class:done={item.done}>{item.text}</span>
                    <!-- svelte-ignore a11y-click-events-have-key-events -->
                    <span on:click={() => removeThis(item)} class="remove" role="button" aria-pressed="false" tabindex="0"><span class="visually-hidden">Remove This</span>&#128465</span> 
               </li>
          {/each}
     </ul>
     {#if lowDone.length > 0}
     <button on:click={clearLow} class="removeBtn">REMOVE COMPLETED LOW TASKS</button>
     {/if}
</div>
{/if}

{#if somedayList.length > 0}
<div class="agendazen somedayList" in:fly={{ y:10, duration: 500 }} eased = bounceIn(500) out:fade={{duration: 500}}>
     <h2 class="someday">Someday</h2>
     <ul>
          {#each somedayList as item, index}
               <li in:fly={{ y:10, duration: 500 }} eased = bounceIn(500) out:fade={{duration: 500}} >
                    <input type="checkbox" bind:checked={item.done} on:change={updateList}>
                    <span class:done={item.done}>{item.text}</span>
                    <!-- svelte-ignore a11y-click-events-have-key-events -->
                    <span on:click={() => removeThis(item)} class="remove" role="button" aria-pressed="false" tabindex="0"><span class="visually-hidden">Remove This</span>&#128465</span> 
               </li>
          {/each}
     </ul>
     {#if somedayDone.length > 0}
     <button on:click={clearSomeday} class="removeBtn">REMOVE COMPLETED SOMEDAY TASKS</button>
     {/if}
</div>
{/if}
<div class="bottom">
     {#if isDone.length > 0}
     <button on:click={clearDone} class="clear">REMOVE COMPLETED TASKS</button>
     {/if}
     {#if $todoList.length > 0}
     <button on:click={clearAll} class="clear">REMOVE ALL TASKS</button>
     {/if}
</div>
<style>
     .agendazen {
          background: #ffffffb7;
          margin: 1vw auto;
          padding: 1rem;
          max-width: 500px;
          box-shadow:
               0 2px 4px 0 rgb(0 0 0 / 20%),
               0 2.5rem 5rem 0 rgb(0 0 0 / 10%);
          border-radius: 3px;
     }
     .bottom {
          margin: 2vw auto 5vw;
     }
     .tasks {
          margin: 5vw auto 1vw;
     }
     .urgentList {
         outline: 4px dotted #ff0000; 
         outline-offset: -5px;  
         display: flow-root; 
     }
     .importantList {
         outline: 4px dotted #ff5c00; 
         outline-offset: -5px;  
         display: flow-root; 
     }
     .normalList {
         outline: 4px dotted #001AFF; 
         outline-offset: -5px;  
         display: flow-root; 
     }
     .lowList {
         outline: 4px dotted #039200; 
         outline-offset: -5px;  
         display: flow-root; 
     }
     .somedayList {
         outline: 4px dotted #bd00ff; 
         outline-offset: -5px;  
         display: flow-root;     
     }   
     button {
          border: none;
          margin: 0.5em 0;
          padding: 0;
          width: auto;
          overflow: visible;
          background: #bb00ff36;
          color: inherit;
          font: inherit;
          line-height: normal;
          -webkit-font-smoothing: inherit;
          -moz-osx-font-smoothing: inherit;
          appearance: none;
          font-weight: 700;
     }
     .importance {
          padding: 0;
          display: flex;
          justify-content: space-evenly;
     }
     .btn {
          border: 1px solid darkslategrey;
          padding: 0.5em;
          margin: 1em 0 0 0;
          cursor: pointer;
          display: inline-block;
          width: 100%;
          font-size: 1.1rem;
          box-shadow:
               0 2px 4px 0 rgb(47 79 79 / 20%),
               0 2.5rem 5rem 0 rgb(47 79 79 / 10%);
          border-radius: 25px;
     }
     .clear {
          border: 1px solid darkslategrey;
          padding: 0.5em;
          cursor: pointer;
          display: block;
          width: 450px;
          margin: 1vw auto;
          font-size: 1.1rem;
          background-color: rgba(255, 255, 255, 0.753);
          box-shadow:
               0 2px 4px 0 rgb(47 79 79 / 20%),
               0 2.5rem 5rem 0 rgb(47 79 79 / 10%);
          border-radius: 25px;
     }
     .removeBtn {
          border: 1px solid darkslategrey;
          padding: 0.5em;
          margin: 0 1em;
          cursor: pointer;
          display: inline-block;
          font-size: 0.8rem;
          background-color: rgba(255, 255, 0, 0.05);
          width: 60%;
          box-shadow:
               0 2px 4px 0 rgb(47 79 79 / 20%),
               0 2.5rem 5rem 0 rgb(47 79 79 / 10%);
          border-radius: 25px;
          float: right;
     }
     ul {
          list-style: none;
          padding: 0 1vw;
     }
     li {
          font-size: 1.5rem;
          font-weight: 300;
          font-style: italic;
          border-bottom: rgba(47, 79, 79, 0.24) dotted 0.25px;
          display: flow-root;
     }
     .urgentList li input[type="checkbox"] {
          width: 1.1em;
          height: 1.1rem;
          accent-color: #ff0000;
     }
     .importantList li input[type="checkbox"] {
          width: 1.1em;
          height: 1.1rem;
          accent-color: #ff5c00;
     }
     .normalList li input[type="checkbox"] {
          width: 1.1em;
          height: 1.1rem;
          accent-color: #001AFF;
     }
     .lowList li input[type="checkbox"] {
          width: 1.1em;
          height: 1.1rem;
          accent-color: #039200;
     }
     .somedayList li input[type="checkbox"] {
          width: 1.1em;
          height: 1.1rem;
          accent-color: #bd00ff;
     }
     .done {
          color: darkslategrey;
          text-decoration: line-through solid 1px;
          opacity: 0.5;
     }
     .remove {
          color: darkslategrey;
          cursor: pointer;
          float: right;
          font-style: normal;
          font-size: 1.2rem;
          line-height: 2;
     }
     .urgent {
          color: #FF0000;
          text-decoration-color: #ff0000;
     }
     #urgent {
          accent-color: #ff0000;
     }
     .important {
          color: #FF5C00;
          text-decoration-color: #FF5C00;
     }
     #important {
          accent-color: #ff5c00;
     }
     .normal {
          color: #001AFF; 
          text-decoration-color: #001aff;
     }
     #normal {
          accent-color: #001AFF;
     }
     .low {
          color: #039200;
          text-decoration-color: #039200;
     }
     #low {
          accent-color: #039200;
     }
     .someday {
          color: #bd00ff;
          text-decoration-color: #bb00ff;
     }
     #someday {
          accent-color: #bd00ff;          
     }

     @media screen and (max-width: 450px) {
     .importance {
          flex-direction: column;
     }
     .clear {
          width: 300px;
     }
}
</style>