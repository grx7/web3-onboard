<script lang="ts" context="module">
  let scrollContainer: HTMLElement

  export function modalAutoScroll(el: HTMLElement): void {
    const { scrollHeight, clientHeight } = scrollContainer || {}

    if (scrollHeight && scrollHeight > clientHeight) {
      el.scrollIntoView({
        behavior: 'smooth',
        block: 'start'
      })
    }
  }
</script>

<script lang="ts">
  import { fade } from 'svelte/transition'
  import { onDestroy, onMount } from 'svelte'

  const body = document.body
  onMount(() => {
    window.addEventListener(
      'scroll',
      () => {
        document.documentElement.style.setProperty(
          '--scroll-y',
          `${window.scrollY}px`
        )
      },
      { passive: true }
    )
    const scrollY =
      document.documentElement.style.getPropertyValue('--scroll-y')
    body.style.position = 'fixed'
    body.style.top = `-${scrollY}`
  })

  onDestroy(() => {
    const scrollY = body.style.top
    body.style.position = ''
    body.style.top = ''
    window.scrollTo(0, parseInt(scrollY || '0') * -1)
  })
  export let close: () => void
</script>

<style>
  section {
    top: 0;
    left: 0;
    pointer-events: none;
    z-index: var(--onboard-modal-z-index, var(--modal-z-index));
  }

  .background {
    width: 100vw;
    height: 100vh;
    background: var(--onboard-modal-backdrop, var(--modal-backdrop));
    pointer-events: all;
  }

  .max-height {
    max-height: calc(100vh - 2rem);
  }

  .modal-position {
    top: var(--onboard-modal-top, var(--modal-top));
    bottom: var(--onboard-modal-bottom, var(--modal-bottom));
    left: var(--onboard-modal-left, var(--modal-left));
    right: var(--onboard-modal-right, var(--modal-right));
  }

  .modal-overflow {
    overflow: hidden;
  }

  .modal-styling {
    border-radius: var(--onboard-modal-border-radius, var(--border-radius-1));
    box-shadow: var(--onboard-modal-box-shadow, var(--box-shadow-0));
  }

  .modal {
    border-radius: var(--onboard-modal-border-radius, var(--border-radius-1));
    overflow-y: auto;
    background: white;
  }

  @media all and (max-width: 520px) {
    .relative {
      width: 100vw;
    }

    .modal-overflow {
      width: 100%;
    }

    .modal {
      width: 100%;
    }
  }
</style>

<section class="fixed" transition:fade>
  <div
    on:click={close}
    class="background flex items-center justify-center relative"
  >
    <div class="flex modal-position absolute">
      <div on:click|stopPropagation class="flex relative max-height">
        <div class="modal-overflow modal-styling relative flex justify-center">
          <div class="modal relative">
            <slot />
          </div>
        </div>
      </div>
    </div>
  </div>
</section>
