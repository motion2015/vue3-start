<template>
  <div 
  class="card mt-2" 
  v-for="(item, index) in items" 
  :key="item.id">
    <slot :item="item" :index="index"></slot>
  </div>
</template>
<script>
export default {
  name: 'ListComp',
  props: {
    items: {
      tyep: Array,
      required: true
    }
  }
}
</script>