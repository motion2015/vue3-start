<template>
<div class="form-group">
<label>Email address</label>
<input :value="modelValue" @input="onInput" type="email" class="form-control" />
</div>
</template>
<script>
export default {
	props: {
		modelValue: {
			type: String,
			required: true
		}
	},
	setup(props, { emit }) {
		const onInput = (e) => {
			emit('update:modelValue', e.target.value)
		}
		
		return {
			onInput
		}
	}
}
</script>