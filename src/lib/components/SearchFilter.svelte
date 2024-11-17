<script lang="ts">
    import { createEventDispatcher } from 'svelte';
    export let onSearch: (filters: any) => void;
    
    const dispatch = createEventDispatcher();
    
    let filters = {
        keyword: '',
        genre: '',
        condition: '',
        availability: ''
    };

    let searchTerm = '';
    let selectedGenre = 'all';

    const genres = ['all', 'Fiction', 'Non-Fiction', 'Science', 'History', 'Biography', 'Other'];

    function handleSearch() {
        dispatch('search', filters);
    }

    function resetFilters() {
        filters = {
            keyword: '',
            genre: '',
            condition: '',
            availability: ''
        };
        handleSearch();
    }

    function handleSubmit() {
        const filters = {
            search: searchTerm,
            genre: selectedGenre === 'all' ? '' : selectedGenre
        };
        onSearch(filters);
    }
</script>

<div class="search-filters">
    <input
        type="text"
        placeholder="Search books by name or author ..."
        bind:value={searchTerm}
        on:input={() => handleSubmit()}
    />
    
    <select bind:value={selectedGenre} on:change={() => handleSubmit()}>
        {#each genres as genre}
            <option value={genre}>{genre}</option>
        {/each}
    </select>
    <button class="reset-btn" on:click={resetFilters}>Reset</button>
</div>

<style>
    .search-filters {
        display: flex;
        gap: 1rem;
        padding: 1rem;
        margin-bottom: 2rem;
        border-radius: 8px;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        }
        
        input, select {
        padding: 0.5rem;
        border: 1px solid #ddd;
        border-radius: 4px;
        font-size: 1rem;
        }
        
        input {
        flex: 1;
        }

        .reset-btn {
        padding: 0.5rem 1rem;
        background: #5f0f40;
        color: white;
        border: none;
        border-radius: 4px;
        cursor: pointer;
        transition: background 0.3s ease;
        }

        .reset-btn:hover {
        background: #e36414;
        }
</style>