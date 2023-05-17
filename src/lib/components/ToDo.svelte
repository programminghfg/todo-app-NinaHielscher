<script>
    import {createEventDispatcher} from 'svelte';

    export let todoData = undefined;
    export let index = undefined;
    export const testProp = 'das ein test'

    const dispatch = createEventDispatcher();

    function triggerDelete(){
            dispatch('removeEvent',index);
    }

</script>

    <!-- checkboxen -->
    <!-- <input type="checkbox" bind:checked={todoData.done} on:change={saveTodos} /> -->
    <input type="checkbox" bind:checked={todoData.done}  />
    <!-- text -->
    <div>{todoData.text}</div>
    <button
            class="delete"
            on:click={() =>
                triggerDelete()
            }>X</button
    >

<slot/>

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
</style>