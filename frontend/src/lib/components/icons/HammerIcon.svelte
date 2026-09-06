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
	<path d="m15 12-8.5 8.5c-.83.83-2.17.83-3 0 0 0 0 0 0 0a2.12 2.12 0 0 1 0-3L12 9"></path>
	<path d="M17.64 15 22 10.64"></path>
	<path d="m20.91 3.26-2.17-2.17a2.1 2.1 0 0 0-2.97 0L9.4 7.46l4.14 4.14 6.37-6.37a2.1 2.1 0 0 0 0-2.97z"></path>
</svg>
