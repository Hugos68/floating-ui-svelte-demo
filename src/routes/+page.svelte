<script lang="ts">
	import { fade } from 'svelte/transition';
	import {
		autoUpdate,
		offset,
		shift,
		flip,
		arrow,
		useFloating,
		FloatingArrow,
		useHover,
		useInteractions,
		useRole,
		useDismiss
	} from '@skeletonlabs/floating-ui-svelte';

	// State
	let open = $state(false);
	let arrowEl: HTMLElement | null = $state(null);

	// Use Floating
	const floating = useFloating({
		whileElementsMounted: autoUpdate,
		get open() {
			return open;
		},
		onOpenChange: (v) => (open = v),
		placement: 'top',
		get middleware() {
			return [offset(10), shift({ padding: 16 }), flip(), arrowEl && arrow({ element: arrowEl })];
		}
	});

	// Interactions
	const role = useRole(floating.context, { role: 'tooltip' });
	const hover = useHover(floating.context, { move: false });
	const dismiss = useDismiss(floating.context);
	const interactions = useInteractions([role, hover, dismiss]);
</script>

<main class="space-y-10">
	<header class="space-y-4">
		<h1>Floating UI Svelte</h1>
	</header>
	<section>
		<!-- Reference Element -->
		<button
			bind:this={floating.elements.reference}
			{...interactions.getReferenceProps()}
			class="btn"
		>
			Hover Me
		</button>
		<!-- Floating Element -->
		{#if open}
			<div
				bind:this={floating.elements.floating}
				style={floating.floatingStyles}
				{...interactions.getFloatingProps()}
				class="floating popover"
				transition:fade={{ duration: 200 }}
			>
				<p>
					A <strong>floating element</strong> is one that floats on top of the UI without disrupting
					the flow, like this one!
				</p>
				<FloatingArrow bind:ref={arrowEl} context={floating.context} class="fill-slate-400" />
			</div>
		{/if}
	</section>
</main>
