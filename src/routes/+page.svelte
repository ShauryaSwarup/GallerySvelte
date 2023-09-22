<script lang="ts">
    import axios from "axios";
    import { afterUpdate, beforeUpdate, onDestroy, onMount } from "svelte";
    import { fade, fly } from "svelte/transition";
    let term = "";
    let photos: {
        id: string;
        urls: {
            regular: string;
        };
        alt_description: string;
    }[] = [];
    onMount(async () => {
        await fetchData();
    });
    onDestroy(() => {
        console.log("onDestroy");
    });
    beforeUpdate(() => {
        console.log("beforeUpdate");
    });
    afterUpdate(() => {
        console.log("afterUpdate");
    });
    const fetchData = async () => {
        const response = await axios.get(
            `https://api.unsplash.com/search/photos?page=1&query=${
                term || "office"
            }&client_id=roGMBYMoHZVMpv1inBVkIKi1-ooomS4VfoqOnYFmSGk`
        );
        photos = response.data.results;
    };
    const handleSearch = async () => {
        if (!term) return;
        await fetchData();
        term = "";
    };
    console.log({ term, photos });
</script>

<div class="container">
    <div class="header">
        <h1>Image Gallery</h1>  
        <input type="text" bind:value={term} /> <!-- bind:value is two way binding -->
        <button class="button" on:click={() => handleSearch()}> Search </button>
    </div>
    <div class="photos">
        {#each photos as photo, i(photo.id)}
            <img
                src={photo.urls.regular}
                alt={photo.alt_description}
                class="image"
                in:fly={{ y: 200, duration: 1000, delay: i*200 }}
                out:fade={{ duration: 500 }}
            />
        {/each}
    </div>
</div>

<style>
    .image {
        width: 400px;
        height: 250px;
        margin: 5px;
    }
    .photos {
        display: flex;
        flex-wrap: wrap;
    }
    .container {
        width: 1230px;
        margin: 0 auto;
    }
    .header {
        text-align: center;
        font-size: 20px;
    }
    .input {
        padding: 10px;
        width: 400px;
        border-radius: 10px;
        outline: none;
        border: 1px solid gray;
        font-size: 20px;
    }
    .button {
        padding: 10px;
        font-size: 20px;
        background-color: aqua;
        border-radius: 10px;
        border: none;
        color: white;
    }
    .input-container {
        margin-bottom: 40px;
    }
</style>
