<script>
    import { onMount } from 'svelte';
    import { browser } from '$app/environment';
    import ToDo from '../lib/components/ToDo.svelte';

    let immerFalse = false;
    let todoText = '';
    let todos = [];
    let draggedTodo;
  
    onMount(() => {
        if (browser) {
            const storedTodos = JSON.parse(
                localStorage.getItem('todos')
            );
            if (storedTodos) {
                todos = storedTodos;
            }
        }
    });
    function saveTodos() {
        // Save todos to local storage
        if (browser) {
            localStorage.setItem('todos', JSON.stringify(todos));
        }
    }
    function addTodo() {
        todos.push({ text: todoText, done: false });
        todos = todos;
        todoText = '';
        saveTodos();
    }
    function remove(index) {
        //delete entry
        todos.splice(index, 1);
        todos = todos;
        saveTodos();
    }
    function drop(event) {
        let dropedTodo = event.target.id;
        if ( draggedTodo < dropedTodo) {
            dropedTodo -= 1;
        }
        let movedTodo = todos.splice(draggedTodo, 1);
        draggedTodo = null;
        todos.splice(dropedTodo, 0, movedTodo[0]);
        todos = todos;
        saveTodos();
    }
</script>
<h1>TODO APP</h1>
<!-- textfeld -->
<input type="text" class="todo-input" bind:value={todoText} on:keypress={(event) => { if (event.key === "Enter") { addTodo() } }}/>
<!-- button add -->
<button on:click={addTodo}>ADD</button>
{#each todos as todo, index}
    <!-- todo -->
    <hr id={index} class="drag-target" on:dragover={(event) => event.preventDefault() } on:dragenter={(event) => { event.target.classList.toggle("drag-target-current") } } on:dragleave={(event) => { event.target.classList.toggle("drag-target-current") } } on:drop={ (event) => { drop(event); event.target.classList.toggle("drag-target-current") }}>
<!--Component ausgeschnitten-->
        <div id={index} class="todo-entry" class:done={todo.done} draggable="true" on:dragstart={(event) => draggedTodo = event.target.id }>
            <ToDo todoData={todo} index={index} on:removeEvent={remove}/> 
        </div>
        {/each}
    <hr id={todos.length} class="drag-target" class:drag-target-current={immerFalse} on:dragover={() => event.preventDefault() } on:dragenter={() => { event.target.classList.toggle("drag-target-current") } } on:dragleave={() => { event.target.classList.toggle("drag-target-current") } } on:drop={ () => { drop(event); event.target.classList.toggle("drag-target-current") }}>
<style>
    
    .drag-target {
        height: 5px;
        color: rgba(0, 0, 0, 0);
    }
    .drag-target-current {
        height: 20px !important;
        background-color: blue !important;
    }
</style>