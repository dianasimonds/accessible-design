<script>
	let items = $state([
		{ id: 1, firstName: 'John', lastName: 'Doe', age: 30 },
		{ id: 2, firstName: 'Jane', lastName: 'Smith', age: 25 }
	]);
	
	let firstName = $state('');
	let lastName = $state('');
	let age = $state('');
	let editId = $state(null);
	let nextId = 3;

	function createItem() {
		items.push({
			id: nextId++,
			firstName,
			lastName,
			age: parseInt(age)
		});
	}

	function updateItem() {
		const index = items.findIndex(i => i.id === editId);
		if (index !== -1) {
			items[index] = { id: editId, firstName, lastName, age: parseInt(age) };
		}
		editId = null;
	}

	function startEdit(item) {
		editId = item.id;
		firstName = item.firstName;
		lastName = item.lastName;
		age = item.age;
	}
	
	function cancelEdit() {
		editId = null;
		firstName = '';
		lastName = '';
		age = '';
	}

  function deleteItem(id) {
		const index = items.findIndex(i => i.id === id);
		if (index !== -1) {
			items.splice(index, 1);
		}
	}

	function handleSubmit(event) {
		event.preventDefault();
		
		if (editId) {
			updateItem();
		} else {
			createItem();
		}
		
		// Reset form
		firstName = '';
		lastName = '';
		age = '';
	}
</script>


<div class="container">
	<header>
		<h1>Accessible Design CRUD Demo</h1>
		<h2>Student Records</h2>
	</header>

	<section class="intro card">
		<img src="https://images.unsplash.com/photo-1516321318423-f06f85e504b3?auto=format&fit=crop&w=900&q=80" alt="Students collaborating at a table" />
		<p class="helper-text">Use this page to quickly manage people in the system.</p>
		<p><a class="text-link" href="/">Review the latest updates</a></p>
	</section>

	<section class="card">
		<h2 id="form-heading">{editId ? 'Edit Person' : 'Add Person'}</h2>
		<form onsubmit={handleSubmit} aria-labelledby="form-heading">
			<fieldset>
				<legend>{editId ? 'Edit Person Details' : 'Add Person Details'}</legend>
				<div class="form-group">
					<label for="first-name">First Name</label>
					<input id="first-name" type="text" placeholder="First Name" bind:value={firstName} required />
				</div>
				<div class="form-group">
					<label for="last-name">Last Name</label>
					<input id="last-name" type="text" placeholder="Last Name" bind:value={lastName} required />
				</div>
				<div class="form-group">
					<label for="age">Age</label>
					<input id="age" type="number" placeholder="Age" bind:value={age} required />
				</div>
			</fieldset>
			<div class="actions">
				<button type="submit">{editId ? 'Update' : 'Add'}</button>
				{#if editId}
					<button type="button" class="secondary" onclick={cancelEdit}>Cancel</button>
				{/if}
			</div>
		</form>
	</section>

	<section class="card">
		<h2>People List</h2>
		<table>
			<thead>
				<tr>
					<th scope="col">ID</th>
					<th scope="col">First Name</th>
					<th scope="col">Last Name</th>
					<th scope="col">Age</th>
					<th scope="col">Actions</th>
				</tr>
			</thead>
			<tbody>
				{#each items as item}
					<tr>
						<td>{item.id}</td>
						<td>{item.firstName}</td>
						<td>{item.lastName}</td>
						<td>{item.age}</td>
						<td>
							<button class="small icon-button" aria-label="Edit {item.firstName} {item.lastName}" onclick={() => startEdit(item)}><span aria-hidden="true">✎</span></button>
							<button class="small danger icon-button" aria-label="Delete {item.firstName} {item.lastName}" onclick={() => deleteItem(item.id)}><span aria-hidden="true">✕</span></button>
						</td>
					</tr>
				{/each}
				{#if items.length === 0}
					<tr>
						<td colspan="5" style="text-align: center;">No data found</td>
					</tr>
				{/if}
			</tbody>
		</table>
	</section>
</div>

<style>
	.container {
		max-width: 800px;
		margin: 0 auto;
		padding: 20px;
		font-family: sans-serif;
	}
	
	h1 { margin-bottom: 20px; }

	
	.card {
		border: 1px solid #ddd;
		padding: 20px;
		border-radius: 8px;
		margin-bottom: 20px;
		background: white;
	}
	
	.form-group {
		margin-bottom: 10px;
	}

	fieldset {
		border: 0;
		padding: 0;
		margin: 0;
	}

	.intro img {
		width: 100%;
		height: 180px;
		object-fit: cover;
		border-radius: 6px;
		margin-bottom: 12px;
	}

	.helper-text {
		color: #333;
		margin-bottom: 12px;
	}

	.text-link {
		color: #0056b3;
		text-decoration: underline;
	}
	
	input {
		width: 100%;
		padding: 8px;
		box-sizing: border-box;
		border: 1px solid #ccc;
		border-radius: 4px;
	}
	
	.actions {
		margin-top: 15px;
		display: flex;
		gap: 10px;
	}
	
	button {
		padding: 8px 16px;
		background-color: #8ec5ff;
		color: #dbeeff;
		border: none;
		border-radius: 4px;
		cursor: pointer;
	}
	
	button:hover { background-color: #0056b3; }
	
	button.secondary {
		background-color: #6c757d;
	}
	button.secondary:hover { background-color: #5a6268; }
	
	button.danger {
        background-color: #dc3545;
	}
	button.danger:hover { background-color: #bd2130; }
	
	button.small {
		padding: 4px 8px;
		font-size: 0.9em;
	}

	.icon-button {
		min-width: 36px;
	}

	
	table {
		width: 100%;
		border-collapse: collapse;
	}
	
	th,
	td {
		padding: 10px;
		border-bottom: 1px solid #ddd;
		text-align: left;
	}

	th {
		background-color: #f8f9fa;
	}
</style>
