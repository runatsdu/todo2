<script>
	import { onMount } from 'svelte';

	let todos = [];
	let newTodoText = '';
	let newTodoDueDate = new Date().toISOString().split('T')[0];
	let showAll = false;

	function addTodo() {
		if (!newTodoText.trim()) return;
		
		todos = [...todos, {
			id: Date.now(),
			text: newTodoText,
			completed: null,
			dueDate: new Date(newTodoDueDate)
		}];
		
		newTodoText = '';
		newTodoDueDate = new Date().toISOString().split('T')[0];
	}

	function completeTodo(id) {
		todos = todos.map(todo => {
			if (todo.id === id) {
				return { ...todo, completed: new Date() };
			}
			return todo;
		});
	}

	function uncompleteTodo(id) {
		todos = todos.map(todo => {
			if (todo.id === id) {
				return { ...todo, completed: null };
			}
			return todo;
		});
	}

	$: filteredTodos = showAll ? todos : todos.filter(todo => {
		const today = new Date();
		today.setHours(0, 0, 0, 0);
		const dueDate = new Date(todo.dueDate);
		dueDate.setHours(0, 0, 0, 0);
		return !todo.completed && dueDate <= today;
	});

	$: sortedTodos = filteredTodos.sort((a, b) => new Date(a.dueDate) - new Date(b.dueDate));
</script>

<svelte:head>
	<title>Todo List</title>
</svelte:head>

<main class="container">
	<h1>Todo List</h1>

	<form on:submit|preventDefault={addTodo} class="add-todo">
		<input
			type="text"
			bind:value={newTodoText}
			placeholder="What needs to be done?"
			required
		/>
		<input
			type="date"
			bind:value={newTodoDueDate}
			required
		/>
		<button type="submit">Add Todo</button>
	</form>

	<div class="filters">
		<label>
			<input type="checkbox" bind:checked={showAll}>
			Show all todos
		</label>
	</div>

	<ul class="todo-list">
		{#each sortedTodos as todo (todo.id)}
			<li class="todo-item" class:completed={todo.completed}>
				<div class="todo-content">
					<span class="todo-text">{todo.text}</span>
					<span class="todo-date">Due: {new Date(todo.dueDate).toLocaleDateString()}</span>
					{#if todo.completed}
						<span class="completed-date">
							Completed: {new Date(todo.completed).toLocaleDateString()}
						</span>
					{/if}
				</div>
				<button 
					on:click={() => todo.completed ? uncompleteTodo(todo.id) : completeTodo(todo.id)}
					class="toggle-btn"
				>
					{todo.completed ? 'Undo' : 'Complete'}
				</button>
			</li>
		{:else}
			<li class="empty-state">No todos to show</li>
		{/each}
	</ul>
</main>

<style>
	.container {
		max-width: 600px;
		margin: 0 auto;
		padding: 2rem;
	}

	h1 {
		color: #333;
		text-align: center;
		margin-bottom: 2rem;
	}

	.add-todo {
		display: flex;
		gap: 1rem;
		margin-bottom: 2rem;
	}

	.add-todo input[type="text"] {
		flex: 1;
		padding: 0.5rem;
		border: 1px solid #ddd;
		border-radius: 4px;
	}

	.add-todo input[type="date"] {
		padding: 0.5rem;
		border: 1px solid #ddd;
		border-radius: 4px;
	}

	.add-todo button {
		padding: 0.5rem 1rem;
		background-color: #4075a6;
		color: white;
		border: none;
		border-radius: 4px;
		cursor: pointer;
	}

	.add-todo button:hover {
		background-color: #2c5282;
	}

	.filters {
		margin-bottom: 1rem;
	}

	.todo-list {
		list-style: none;
		padding: 0;
	}

	.todo-item {
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 1rem;
		margin-bottom: 0.5rem;
		background-color: white;
		border-radius: 4px;
		box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
	}

	.todo-content {
		flex: 1;
	}

	.todo-text {
		display: block;
		font-size: 1.1rem;
		margin-bottom: 0.25rem;
	}

	.todo-date, .completed-date {
		display: block;
		font-size: 0.9rem;
		color: #666;
	}

	.completed {
		background-color: #f8f8f8;
	}

	.completed .todo-text {
		text-decoration: line-through;
		color: #888;
	}

	.toggle-btn {
		padding: 0.5rem 1rem;
		background-color: #48bb78;
		color: white;
		border: none;
		border-radius: 4px;
		cursor: pointer;
		margin-left: 1rem;
	}

	.completed .toggle-btn {
		background-color: #718096;
	}

	.toggle-btn:hover {
		opacity: 0.9;
	}

	.empty-state {
		text-align: center;
		color: #666;
		padding: 2rem;
	}
</style>