<script lang="ts">
	interface Props {
		class?: string;
		size?: number | string;
	}
	let { class: className = 'w-6 h-6 inline-block', size = 24 }: Props = $props();
</script>

<svg
	xmlns="http://www.w3.org/2000/svg"
	width={size}
	height={size}
	viewBox="0 0 24 24"
	fill="currentColor"
	stroke="none"
	class={className}
>
	<rect x="3" y="3" width="18" height="18" rx="2"></rect>
</svg>
