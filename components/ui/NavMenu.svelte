<script>
    import { onMount } from 'svelte';
    import navConfig from './NavMenuConfig.json';
    export let data;
    let pages = [];

    onMount(() => {
        if (data && data.pagesManifest && data.pagesManifest.children) {
            let pagesData = data.pagesManifest.children;
            console.log('pages', pagesData);

            let sortedPages = pagesData
                .filter(page => navConfig.find(cfg => cfg.label === page.label))
                .map(page => ({...page, ...navConfig.find(cfg => cfg.label === page.label)}))
                .filter(page => page.show !== false) 
                .sort((a, b) => a.order - b.order);

            let nonConfigPages = pagesData.filter(page => !navConfig.find(cfg => cfg.label === page.label));
          
            pages = [...sortedPages, ...nonConfigPages];
        } else {
            console.error('No pages data available');
        }
    });

    function capitalizeFirstLetter(string) {
        if (!string) return string;
        return string.charAt(0).toUpperCase() + string.slice(1);
    }
</script>

{#if pages && pages.length > 0}
  
<a class="nav-menu" uk-toggle="target: #offcanvas-nav" href>
  <span uk-icon="icon: menu; ratio: 1"></span>
</a>

<div id="offcanvas-nav" uk-offcanvas="overlay: true; mode: push;">
  <div class="uk-offcanvas-bar">
      <h4>Menu</h4>
      <hr />
      <ul class="uk-nav uk-nav-default" uk-nav>
          {#each pages as item}
              {#if item.children.length === 0}
                  <li><a href="{item.href}/">{capitalizeFirstLetter(item.label)}</a></li>
              {/if}
              {#if item.children.length > 0}
                  <li class="uk-parent">
                      <a href>{capitalizeFirstLetter(item.label)} <span uk-nav-parent-icon></span></a>
                      <ul class="uk-nav-sub">
                          {#each item.children as child}
                              {#if child.label}
                                  <li><a href={child.href}>{capitalizeFirstLetter(child.label)}</a></li>
                              {/if}
                          {/each}
                      </ul>
                  </li>
              {/if}
          {/each}
      </ul>
      <button class="uk-offcanvas-close" type="button" uk-close></button>
  </div>
</div>

{/if}

<style>
.nav-menu {
  z-index:999 !important;
  position: absolute;
  top:14px;
  left:14px;
}
</style>