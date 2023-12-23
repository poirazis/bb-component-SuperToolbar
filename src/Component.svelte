<script>
  import { getContext, setContext } from "svelte";
  import { writable } from "svelte/store";

  export let direction = "row";
  export let size;
  export let quiet;
  export let iconOnly;
  export let compact;

  export let buttonType;
  export let buttons;

  export let preset;

  let appliedPreset;

  const { styleable, Block, BlockComponent, componentStore, builderStore } =
    getContext("sdk");
  const component = getContext("component");
  const buttonGroupStore = writable({
    size: size,
    quiet: quiet,
    iconOnly: iconOnly,
  });

  const presets = {
    form: [
      {
        type: "cta",
        text: "Cancel",
      },
      {
        type: "primary",
        text: "Save",
      },
    ],
    pagination: [
      {
        type: "cta",
        text: "Copy",
      },
      {
        type: "primary",
        text: "Paste",
      },
      {
        type: "primary",
        text: "Duplicate",
      },
    ],
  };

  $: $buttonGroupStore.size = size;
  $: $buttonGroupStore.quiet = quiet;
  $: $buttonGroupStore.iconOnly = iconOnly;

  $: if (
    $builderStore.inBuilder &&
    preset != "custom" &&
    appliedPreset != preset &&
    $componentStore.selectedComponentPath?.includes($component.id)
  ) {
    builderStore.actions.updateProp("buttons", presets[preset]);
    appliedPreset = preset;
  }

  const getButtonProps = (btn) => {
    return {
      size: "M",
      text: btn.text,
      type: btn.type,
      onClick: btn.onClick
    };
  };

  setContext("super-action-button-group", buttonGroupStore);
</script>

<Block>
  <div
    class="spectrum-ActionGroup spectrum-ActionGroup--size{size}"
    class:spectrum-ActionGroup--vertical={direction == "column"}
    class:spectrum-ActionGroup--quiet={quiet}
    class:spectrum-ActionGroup--compact={compact}
    use:styleable={$component.styles}
  >
  {#if buttons?.length}
    {#each buttons as button, idx}
      <BlockComponent
        type={buttonType == "action"
          ? "plugin/bb-component-SuperButton"
          : "button"}
        props={getButtonProps(button)}
        order={idx}
        interactive
        name="SuperButton"
      />
    {/each}
    {/if}
    <slot />
  </div>
</Block>

<style>
  .spectrum-ActionGroup {
    margin-top: 0px !important;
    gap: var(--spectrum-global-dimension-size-100);
  }

  .spectrum-ActionGroup--compact {
    gap: 0px;
  }
</style>
