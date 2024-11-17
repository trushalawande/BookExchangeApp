<script lang="ts">
    import { onMount } from 'svelte';
    import BookCard from '$lib/components/BookCard.svelte';
    import SearchFilter from '$lib/components/SearchFilter.svelte';
    import { fade, crossfade } from 'svelte/transition';

    let books = [];
    let loading = true;
    let currentPage = 1;
    let totalPages = 1;
    let totalBooks = 0;
    const booksPerPage = 12;

    const bannerMessages = [
        {
            title: "Book Exchange Platform",
            subtitle: "Exchange books with readers worldwide"
        },
        {
            title: "Discover New Stories",
            subtitle: "Find your next favorite book"
        },
        {
            title: "Connect Through Reading",
            subtitle: "Join our community of book lovers"
        },
        {
            title: "Share Your Collection",
            subtitle: "Give your books a new home"
        }
    ];

    let currentBannerIndex = 0;

    const [send, receive] = crossfade({
        duration: 500,
        fallback(node, params) {
            return {
                duration: 500,
                css: t => `
                    opacity: ${t};
                    position: absolute;
                    width: 100%;
                `
            };
        }
    });

    function rotateBanner() {
        currentBannerIndex = (currentBannerIndex + 1) % bannerMessages.length;
    }

    onMount(async () => {
        await fetchBooks();
        const interval = setInterval(rotateBanner, 5000); // Rotate every 5 seconds
        return () => clearInterval(interval);
    });

    async function fetchBooks(filters = {}) {
        loading = true; 
        try {
            const queryParams = new URLSearchParams();
            if (filters.search) queryParams.append('search', filters.search);
            if (filters.genre) queryParams.append('genre', filters.genre);
            queryParams.append('page', currentPage.toString());
            queryParams.append('limit', booksPerPage.toString());
            
            const response = await fetch(`/api/books?${queryParams}`);
            const data = await response.json();
            books = data.books;  // Extract the books array from the response
            totalBooks = data.total;
            totalPages = Math.ceil(totalBooks / booksPerPage);
            console.log('Fetched books:', books);
        } catch (error) {
            console.error('Error fetching books:', error);
        } finally {
            loading = false; 
        }
    }

    function handleSearch(filters) {
        currentPage = 1;
        fetchBooks(filters);
    }

    function handlePageChange(newPage: number) {
        if (newPage >= 1 && newPage <= totalPages) {
            currentPage = newPage;
            fetchBooks();
        }
    }

    $: visiblePages = getVisiblePages(currentPage, totalPages);

    function getVisiblePages(current: number, total: number) {
        let pages = [];
        const maxVisible = 5;
        
        if (total <= maxVisible) {
            return Array.from({length: total}, (_, i) => i + 1);
        }

        if (current <= 3) {
            pages = [1, 2, 3, 4, '...', total];
        } else if (current >= total - 2) {
            pages = [1, '...', total - 3, total - 2, total - 1, total];
        } else {
            pages = [1, '...', current - 1, current, current + 1, '...', total];
        }
        
        return pages;
    }
</script>

<div class="container">
    <div class="banner">
        <div class="banner-wrapper">
            {#key currentBannerIndex}
                <div 
                    class="banner-content"
                    in:receive={{key: currentBannerIndex}}
                    out:send={{key: currentBannerIndex}}
                >
                    <h1>{bannerMessages[currentBannerIndex].title}</h1>
                    <p>{bannerMessages[currentBannerIndex].subtitle}</p>
                </div>
            {/key}
        </div>
    </div>

    <main>
        <SearchFilter onSearch={handleSearch} />
        
        {#if loading}
            <div class="loading">Loading...</div>
        {:else if books && books.length > 0}
            <div class="book-grid">
                {#each books as book}
                    <BookCard {book} />
                {/each}
            </div>
            
            {#if totalPages > 1}
                <div class="pagination">
                    <button 
                        class="page-btn" 
                        disabled={currentPage === 1}
                        on:click={() => handlePageChange(currentPage - 1)}
                    >
                        Previous
                    </button>
                    
                    {#each visiblePages as page}
                        {#if page === '...'}
                            <span class="ellipsis">...</span>
                        {:else}
                            <button 
                                class="page-btn" 
                                class:active={page === currentPage}
                                on:click={() => handlePageChange(page)}
                            >
                                {page}
                            </button>
                        {/if}
                    {/each}
                    
                    <button 
                        class="page-btn" 
                        disabled={currentPage === totalPages}
                        on:click={() => handlePageChange(currentPage + 1)}
                    >
                        Next
                    </button>
                </div>
            {/if}
        {:else}
            <div class="no-books">No books found</div>
        {/if}
    </main>
</div>

<style>
    .banner-wrapper {
        position: relative;
        min-height: 150px;
        display: flex;
        align-items: center;
        justify-content: center;
    }

    .banner-content {
        position: absolute;
        width: 100%;
        left: 0;
        right: 0;
    }

    .banner {
        background: rgba(139, 71, 181, 0.2);
        backdrop-filter: blur(10px);
        border: 1px solid rgba(255, 255, 255, 0.1);
        color: white;
        padding: 4rem 2rem;
        text-align: center;
        margin: 1rem;
        margin-bottom: 2rem;
        border-radius: 12px;
        position: relative;
        overflow: hidden;
        box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    }

    .banner h1 {
        font-size: 2.5rem;
        margin-bottom: 1rem;
        text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
    }

    .banner p {
        font-size: 1.2rem;
        opacity: 0.9;
        text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.2);
    }

    .book-grid {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
        gap: 1rem;  
        padding: 1rem 0 1rem 0;  /* Changed from padding: 1rem; to remove right padding */
        grid-auto-flow: dense;
    }

    .container {
        min-height: 100vh;
        display: flex;
        flex-direction: column;
        background: transparent;
    }

    main {
        flex: 1;
        overflow-y: auto;
        padding: 1rem 0 1rem 0; 
        background: rgba(255, 255, 255, 0.1);
        backdrop-filter: blur(5px);
        border-radius: 12px;
        margin: 0 1rem;
    }

    @media (max-width: 768px) {
        .book-grid {
            gap: 0.5rem;
            padding: 0.5rem 0 0.5rem 0;
        }
    }

    @media (max-width: 480px) {
        .book-grid {
            gap: 0.25rem;
            padding: 0.25rem 0 0.25rem 0;
        }
    }

    .no-books {
        text-align: center;
        font-size: 2rem;
        color: white;
        padding: 4rem;
        background-color: rgba(139, 71, 181, 0.3);
        margin: 2rem;
        border-radius: 8px;
        height: 100%;
    }

    .loading {
        text-align: center;
        font-size: 2rem;
        color: white;
        padding: 4rem;
    }

    .pagination {
        display: flex;
        justify-content: center;
        gap: 0.5rem;
        padding: 2rem;
        align-items: center;
    }

    .page-btn {
        padding: 0.5rem 1rem;
        border: none;
        background: rgba(139, 71, 181, 0.2);
        color: white;
        border-radius: 6px;
        cursor: pointer;
        transition: all 0.3s ease;
    }

    .page-btn:disabled {
        opacity: 0.5;
        cursor: not-allowed;
    }

    .page-btn.active {
        background: rgba(139, 71, 181, 0.8);
    }

    .page-btn:hover:not(:disabled) {
        background: rgba(139, 71, 181, 0.4);
    }

    .ellipsis {
        color: white;
        padding: 0.5rem;
    }
</style>
