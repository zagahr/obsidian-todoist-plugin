<script lang="ts">
    import { onDestroy } from "svelte";
    import type { TodoistApi, ITodoistMetadata } from "../api/api";
    import type { Task, ID } from "../api/models";
    import type { ISettings } from "../settings";
    import CompletedTaskRenderer from "./CompletedTaskRenderer.svelte";
import NoCompletedTasks from "./NoCompletedTasks.svelte";
    import NoTaskDisplay from "./NoTaskDisplay.svelte";
    import TaskRenderer from "./TaskRenderer.svelte";
  
    export let tasks: Task[];
    export let settings: ISettings;
    export let api: TodoistApi;
    export let sorting: string[];
    export let renderProject: boolean = true;
    export let renderNoTaskInfo: boolean = true;
  
    let metadata: ITodoistMetadata = null;
    const metadataUnsub = api.metadata.subscribe((value) => (metadata = value));
  
    onDestroy(() => {
      metadataUnsub();
    });
  
    let tasksPendingClose: ID[] = [];
    $: todos = tasks
      .filter((task) => !tasksPendingClose.includes(task.id))
      .sort((first: Task, second: Task) => first.compareTo(second, sorting));
  
    async function onClickTask(task: Task) {
    }
  </script>
  
  {#if todos.length != 0}
    <ul class="contains-task-list todoist-task-list">
      {#each todos as todo (todo.id)}
        <CompletedTaskRenderer
          {onClickTask}
          {metadata}
          {settings}
          {api}
          {sorting}
          {renderProject}
          {todo} />
      {/each}
    </ul>
  {:else if renderNoTaskInfo}
    <NoCompletedTasks />
  {/if}
  