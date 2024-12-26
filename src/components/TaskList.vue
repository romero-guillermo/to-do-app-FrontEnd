<template>
  <div>
      <h1>Lista de Tareas</h1>
      <ul>
          <li v-for="task in tasks" :key="task.id">
            <input
                 type="checkbox"
                    v-model="task.completed"
                        @change="updateTask(task)"
                        />  
              {{ task.title }}
              <button @click="deleteTask(task.id)">Eliminar</button>
          </li>
      </ul>
      <form @submit.prevent="addTask">
          <input type="text" v-model="newTaskTitle" placeholder="Nueva tarea" />
          <button type="submit">Agregar</button>
      </form>
  </div>
</template>

<script>
import api from "../components/api.js";

export default {
  data() {
      return {
          tasks: [],
          newTaskTitle: "",
      };
  },
  methods: {
    async fetchTasks() {
    try {
      const response = await api.get("/tareas");
      this.tasks = response.data; // Asegúrate de que completed llega como booleano
      console.log("Tareas cargadas:", this.tasks);
    } catch (error) {
      console.error("Error al cargar tareas:", error);
    }
  },
  async addTask() {
    if (this.newTaskTitle.trim()) {
        try {
            // Agregar la nueva tarea al backend
            await api.post("/tareas", {
                title: this.newTaskTitle,
                completed: false,
            });

            // Limpiar el campo de entrada
            this.newTaskTitle = "";

            // Recargar la lista de tareas desde el backend
            await this.fetchTasks();
        } catch (error) {
            console.error("Error al agregar la tarea:", error);
        }
    }

},
      async updateTask(task) {
        try {
            // Cambia el estado de `completed` en la base de datos
            const response = await api.put("/tareas", {
                id: task.id,
                title: task.title, // Incluye el título existente para no perder datos
                completed: task.completed, // Usa el valor actualizado del checkbox
            });

            console.log("Task updated:", response.data);
        } catch (error) {
            console.error("Error updating task:", error);
            // En caso de error, revertir el cambio en el modelo
            task.completed = !task.completed;
        }
        console.log(task.completed)
    },
      async deleteTask(id) {
          await api.delete("/tareas", { data: { id } });
          this.tasks = this.tasks.filter((task) => task.id !== id);
      },
  },
  mounted() {
      this.fetchTasks();
  },
};
</script>

<style scoped>
ul {
  list-style: none;
  padding: 0;
}
li {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}
button {
  margin-left: 10px;
}
</style>