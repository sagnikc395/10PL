<script lang="ts">
	import { onMount } from 'svelte';

	// State for the markdown content
	let markdownContent: string = '';
	let parsedSections: { title: string; content: string; isOpen: boolean }[] = [];

	// Function to parse markdown into sections
	function parseMarkdown(markdown: string) {
		// Split the markdown by h2 headers (##)
		const sections = markdown.split(/^## /gm).filter(Boolean);

		return sections.map((section) => {
			const lines = section.trim().split('\n');
			const title = lines[0].trim();
			const content = lines.slice(1).join('\n').trim();

			return {
				title,
				content,
				isOpen: false
			};
		});
	}

	// Toggle section visibility
	function toggleSection(index: number) {
		parsedSections[index].isOpen = !parsedSections[index].isOpen;
		parsedSections = [...parsedSections]; // Trigger reactivity
	}

	// Load markdown content on mount
	onMount(async () => {
		try {
			// In a real app, you would load this from a file
			// For this example, we'll use a hardcoded markdown string
			// In a real SvelteKit app, you would use:
			// const response = await fetch('/content.md');
			// markdownContent = await response.text();

			markdownContent = `## Getting Started
        
  Welcome to 10pl! This section covers how to get started with our platform.
  
  - Install dependencies
  - Configure your environment
  - Run the application
  
  ## Features
  
  Our platform offers several key features:
  
  1. Fast rendering
  2. Type safety
  3. Great developer experience
  4. Excellent performance
  
  ## Documentation
  
  Check out our documentation for more details on:
  
  - API Reference
  - Component Library
  - Best Practices
  - Troubleshooting
  `;

			parsedSections = parseMarkdown(markdownContent);
		} catch (error) {
			console.error('Failed to load markdown:', error);
		}
	});
</script>

<svelte:head>
	<title>10pl</title>
</svelte:head>

<div class="flex min-h-screen flex-col">
	<!-- Header -->
	<header class="bg-slate-800 p-4 text-white shadow-md">
		<div class="container mx-auto">
			<h1 class="text-2xl font-bold">10pl</h1>
		</div>
	</header>

	<!-- Main content -->
	<main class="container mx-auto flex-grow p-4 md:p-6">
		<div class="mx-auto max-w-3xl rounded-lg bg-white p-6 shadow-md">
			<h2 class="mb-6 text-xl font-semibold">Documentation</h2>

			{#if parsedSections.length === 0}
				<div class="flex animate-pulse space-x-4">
					<div class="flex-1 space-y-4 py-1">
						<div class="h-4 w-3/4 rounded bg-slate-200"></div>
						<div class="space-y-2">
							<div class="h-4 rounded bg-slate-200"></div>
							<div class="h-4 w-5/6 rounded bg-slate-200"></div>
						</div>
					</div>
				</div>
			{:else}
				<div class="space-y-4">
					{#each parsedSections as section, index}
						<div class="overflow-hidden rounded-md border border-slate-200">
							<button
								class="flex w-full items-center justify-between bg-slate-50 p-4 text-left hover:bg-slate-100"
								on:click={() => toggleSection(index)}
							>
								<span class="font-medium">{section.title}</span>
								<svg
									class="h-5 w-5 transition-transform duration-200 {section.isOpen
										? 'rotate-180 transform'
										: ''}"
									fill="none"
									stroke="currentColor"
									viewBox="0 0 24 24"
									xmlns="http://www.w3.org/2000/svg"
								>
									<path
										stroke-linecap="round"
										stroke-linejoin="round"
										stroke-width="2"
										d="M19 9l-7 7-7-7"
									></path>
								</svg>
							</button>

							{#if section.isOpen}
								<div class="prose prose-slate max-w-none p-4">
									{#each section.content.split('\n') as line}
										<p>{line}</p>
									{/each}
								</div>
							{/if}
						</div>
					{/each}
				</div>
			{/if}
		</div>
	</main>

	<!-- Footer -->
	<footer class="mt-8 bg-slate-800 p-4 text-white">
		<div class="container mx-auto flex flex-col items-center justify-between md:flex-row">
			<div class="mb-4 md:mb-0">
				<p class="text-sm">© {new Date().getFullYear()} 10pl. All rights reserved.</p>
			</div>

			<div class="flex space-x-4">
				<a
					href="https://twitter.com"
					target="_blank"
					rel="noopener noreferrer"
					class="text-white transition-colors hover:text-blue-400"
				>
					<span class="sr-only">Twitter</span>
					<svg class="h-6 w-6" fill="currentColor" viewBox="0 0 24 24" aria-hidden="true">
						<path
							d="M8.29 20.251c7.547 0 11.675-6.253 11.675-11.675 0-.178 0-.355-.012-.53A8.348 8.348 0 0022 5.92a8.19 8.19 0 01-2.357.646 4.118 4.118 0 001.804-2.27 8.224 8.224 0 01-2.605.996 4.107 4.107 0 00-6.993 3.743 11.65 11.65 0 01-8.457-4.287 4.106 4.106 0 001.27 5.477A4.072 4.072 0 012.8 9.713v.052a4.105 4.105 0 003.292 4.022 4.095 4.095 0 01-1.853.07 4.108 4.108 0 003.834 2.85A8.233 8.233 0 012 18.407a11.616 11.616 0 006.29 1.84"
						></path>
					</svg>
				</a>

				<a
					href="https://github.com"
					target="_blank"
					rel="noopener noreferrer"
					class="text-white transition-colors hover:text-gray-400"
				>
					<span class="sr-only">GitHub</span>
					<svg class="h-6 w-6" fill="currentColor" viewBox="0 0 24 24" aria-hidden="true">
						<path
							fill-rule="evenodd"
							d="M12 2C6.477 2 2 6.484 2 12.017c0 4.425 2.865 8.18 6.839 9.504.5.092.682-.217.682-.483 0-.237-.008-.868-.013-1.703-2.782.605-3.369-1.343-3.369-1.343-.454-1.158-1.11-1.466-1.11-1.466-.908-.62.069-.608.069-.608 1.003.07 1.531 1.032 1.531 1.032.892 1.53 2.341 1.088 2.91.832.092-.647.35-1.088.636-1.338-2.22-.253-4.555-1.113-4.555-4.951 0-1.093.39-1.988 1.029-2.688-.103-.253-.446-1.272.098-2.65 0 0 .84-.27 2.75 1.026A9.564 9.564 0 0112 6.844c.85.004 1.705.115 2.504.337 1.909-1.296 2.747-1.027 2.747-1.027.546 1.379.202 2.398.1 2.651.64.7 1.028 1.595 1.028 2.688 0 3.848-2.339 4.695-4.566 4.943.359.309.678.92.678 1.855 0 1.338-.012 2.419-.012 2.747 0 .268.18.58.688.482A10.019 10.019 0 0022 12.017C22 6.484 17.522 2 12 2z"
							clip-rule="evenodd"
						></path>
					</svg>
				</a>
			</div>
		</div>
	</footer>
</div>

<style>
	/* Add any additional styles here */
	:global(body) {
		background-color: #f8fafc;
	}

	/* Basic styling for markdown content */
	.prose ul {
		list-style-type: disc;
		padding-left: 1.5rem;
		margin-top: 0.5rem;
		margin-bottom: 0.5rem;
	}

	.prose ol {
		list-style-type: decimal;
		padding-left: 1.5rem;
		margin-top: 0.5rem;
		margin-bottom: 0.5rem;
	}
</style>
