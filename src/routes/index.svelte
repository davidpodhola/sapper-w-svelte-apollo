<script lang="ts">
  import { mutation, query } from "svelte-apollo";
  import { gql } from "@apollo/client";
  const EVERYTHING = gql`
    {
      todos {
        data {
          id
          title
          completed
        }
      }
    }
  `;

  const ADD = gql`
    mutation {
      createTodo(input: { title: "test 123", completed: false }) {
        id
      }
    }
  `;
  const addTodo = mutation(ADD);

  const todos = query(EVERYTHING, {
    // variables, fetchPolicy, errorPolicy, and others
  });

  function reload() {
    todos.refetch();
  }

  const add = async () => {
    await addTodo({});
  }
</script>

<ul>
  {#if $todos.loading}
    <li>Loading...</li>
  {:else if $todos.error}
    <li>ERROR: {$todos.error.message}</li>
  {:else}
    {#each $todos.data.todos.data as todo (todo.id)}
      <li>{todo.title} is {todo.completed ? 'completed' : 'to be done'}</li>
    {/each}
  {/if}
</ul>

<button on:click={reload}>Reload</button>
<button on:click={add}>Add</button>
