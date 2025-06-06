<script lang="ts">
    import { onMount } from "svelte";

    import DecimatorSticker from "$components/icons/DecimatorSticker.svelte";

    // please add a source link (https://github.com/imputnet/decimator) if you use this implementation
    // i spent 4 hours switching between simulators and devices to get the best way to do this

    $: safeAreaTop = 0;
    $: safeAreaBottom = 0;
    $: state = "hidden"; // "notch", "island", "notch x"

    const islandValues = [
        53, // 16 pro max: larger text
        59, // regular & plus: default
        48, // regular: larger text
        49, // 16: larger text
        51, // plus only: larger text
        62, // 16: regular
    ];

    const xNotch = [44];

    const getSafeAreaTop = () => {
        const root = document.documentElement;
        return getComputedStyle(root)
            .getPropertyValue("--safe-area-inset-top")
            .trim();
    };

    const getSafeAreaBottom = () => {
        const root = document.documentElement;
        return getComputedStyle(root)
            .getPropertyValue("--safe-area-inset-bottom")
            .trim();
    };

    onMount(() => {
        safeAreaTop = Number(getSafeAreaTop().replace("px", ""));
        safeAreaBottom = Number(getSafeAreaBottom().replace("px", ""));
    });

    $: if (safeAreaTop > 20) {
        state = "notch";
        if (islandValues.includes(safeAreaTop)) {
            state = "island";
        }
        if (xNotch.includes(safeAreaTop)) {
            state = "notch x";
        }
        // exception for XR and 11 at regular screen zoom
        if (safeAreaTop === 48 && safeAreaBottom === 34) {
            state = "notch";
        }

        // exception for iPhone 16 Pro Max
        if (safeAreaTop === 53 && safeAreaBottom === 29) {
            state = "notch sixteen-pro-max";
        }
    }
</script>

{#if state !== "hidden"}
    <div id="decimator-notch-sticker" aria-hidden="true" class={state}>
        <DecimatorSticker />
    </div>
{/if}

<style>
    #decimator-notch-sticker {
        display: flex;
        justify-content: center;
        align-items: center;
        position: absolute;
        top: 0;
        width: 100%;
        z-index: 999;
    }

    #decimator-notch-sticker.island {
        padding-top: 15px;
    }

    #decimator-notch-sticker.notch {
        padding-top: 2px;
    }

    #decimator-notch-sticker.sixteen-pro-max {
        padding-top: 12px;
    }

    #decimator-notch-sticker.notch.x :global(svg) {
        height: 28px;
    }

    #decimator-notch-sticker :global(svg) {
        width: 100px;
        height: 30px;
    }

    /* regular iphone size, larger text display mode */
    @media screen and (max-width: 350px) {
        #decimator-notch-sticker.notch :global(svg) {
            height: 24px;
        }

        #decimator-notch-sticker.island {
            padding-top: 9px;
        }
    }

    /* regular & plus iphone size, dynamic island, larger text display mode */
    @media screen and (max-width: 375px) {
        #decimator-notch-sticker.island :global(svg) {
            height: 26px;
        }

        #decimator-notch-sticker.island {
            padding-top: 11px;
        }
    }

    @media screen and (orientation: landscape) {
        #decimator-notch-sticker {
            display: none;
        }
    }
</style>
