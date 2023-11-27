<script>
  import { getContext, setContext } from "svelte"
  import { writable } from "svelte/store"

  export let direction
  export let size
  export let quiet
  export let iconOnly
  export let compact

  export let buttonType
  export let buttons

  const { styleable, Block, BlockComponent } = getContext("sdk")
  const component = getContext("component")
  const buttonGroupStore = writable({ "size" : size, "quiet": quiet, "iconOnly" : iconOnly });

  $: $buttonGroupStore.size = size
  $: $buttonGroupStore.quiet = quiet
  $: $buttonGroupStore.iconOnly = iconOnly

  const getButtonProps = ( btn ) => {
    return {
      size: "M",
      text: btn.text
    }
  }

  setContext( "super-action-button-group", buttonGroupStore )

  $: console.log(buttons)
</script>


<Block>
  <div 
  class="spectrum-ActionGroup spectrum-ActionGroup--size{size}" 
  class:spectrum-ActionGroup--vertical={direction=="column"}
  class:spectrum-ActionGroup--quiet={quiet}
  class:spectrum-ActionGroup--compact={compact}
  use:styleable={$component.styles}
  >
    {#each buttons as button, idx }
      <BlockComponent
        type={  buttonType == "action" ? "plugin/bb-coomponent-SuperActionButton" : "button"}
        props={getButtonProps(button)}
        order={idx}
        interactive
        name="SuperButton"
      />
    {/each}
    <slot />
  </div>
</Block>

<style>
  .spectrum-ActionGroup {
    margin-top: 0px !important;
    gap: var(--spectrum-global-dimension-size-100);
  }

  .spectrum-ActionGroup--compact{
    gap: 0px;
  }
</style>