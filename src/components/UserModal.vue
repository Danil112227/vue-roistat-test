<template>
  <div class="modal">
    <div class="modal-body">
      <div class="modal-header">
        <h3>Добавление пользователя</h3>
        <button @click="closeModal" class="close-btn">X</button>
      </div>
      <form @submit.prevent="saveUser">
        <div class="form-group">
          <label for="name">Имя:</label>
          <input
            v-model="form.name"
            id="name"
            type="text"
            placeholder="Введите имя"
            required
          />
        </div>
        <div class="form-group">
          <label for="phone">Телефон:</label>
          <input
            v-model="form.phone"
            id="phone"
            type="tel"
            placeholder="Введите телефон"
            required
          />
        </div>
        <div class="form-group">
          <label for="parent">Начальник:</label>
          <select v-model="form.parentId" id="parent">
            <option :value="null">Без начальника</option>
            <option v-for="user in users" :key="user.id" :value="user.id">
              {{ user.name }}
            </option>
          </select>
        </div>
        <div class="form-actions">
          <button type="submit" class="btn btn-primary">Сохранить</button>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    users: {
      type: Array,
      required: true,
    },
    onSave: {
      type: Function,
      required: true,
    },
  },
  data() {
    return {
      form: {
        name: "",
        phone: "",
        parentId: null,
      },
    };
  },
  methods: {
    saveUser() {
      this.onSave({ ...this.form, id: Date.now(), children: [] });
      this.resetForm();
      this.closeModal();
    },
    closeModal() {
      this.$emit("close");
    },
    resetForm() {
      this.form = {
        name: "",
        phone: "",
        parentId: null,
      };
    },
  },
};
</script>

<style scoped>
.modal {
  position: absolute;
  left: 55%;
  top: 95px;
  width: 400px;
  height: auto;
  border: 1px solid #ddd;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.modal-body {
  background: white;
  padding: 10px 20px 20px 20px;
  border-radius: 8px;
  width: 400px;
}

.close {
  position: absolute;
  top: 10px;
  right: 10px;
  font-size: 20px;
  cursor: pointer;
}

.form-group {
  margin: 10px 0;
}

form label {
  display: block;
  margin-bottom: 10px;
  margin-top: 15px;
}

form input,
form select {
  width: 100%;
  padding: 8px;
  box-sizing: border-box;
  border: 1px solid #ddd;
}

button {
  padding: 10px 10px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
</style>
