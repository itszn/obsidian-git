<script lang="ts">
  import { setIcon, Workspace } from "obsidian";
  import { hoverPreview, openOrSwitch } from "obsidian-community-lib";
  import { FileStatusResult } from "src/types";
  import GitView from "../sidebarView";

  export let change: FileStatusResult;
  export let view: GitView;
  $: side = (view.leaf.getRoot() as any).side == "left" ? "right" : "left";

  function hover(event: MouseEvent) {
    //Don't show previews of config- or hidden files.
    if (
      !change.path.startsWith(view.app.vault.configDir) ||
      !change.path.startsWith(".")
    ) {
      hoverPreview(
        event,
        view as any,
        change.vault_path.split("/").last().replace(".md", "")
      );
    }
  }

  function open(event: MouseEvent) {
    if (
      !(
        change.path.startsWith(view.app.vault.configDir) ||
        change.path.startsWith(".") ||
        change.working_dir === "D"
      )
    ) {
      openOrSwitch(change.vault_path, event);
    }
  }
</script>

<main on:mouseover={hover} on:click|self={open} on:focus>
  <span
    class="path"
    aria-label-position={side}
    aria-label={change.vault_path.split("/").last() != change.vault_path
      ? change.vault_path
      : ""}
    on:click|self={open}
  >
    {change.vault_path.split("/").last().replace(".md", "")}
  </span>
  <div class="tools">
    <span class="type" data-type={change.working_dir}>{change.working_dir}</span
    >
  </div>
</main>

<style lang="scss">
  main {
    cursor: pointer;
    background-color: var(--background-secondary);
    border-radius: 4px;
    width: 98%;
    display: flex;
    justify-content: space-between;
    font-size: 0.8rem;
    margin-bottom: 2px;

    .path {
      color: var(--text-muted);
      white-space: nowrap;
      max-width: 75%;
      overflow: hidden;
      text-overflow: ellipsis;
    }

    &:hover .path {
      color: var(--text-normal);
      transition: all 200ms;
    }

    .tools {
      display: flex;
      align-items: center;

      .type {
        height: 16px;
        width: 16px;
        margin: 0;
        display: flex;
        align-items: center;
        justify-content: center;
        &[data-type="M"] {
          color: orange;
        }
        &[data-type="D"] {
          color: red;
        }
      }
    }
  }
</style>
