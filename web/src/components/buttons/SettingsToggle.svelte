<script
    lang="ts"
    generics="
        Context extends Exclude<keyof DecimatorSettings, 'schemaVersion'>,
        Id extends keyof DecimatorSettings[Context]
    "
>
    import settings, { updateSetting } from "$lib/state/settings";
    import type { DecimatorSettings } from "$lib/types/settings";

    import Toggle from "$components/misc/Toggle.svelte";

    export let settingId: Id;
    export let settingContext: Context;

    export let title: string;
    export let description: string = "";

    export let disabled = false;
    export let disabledOpacity = false;

    $: setting = $settings[settingContext][settingId];
    $: isEnabled = !!setting;
</script>

<div
    id="setting-toggle-{settingContext}-{String(settingId)}"
    class="toggle-parent"
    class:disabled
    class:faded={disabledOpacity}
    aria-hidden={disabled}
>
    <button
        class="toggle-container"
        role="switch"
        aria-checked={isEnabled}
        disabled={disabled}
        on:click={() =>
            updateSetting({
                [settingContext]: {
                    [settingId]: !isEnabled,
                }
            })
        }
    >
        <h4 class="toggle-title">{title}</h4>
        <Toggle enabled={isEnabled} />
    </button>

    {#if description}
        <div class="subtext toggle-description">{description}</div>
    {/if}
</div>

<style>
    .toggle-parent {
        display: flex;
        flex-direction: column;
        gap: 8px;
        overflow: hidden;
        transition: opacity 0.2s;
    }

    .toggle-parent.disabled {
        pointer-events: none;
    }

    .toggle-parent.faded {
        opacity: 0.5;
    }

    .toggle-container {
        width: 100%;
        display: flex;
        flex-direction: row;
        align-items: center;
        gap: var(--padding);
        justify-content: space-between;
        text-align: start;
        transform: none;
        padding: calc(var(--switcher-padding) * 2) 16px;
        border-radius: var(--border-radius);
        overflow: scroll;
    }
</style>
