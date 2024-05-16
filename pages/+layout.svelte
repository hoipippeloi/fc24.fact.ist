<script>
	import '@evidence-dev/tailwind/fonts.css';
	import '../app.css';
	import { EvidenceDefaultLayout } from '@evidence-dev/core-components';
	export let data;

	import "$lib/uikit-3.20.10/css/uikit.min.css";
	import "$lib/uikit-3.20.10/js/uikit.min.js";
	import "$lib/uikit-3.20.10/js/uikit-icons.min.js";
	import "$lib/css/style.css";

	import NavMenu from "$lib/ui/NavMenu.svelte";

</script>

<svelte:head>
	<title>fc24.fact.ist</title>
</svelte:head>

<EvidenceDefaultLayout {data}
	logo="/img/icon.png"
	title='My App'
  	neverShowQueries=true
  	githubRepo='https://github.com/hoipippeloi/tpl.fact.ist'
  	hideSidebar=true
  	fullWidth=true
  	maxWidth=1440
	hideTOC=true
	>

	<NavMenu {data} />
	<slot slot="content" />

</EvidenceDefaultLayout>
