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
	<h1>CRUD (Vanilla CSS)</h1>

	<div class="card">
		<h2>{editId ? 'Edit Person' : 'Add Person'}</h2>
		<form onsubmit={handleSubmit}>
			<div class="form-group">
				<label for="fname">First Name</label>
				<input id="fname" type="text" bind:value={firstName} required />
			</div>
			
			<div class="form-group">
				<label for="lname">Last Name</label>
				<input id="lname" type="text" bind:value={lastName} required />
			</div>
			
			<div class="form-group">
				<label for="age">Age</label>
				<input id="age" type="number" bind:value={age} required />
			</div>
			
			<div class="actions">
				<button type="submit">{editId ? 'Update' : 'Add'}</button>
				{#if editId}
					<button type="button" class="secondary" onclick={cancelEdit}>Cancel</button>
				{/if}
			</div>
		</form>
	</div>

	<div class="card">
		<h2>People List</h2>
		<table>
			<thead>
				<tr>
          <th>ID</th>
					<th>First Name</th>
					<th>Last Name</th>
					<th>Age</th>
					<th>Actions</th>
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
							<button class="small" onclick={() => startEdit(item)}>Edit</button>
							<button class="small danger" onclick={() => deleteItem(item.id)}>Delete</button>
						</td>
					</tr>
				{/each}
				{#if items.length === 0}
					<tr>
						<td colspan="4" style="text-align: center;">No data found</td>
					</tr>
				{/if}
			</tbody>
		</table>
	</div>
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
	
	label {
		display: block;
		margin-bottom: 5px;
		font-weight: bold;
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
		background-color: #007bff;
		color: white;
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
	
	table {
		width: 100%;
		border-collapse: collapse;
	}
	
	th, td {
		padding: 10px;
		border-bottom: 1px solid #ddd;
		text-align: left;
	}
	
	th {
		background-color: #f8f9fa;
	}
</style>
