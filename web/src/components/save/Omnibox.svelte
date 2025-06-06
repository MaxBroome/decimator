<script lang="ts">
    import { page } from "$app/stores";
    import { goto } from "$app/navigation";
    import { browser } from "$app/environment";
    import { SvelteComponent, tick } from "svelte";

    import { t } from "$lib/i18n/translations";

    import dialogs from "$lib/state/dialogs";
    import { link } from "$lib/state/omnibox";
    import { updateSetting } from "$lib/state/settings";
    import { turnstileEnabled, turnstileSolved } from "$lib/state/turnstile";

    import type { Optional } from "$lib/types/generic";
    import type { DownloadModeOption } from "$lib/types/settings";

    import ClearButton from "$components/save/buttons/ClearButton.svelte";
    import DownloadButton from "$components/save/buttons/DownloadButton.svelte";

    import Switcher from "$components/buttons/Switcher.svelte";
    import OmniboxIcon from "$components/save/OmniboxIcon.svelte";
    import SettingsButton from "$components/buttons/SettingsButton.svelte";

    import IconMute from "$components/icons/Mute.svelte";
    import IconMusic from "$components/icons/Music.svelte";
    import IconSparkles from "$components/icons/Sparkles.svelte";

    let linkInput: Optional<HTMLInputElement>;
    let downloadButton: SvelteComponent;

    let isFocused = false;

    let isDisabled = false;
    let isLoading = false;

    $: isBotCheckOngoing = $turnstileEnabled && !$turnstileSolved;

    const validLink = (url: string) => {
        try {
            return /^https?\:/i.test(new URL(url).protocol);
        } catch {}
    };

    $: linkFromHash = $page.url.hash.replace("#", "") || "";
    $: linkFromQuery = (browser ? $page.url.searchParams.get("u") : 0) || "";

    $: if (linkFromHash || linkFromQuery) {
        if (validLink(linkFromHash)) {
            $link = linkFromHash;
        } else if (validLink(linkFromQuery)) {
            $link = linkFromQuery;
        }

        // clear hash and query to prevent bookmarking unwanted links
        goto("/", { replaceState: true });
    }

    const changeDownloadMode = (mode: DownloadModeOption) => {
        updateSetting({ save: { downloadMode: mode } });
    };

    const handleKeydown = (e: KeyboardEvent) => {
        if (!linkInput || $dialogs.length > 0 || isDisabled || isLoading) {
            return;
        }

        if (e.metaKey || e.ctrlKey || e.key === "/") {
            linkInput.focus();
        }

        if (e.key === "Enter" && validLink($link) && isFocused) {
            downloadButton.download($link);
        }

        if (["Escape", "Clear"].includes(e.key) && isFocused) {
            $link = "";
        }

        if (e.target === linkInput) {
            return;
        }

        switch (e.key) {
            case "J":
                changeDownloadMode("auto");
                break;
            case "K":
                changeDownloadMode("audio");
                break;
            case "L":
                changeDownloadMode("mute");
                break;
            default:
                break;
        }
    };
</script>

<svelte:window on:keydown={handleKeydown} />

    <div id="instance-label">
        decimator - download anything
    </div>

<div id="omnibox">
    <div
        id="input-container"
        class:focused={isFocused}
        class:downloadable={validLink($link)}
    >
        <OmniboxIcon loading={isLoading || isBotCheckOngoing} />
        <input
            id="link-area"
            bind:value={$link}
            bind:this={linkInput}
            on:input={() => (isFocused = true)}
            on:focus={() => (isFocused = true)}
            on:blur={() => (isFocused = false)}
            spellcheck="false"
            autocomplete="off"
            autocapitalize="off"
            maxlength="512"
            placeholder={$t("save.input.placeholder")}
            aria-label={isBotCheckOngoing
                ? $t("a11y.save.link_area.turnstile")
                : $t("a11y.save.link_area")}
            data-form-type="other"
            disabled={isDisabled}
        />

        {#if $link && !isLoading}
            <ClearButton click={() => ($link = "")} />
        {/if}
        {#if validLink($link)}
            <DownloadButton
                url={$link}
                bind:this={downloadButton}
                bind:disabled={isDisabled}
                bind:loading={isLoading}
            />
        {/if}
    </div>

    <div id="action-container">
        <Switcher>
            <SettingsButton
                settingContext="save"
                settingId="downloadMode"
                settingValue="auto"
            >
                <IconSparkles />
                {$t("save.auto")}
            </SettingsButton>
            <SettingsButton
                settingContext="save"
                settingId="downloadMode"
                settingValue="audio"
            >
                <IconMusic />
                {$t("save.audio")}
            </SettingsButton>
            <SettingsButton
                settingContext="save"
                settingId="downloadMode"
                settingValue="mute"
            >
                <IconMute />
                {$t("save.mute")}
            </SettingsButton>
        </Switcher>
    </div>
</div>

<style>
    #omnibox {
        display: flex;
        flex-direction: column;
        max-width: 640px;
        width: 100%;
        gap: 8px;
    }

    #input-container {
        --input-padding: 10px;
        display: flex;
        box-shadow: 0 0 0 1.5px var(--input-border) inset;
        border-radius: var(--border-radius);
        padding: 0 var(--input-padding);
        align-items: center;
        gap: var(--input-padding);
        font-size: 14px;
        flex: 1;
    }

    #input-container.downloadable {
        padding-right: 0;
    }

    #input-container.downloadable:dir(rtl) {
        padding-right: var(--input-padding);
        padding-left: 0;
    }

    #input-container.focused {
        box-shadow: 0 0 0 1.5px var(--secondary) inset;
        outline: var(--secondary) 0.5px solid;
    }

    #input-container.focused :global(#input-icons svg) {
        stroke: var(--secondary);
    }

    #input-container.downloadable :global(#input-icons svg) {
        stroke: var(--secondary);
    }

    #link-area {
        display: flex;
        width: 100%;
        margin: 0;
        padding: var(--input-padding) 0;
        height: 18px;

        align-items: center;

        border: none;
        outline: none;
        background-color: transparent;
        color: var(--secondary);

        -webkit-tap-highlight-color: transparent;
        flex: 1;

        font-weight: 500;

        /* workaround for safari */
        font-size: inherit;
    }

    #link-area:focus-visible {
        box-shadow: unset !important;
    }

    #link-area::placeholder {
        color: var(--gray);
        /* fix for firefox */
        opacity: 1;
    }

    /* fix for safari */
    input:disabled {
        opacity: 1;
    }

    #action-container {
        display: flex;
        flex-direction: row;
    }

    #action-container {
        justify-content: space-between;
    }


    #instance-label {
        font-size: 13px;
        color: var(--gray);
        font-weight: 500;
    }

    @media screen and (max-width: 440px) {
        #action-container {
            flex-direction: column;
            gap: 5px;
        }

        #action-container :global(.button) {
            width: 100%;
        }
    }
</style>
