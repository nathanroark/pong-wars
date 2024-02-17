<script>
	import '../app.pcss';
	import TailwindIndicator from '@/components/tailwind-indicator.svelte';
	import SiteHeader from '@/components/site-header.svelte';
	import SiteFooter from '@/components/site-footer.svelte';
	import { dev } from '$app/environment';
	import { siteConfig } from '$lib/config/site';
</script>

<svelte:head>
	<title>{siteConfig.name}</title>
	<meta name="description" content={siteConfig.description} />
	<meta name="keywords" content={siteConfig.keywords} />
	<meta name="author" content="nathanroark" />
	<meta name="twitter:card" content="summary_large_image" />
	<meta name="twitter:site" content={siteConfig.url} />
	<meta name="twitter:title" content={siteConfig.name} />
	<meta name="twitter:description" content={siteConfig.description} />
	<meta name="twitter:image" content="https://pong-wars.nathanroark.dev/og.png" />
	<meta name="twitter:image:alt" content={siteConfig.name} />
	<meta name="twitter:creator" content="nathanroark" />
	<meta property="og:title" content={siteConfig.name} />
	<meta property="og:type" content="article" />
	<meta property="og:url" content={siteConfig.url} />
	<meta property="og:image" content="https://pong-wars.nathanroark.dev/og.png" />
	<meta property="og:image:alt" content={siteConfig.name} />
	<meta property="og:image:width" content="1200" />
	<meta property="og:image:height" content="630" />
	<meta property="og:description" content={siteConfig.description} />
	<meta property="og:site_name" content={siteConfig.name} />
	<meta property="og:locale" content="EN_US" />
	<link rel="shortcut icon" href="/favicon-16x16.png" />
	<link rel="apple-touch-icon" href="/apple-touch-icon.png" />
</svelte:head>
<div class="relative flex min-h-screen flex-col bg-background" id="page" data-vaul-drawer-wrapper>
	<SiteHeader />
	<div class="flex-1">
		<slot />
	</div>
	<!-- <SiteFooter /> -->
	{#if dev}
		<TailwindIndicator />
	{/if}
</div>
