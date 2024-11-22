<template>
  <div>
    <button @click="openModal" class="add-button">Добавить</button>
    <table class="user-table">
      <thead>
        <tr>
          <th @click="sortBy('name')">Имя</th>
          <th @click="sortBy('phone')">Телефон</th>
        </tr>
      </thead>
      <tbody>
        <UserRow
          v-for="user in sortedUsers"
          :key="user.id"
          :user="user"
          :children="user.children || []"
        />
      </tbody>
    </table>

    <Modal
      v-if="isModalOpen"
      :users="flattenUsers(users)"
      @close="closeModal"
      @save="addUser"
    />
  </div>
</template>

<script>
import UserRow from "./UserRow.vue";
import Modal from "./UserModal.vue";

export default {
  name: "UserTable",
  components: {
    UserRow,
    Modal,
  },
  data() {
    return {
      users: JSON.parse(localStorage.getItem("users")) || [],
      isModalOpen: false,
      sortKey: null,
      sortOrder: 1,
    };
  },
  computed: {
    sortedUsers() {
      if (!this.sortKey) return this.users;
      return this.sortUsers(this.users);
    },
  },
  methods: {
    openModal() {
      this.isModalOpen = true;
    },
    closeModal() {
      this.isModalOpen = false;
    },
    addUser(newUser) {
      const existingUser = this.findUserById(newUser.id);
      if (existingUser) {
        this.removeUserById(newUser.id);
        newUser.children = existingUser.children || [];
      }

      if (newUser.parentId) {
        const parent = this.findUserById(newUser.parentId);
        if (parent) {
          if (!parent.children) parent.children = [];
          parent.children.push(newUser);
        }
      } else {
        this.users.push(newUser);
      }

      this.saveToLocalStorage();
    },
    findUserById(id) {
      const findInTree = (users) => {
        for (const user of users) {
          if (user.id === id) return user;
          if (user.children) {
            const found = findInTree(user.children);
            if (found) return found;
          }
        }
        return null;
      };
      return findInTree(this.users);
    },
    removeUserById(id) {
      const removeInTree = (users) => {
        for (let i = 0; i < users.length; i++) {
          if (users[i].id === id) {
            users.splice(i, 1);
            return true;
          }
          if (users[i].children && removeInTree(users[i].children)) {
            return true;
          }
        }
        return false;
      };
      removeInTree(this.users);
    },
    flattenUsers(users) {
      const flatten = (list, result = []) => {
        for (const user of list) {
          result.push({ id: user.id, name: user.name });
          if (user.children) flatten(user.children, result);
        }
        return result;
      };
      return flatten(users);
    },
    sortBy(key) {
      this.sortKey = key;
      this.sortOrder *= -1;
    },
    sortUsers(users) {
      return [...users]
        .sort((a, b) => {
          const valueA = a[this.sortKey];
          const valueB = b[this.sortKey];
          if (valueA < valueB) return this.sortOrder === 1 ? -1 : 1;
          if (valueA > valueB) return this.sortOrder === 1 ? 1 : -1;
          return 0;
        })
        .map((user) => ({
          ...user,
          children: user.children ? this.sortUsers(user.children) : [],
        }));
    },
    saveToLocalStorage() {
      localStorage.setItem("users", JSON.stringify(this.users));
    },
  },
};
</script>

<style scoped>
.user-table {
  table-layout: fixed;
  width: 50%;
  border-collapse: collapse;
  margin-top: 20px;
}

.user-table th {
  cursor: pointer;
  background: #f4f4f4;
  padding: 10px;
  text-align: left;
  border: 1px solid #ddd;
}

.add-button {
  padding: 10px 20px;
  background-color: #007bff;
  color: white;
  border: none;
  cursor: pointer;
  border-radius: 4px;
}

.add-button:hover {
  background-color: #0056b3;
}
</style>
