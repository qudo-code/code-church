<style global>
    @import "../app.postcss";
</style>

<script>
    import Wallet from "$lib/components/wallet.svelte";
</script>

<nav
    class="sticky top-0 left-0 flex w-full items-center justify-between bg-black p-3"
>
    <h1 class="text-xl">The 76</h1>
    <div class="flex items-center">
        <Wallet />
    </div>
</nav>
<main class="mx-auto max-w-3xl py-10">
    <slot />
</main>
