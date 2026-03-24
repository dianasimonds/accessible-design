<script>
	import { onMount } from 'svelte';

	let items = $state([]);
	
	let firstName = $state('');
	let lastName = $state('');
	let age = $state('');
	let editId = $state(null);
	let nextId = $state(1);
	let loaded = false;

	onMount(() => {
		const stored = localStorage.getItem('crud-items');
		if (stored) {
			try {
				const parsed = JSON.parse(stored);
				if (Array.isArray(parsed)) {
					items = parsed;
					// Update nextId to be higher than any existing ID
					const maxId = items.reduce((max, item) => Math.max(max, item.id), 0);
					nextId = maxId + 1;
				}
			} catch (e) {
				console.error('Failed to parse items from local storage', e);
			}
		}
		loaded = true;
	});

	// Save to local storage whenever items change, but only after initial load
	$effect(() => {
		if (loaded) {
			localStorage.setItem('crud-items', JSON.stringify(items));
		}
	});

	function createItem() {
		const id = nextId;
		nextId += 1;
		
		items.push({
			id,
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

<div class="max-w-3xl mx-auto p-5 font-sans">
	<h1 class="text-2xl font-bold mb-5">CRUD (Local Storage)</h1>

	<div class="bg-white border border-gray-200 p-5 rounded-lg mb-5 shadow-sm">
		<h2 class="text-xl font-semibold mb-3">{editId ? 'Edit Person' : 'Add Person'}</h2>
		<form onsubmit={handleSubmit}>
			<div class="mb-3">
				<label for="fname" class="block mb-1 font-bold text-gray-700">First Name</label>
				<input id="fname" type="text" bind:value={firstName} required 
                    class="w-full p-2 border border-gray-300 rounded focus:border-blue-500 focus:ring-1 focus:ring-blue-500 outline-none" />
			</div>
			
			<div class="mb-3">
				<label for="lname" class="block mb-1 font-bold text-gray-700">Last Name</label>
				<input id="lname" type="text" bind:value={lastName} required 
                    class="w-full p-2 border border-gray-300 rounded focus:border-blue-500 focus:ring-1 focus:ring-blue-500 outline-none" />
			</div>
			
			<div class="mb-3">
				<label for="age" class="block mb-1 font-bold text-gray-700">Age</label>
				<input id="age" type="number" bind:value={age} required 
                    class="w-full p-2 border border-gray-300 rounded focus:border-blue-500 focus:ring-1 focus:ring-blue-500 outline-none" />
			</div>
			
			<div class="flex gap-2 mt-4">
				<button type="submit" 
                    class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 transition-colors cursor-pointer border-none">
                    {editId ? 'Update' : 'Add'}
                </button>
				{#if editId}
					<button type="button" onclick={cancelEdit}
                        class="px-4 py-2 bg-gray-500 text-white rounded hover:bg-gray-600 transition-colors cursor-pointer border-none">
                        Cancel
                    </button>
				{/if}
			</div>
		</form>
	</div>

	<div class="bg-white border border-gray-200 p-5 rounded-lg mb-5 shadow-sm">
		<div class="flex justify-between mb-3 items-center">
			<h2 class="text-xl font-semibold">People List</h2>
			{#if loaded}
				<span class="text-xs text-gray-500 bg-gray-100 px-2 py-1 rounded">Storage Active</span>
			{/if}
		</div>
        <div class="overflow-x-auto">
            <table class="w-full border-collapse">
                <thead>
                    <tr class="bg-gray-50 text-left">
                        <th class="p-3 border-b border-gray-200 font-bold text-gray-700">ID</th>
                        <th class="p-3 border-b border-gray-200 font-bold text-gray-700">First Name</th>
                        <th class="p-3 border-b border-gray-200 font-bold text-gray-700">Last Name</th>
                        <th class="p-3 border-b border-gray-200 font-bold text-gray-700">Age</th>
                        <th class="p-3 border-b border-gray-200 font-bold text-gray-700">Actions</th>
                    </tr>
                </thead>
                <tbody>
                    {#each items as item (item.id)}
                        <tr class="hover:bg-gray-50">
                            <td class="p-3 border-b border-gray-200">{item.id}</td>
                            <td class="p-3 border-b border-gray-200">{item.firstName}</td>
                            <td class="p-3 border-b border-gray-200">{item.lastName}</td>
                            <td class="p-3 border-b border-gray-200">{item.age}</td>
                            <td class="p-3 border-b border-gray-200">
                                <button onclick={() => startEdit(item)} 
                                    class="px-2 py-1 text-sm bg-blue-500 text-white rounded hover:bg-blue-600 mr-2 cursor-pointer border-none">
                                    Edit
                                </button>
                                <button onclick={() => deleteItem(item.id)}
                                    class="px-2 py-1 text-sm bg-red-500 text-white rounded hover:bg-red-600 cursor-pointer border-none">
                                    Delete
                                </button>
                            </td>
                        </tr>
                    {/each}
                    {#if items.length === 0}
                        <tr>
                            <td colspan="5" class="p-3 border-b border-gray-200 text-center text-gray-500">No data found</td>
                        </tr>
                    {/if}
                </tbody>
            </table>
        </div>
	</div>
</div>
