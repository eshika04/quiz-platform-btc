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
	<line x1="18" y1="6" x2="6" y2="18"></line>
	<line x1="6" y1="6" x2="18" y2="18"></line>
</svg>
