<script lang="ts">
	interface Props {
		class?: string;
		size?: number | string;
	}
	let { class: className = 'w-4 h-4 inline-block', size = 16 }: Props = $props();
</script>

<svg
	xmlns="http://www.w3.org/2000/svg"
	width={size}
	height={size}
	viewBox="0 0 24 24"
	fill="none"
	stroke="currentColor"
	stroke-width="2"
	stroke-linecap="round"
	stroke-linejoin="round"
	class={className}
>
	<path d="M6 9H4.5a2.5 2.5 0 0 1 0-5H6"></path>
	<path d="M18 9h1.5a2.5 2.5 0 0 0 0-5H18"></path>
	<path d="M4 22h16"></path>
	<path d="M10 14.66V17c0 .55-.45 1-1 1H7v4h10v-4h-2c-.55 0-1-.45-1-1v-2.34"></path>
	<path d="M6 2v7a6 6 0 0 0 12 0V2H6z"></path>
</svg>
