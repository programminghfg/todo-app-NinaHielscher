<script> 
    import { onMount } from 'svelte';
    import { browser } from '$app/environment';

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
        let dropedTodo = event.originalTarget.id;
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
    <hr id={index} class="drag-target" on:dragover={() => event.preventDefault() } on:dragenter={() => { event.target.classList.toggle("drag-target-current") } } on:dragleave={() => { event.target.classList.toggle("drag-target-current") } } on:drop={ () => { drop(event); event.target.classList.toggle("drag-target-current") }}>
    <div id={index} class="todo-entry" class:done={todo.done} draggable="true" on:dragstart={() => draggedTodo = event.originalTarget.id }>
            <!-- checkboxen -->
            <input type="checkbox" bind:checked={todo.done} on:change={saveTodos} />
            <!-- text -->
            <div>{todo.text}</div>
            <button
                    class="delete"
                    on:click={() => 
                        remove(index)
                    }>X</button
            >
    </div>
{/each}
    <hr id={todos.length} class="drag-target" class:drag-target-current={immerFalse} on:dragover={() => event.preventDefault() } on:dragenter={() => { event.target.classList.toggle("drag-target-current") } } on:dragleave={() => { event.target.classList.toggle("drag-target-current") } } on:drop={ () => { drop(event); event.target.classList.toggle("drag-target-current") }}>
<style>
    .delete {
            background-color: white;
            border: none;
    }
    .delete:hover {
            background-color: grey;
            font-weight: 700;
    }
    .done {
        color: grey;
        text-decoration: line-through;
    }
    .todo-entry {
            display: flex;
    }
    .drag-target {
        height: 5px;
        color: rgba(0, 0, 0, 0);
    }
    .drag-target-current {
        height: 20px !important;
        background-color: blue !important;
    }
</style>