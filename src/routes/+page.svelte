<script>
    import { walletStore } from "@svelte-on-solana/wallet-adapter-core";

    let loading = false;
    let error = "";
    let nfts = [];
    let hasFetched = false;

    const getNfts = async () => {
        try {
            loading = true;

            const result = await fetch("/api/solana/search-assets", {
                method: "POST",
                body: JSON.stringify({
                    ownerAddress: $walletStore.publicKey?.toBase58(),
                }),
            });

            const { data } = await result.json();

            nfts = data.items;
        } catch (err) {
            error = String(err);

            console.error(err);
        } finally {
            hasFetched = true;
            loading = false;
        }
    };

    $: console.log({ nfts });
</script>

{#if $walletStore.connected}
    <div
        class="mb-4 flex items-center justify-between rounded-xl p-5 shadow-xl"
    >
        <div>
            <h3 class="text-xl font-bold">Connected!</h3>
            <p>{$walletStore.publicKey?.toBase58()}</p>
        </div>

        <div>
            <button
                class="btn-primary btn"
                class:loading
                on:click={getNfts}>Get NFTs</button
            >
        </div>
    </div>
{/if}

{#if loading}
    <div class="grid gap-3 md:grid-cols-3">
        {#each Array(9) as _}
            <div class="aspect-square animate-pulse rounded bg-gray-700" />
        {/each}
    </div>
{:else if !loading && hasFetched}
    <div class="grid gap-3 md:grid-cols-3">
        {#each nfts as nft}
            {@const image = nft.content.files.find(({ mime }) =>
                mime.startsWith("image")
            )}
            <div class="aspect-square rounded bg-gray-700">
                <img
                    class="rounded object-cover"
                    src={image.uri}
                    alt={nft.name}
                />
            </div>
        {/each}
    </div>
{:else if !loading && hasFetched && !nfts.length}
    <h2>No NFTs found! 😔</h2>
{:else if error}
    <h2>Something blew up! 💣</h2>
{/if}
