<template>
  <div class="task" :class="[{ completed: days.completed }]">
    <div className="task-info">
      <input @click="this.$emit('mark', days.id)" type="checkbox" />
      <label htmlFor="id">{{ days.event }}</label>
    </div>
    <div className="task-time">{{ days.time }}</div>
  </div>
</template>
<script>
export default {
  props: {
    days: {
      type: Object,
      required: true,
    },
  },
};
</script>
<style></style>
