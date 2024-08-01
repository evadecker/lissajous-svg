<script>
import "$lib/styles/reset.css";
import "$lib/styles/theme.css";
import "$lib/styles/base.css";
</script>

<main class="main">
  <slot />
</main>

<style>
  .main {
    flex: 1;
    width: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }
</style>
