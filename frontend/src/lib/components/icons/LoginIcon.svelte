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
	<path d="M21 2l-2 2m-1.5 1.5L14 9l-1.5-1.5L11 9l-1.5-1.5L8 9 4.5 5.5a5.5 5.5 0 1 0 7.78 7.78L21 4.5V2h-2z"></path>
</svg>
