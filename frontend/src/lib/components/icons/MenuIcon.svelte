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
	<line x1="3" y1="12" x2="21" y2="12"></line>
	<line x1="3" y1="6" x2="21" y2="6"></line>
	<line x1="3" y1="18" x2="21" y2="18"></line>
</svg>
