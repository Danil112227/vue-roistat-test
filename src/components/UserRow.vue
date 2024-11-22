<template>
  <template v-if="user">
    <tr>
      <td>
        <div :style="{ marginLeft: level * 20 + 'px' }" class="indent">
          <button v-if="children.length" @click="toggle">
            {{ expanded ? "-" : "+" }}
          </button>
          <span v-else class="toggle-placeholder"></span>
          <div class="user-name">{{ user.name }}</div>
        </div>
      </td>
      <td>
        <div class="user-phone">{{ user.phone }}</div>
      </td>
    </tr>
    <template v-if="expanded">
      <UserRow
        v-for="child in children"
        :key="child.id"
        :user="child"
        :children="child.children || []"
        :level="level + 1"
      />
    </template>
  </template>
</template>

<script>
export default {
  name: "UserRow",
  props: {
    user: Object,
    children: Array,
    level: {
      type: Number,
      default: 0,
    },
  },
  data() {
    return {
      expanded: false,
    };
  },
  methods: {
    toggle() {
      this.expanded = !this.expanded;
    },
    getChildren(parentId) {
      return this.children.filter((child) => child.parentId === parentId);
    },
  },
};
</script>

<style scoped>
.user-table td {
  padding: 10px;
  border: 1px solid #ddd;
  white-space: nowrap;
  overflow: hidden;
}

.indent {
  display: flex;
  align-items: center;
  margin-left: 0;
}

button {
  background: none;
  border: none;
  cursor: pointer;
}

.toggle-placeholder {
  display: inline-block;
  width: 20px;
  height: 20px;
}

.user-name {
  margin-left: 20px;
}

.user-phone {
  margin-left: 20px;
}
</style>
