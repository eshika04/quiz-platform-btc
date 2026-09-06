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
	<path d="M4 19.5A2.5 2.5 0 0 1 6.5 17H20"></path>
	<path d="M6.5 2H20v20H6.5A2.5 2.5 0 0 1 4 19.5v-15A2.5 2.5 0 0 1 6.5 2z"></path>
</svg>
