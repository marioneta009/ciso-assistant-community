<script lang="ts">
	import { formFieldProxy } from 'sveltekit-superforms';

	let _class = '';

	export { _class as class };
	export let label: string | undefined = undefined;
	export let field: string;
	export let helpText: string | undefined = undefined;

	export let form;

	label = label ?? field;

	const { value, errors, constraints } = formFieldProxy(form, field);

	$: classesTextField = (errors: string[] | undefined) => (errors ? 'input-error' : '');
	$: classesDisabled = (disabled: boolean) => (disabled ? 'opacity-50' : '');
</script>

<div>
	<div class={classesDisabled($$props.disabled)}>
		{#if label !== undefined && !$$props.hidden}
			{#if $constraints?.required || $$props.required}
				<label class="text-sm font-semibold" for={field}
					>{label} <span class="text-red-500">*</span></label
				>
			{:else}
				<label class="text-sm font-semibold" for={field}>{label}</label>
			{/if}
		{/if}
		{#if $errors}
			<div>
				{#each $errors as error}
					<p class="text-error-500 text-xs font-medium">{error}</p>
				{/each}
			</div>
		{/if}
	</div>
	<div class="control">
		<input
			class="{'input ' + _class} {classesTextField($errors)}"
			data-testid="form-input-{field.replaceAll('_', '-')}"
			name={field}
			aria-invalid={$errors ? 'true' : undefined}
			placeholder=""
			bind:value={$value}
			{...$constraints}
			{...$$restProps}
		/>
	</div>
	{#if helpText}
		<p class="text-sm text-gray-500">{helpText}</p>
	{/if}
</div>
