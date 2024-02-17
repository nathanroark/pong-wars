<script lang="ts">
	import { Icons } from '@/components/icons';
	import { cn } from '$lib/utils';
	import { buttonVariants } from '$lib/components/ui/button';
	import { siteConfig } from '$lib/config/site';
</script>

<header
	class="sticky top-0 z-50 w-full border-b border-border/40 bg-background/95 backdrop-blur supports-[backdrop-filter]:bg-background/60"
>
	<div class="container flex h-14 max-w-screen-2xl items-center">
		<div class="mr-4">
			<a href="/" class="mr-6 flex items-center space-x-2">
				<Icons.logo class="h-6 w-6" />
				<span class="inline-block font-bold">
					{siteConfig.name}
				</span>
			</a>
		</div>
		<div class="flex flex-1 items-center justify-end space-x-2">
			<nav class="flex items-center">
				<a href={siteConfig.links.cv} target="_blank" rel="noreferrer">
					<div
						class={cn(
							buttonVariants({
								size: 'sm',
								variant: 'ghost'
							}),
							'w-9 px-0'
						)}
					>
						<Icons.home class="h-6 w-6" />
						<span class="sr-only">Home Site</span>
					</div>
				</a>
				<a href={siteConfig.links.github} target="_blank" rel="noopener noreferrer">
					<div
						class={cn(
							buttonVariants({
								size: 'sm',
								variant: 'ghost'
							}),
							'w-9 px-0'
						)}
					>
						<Icons.gitHub class="h-6 w-6" />
						<span class="sr-only">GitHub</span>
					</div>
				</a>
			</nav>
		</div>
	</div>
</header>
