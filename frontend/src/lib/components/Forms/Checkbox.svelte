<script lang="ts">
	import type { ComponentProps } from 'svelte';
	import type { Writable } from 'svelte/store';
	import { formFieldProxy } from 'sveltekit-superforms';
	export let label: string | undefined = undefined;
	export let field: string;
	export let helpText: string | undefined = undefined;

	label = label ?? field;

	export let form;

	const { value, errors, constraints } = formFieldProxy(form, field);

	$: boolValue = value as Writable<boolean>;

	$: classesHidden = (hidden: boolean) => (hidden ? 'hidden' : '');
	$: classesDisabled = (disabled: boolean) => (disabled ? 'opacity-50' : '');
</script>

<div>
	<div
		class="flex flex-row space-x-2 items-center {classesHidden($$props.hidden)} {classesDisabled(
			$$props.disabled
		)}"
	>
		{#if label !== undefined}
			{#if $constraints?.required}
				<label class="text-sm font-semibold" for={field}
					>{label} <span class="text-red-500">*</span></label
				>
			{:else}
				<label class="text-sm font-semibold" for={field}>{label}</label>
			{/if}
		{/if}
		<div class="control">
			<input
				name={field}
				type="checkbox"
				class="checkbox"
				data-testid="form-input-{field.replaceAll('_', '-')}"
				bind:checked={$boolValue}
				{...$constraints}
				{...$$restProps}
				disabled={$$props.disabled}
			/>
		</div>
	</div>
	{#if $errors}
		<div>
			{#each $errors as error}
				<p class="text-error-500 text-xs font-medium">{error}</p>
			{/each}
		</div>
	{/if}
	{#if helpText}
		<p class="text-sm text-gray-500">{helpText}</p>
	{/if}
</div>
