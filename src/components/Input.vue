<template lang="">
  <div class="form-group">
    <label>{{label}}</label>
    <input 
    type="text" 
    class="form-control" 
    :value="subject"
    @input="onInput"
    />
    <div v-if="error" class="text-red">{{error}}</div>
  </div>
</template>
<script>
export default {
  name: 'InputComp',
  props: {
    label: {
      type:String,
      reqired: true
    },
    error: {
      type:String,
      reqired: true
    },
    subject: {
      type:String,
      reqired: true
    }
  },
  setup(props, {emit}) {
    const onInput =(e)=> {
      // console.log(e.target.value);
      // emit('update-subject', e.target.value);
      emit('update:subject', e.target.value);  // subject는 props의 subject
    }
    return {
      onInput
    }
  }
}
</script>
<style scoped>
.text-red {color: red;}
</style>