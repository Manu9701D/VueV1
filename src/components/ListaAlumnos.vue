<template>
  <div class="p-6">
    <!-- Botón para añadir/cerrar formulario -->
    <div class="flex justify-between items-center mb-4">
      <button
        @click="mostrarFormulario = !mostrarFormulario"
        :class="[
          'text-white px-4 py-2 rounded-lg',
          mostrarFormulario
            ? 'bg-red-600 hover:bg-red-700'
            : 'bg-green-600 hover:bg-green-700',
        ]"
      >
        {{ mostrarFormulario ? "Cerrar formulario" : "Añadir alumno" }}
      </button>
    </div>

    <!-- Formulario arriba -->
    <form
      v-if="mostrarFormulario"
      @submit.prevent="guardarAlumno"
      class="mb-6 bg-neutral-800 p-4 rounded-lg shadow text-white"
    >
      <div class="grid grid-cols-2 gap-4">
        <template v-for="(campo, index) in camposForm" :key="index">
          <input
            v-if="!(campo === 'password' && form.id)"
            :type="
              campo === 'password'
                ? 'password'
                : campo === 'edad'
                ? 'number'
                : 'text'
            "
            v-model="form[campo]"
            :placeholder="capitalizar(campo)"
            class="p-2 bg-neutral-700 rounded"
            :required="
              campo === 'nombre' ||
              campo === 'email' ||
              (campo === 'password' && !form.id)
            "
          />
        </template>
      </div>

      <div class="mt-4">
        <button
          type="submit"
          class="bg-blue-500 hover:bg-blue-600 px-4 py-2 rounded"
        >
          {{ form.id ? "Actualizar" : "Crear" }}
        </button>
        <button
          v-if="form.id"
          type="button"
          @click="cancelarEdicion"
          class="ml-2 bg-orange-600 hover:bg-yellow-700 px-4 py-2 rounded"
        >
          Cancelar edición
        </button>
      </div>
    </form>

    <!-- Tabla -->
    <table
      class="min-w-full bg-neutral-800 text-white shadow-md rounded-lg overflow-hidden"
    >
      <thead class="bg-black">
        <tr>
          <th
            v-for="(columna, index) in columnas"
            :key="index"
            class="py-3 px-6 text-center font-bold uppercase text-white"
          >
            {{ columna }}
          </th>
        </tr>
      </thead>

      <tbody>
        <tr
          v-for="user in users"
          :key="user.id"
          class="border-b border-neutral-700 hover:bg-neutral-700"
        >
          <td
            v-for="campo in camposTabla"
            :key="campo"
            class="py-3 text-center px-6"
          >
            {{ user[campo] }}
          </td>
          <td class="py-3 px-6 text-center w-[120px]">
            <div class="h-6 flex justify-center items-center space-x-2">
              <button
                @click.stop="editarAlumno(user)"
                class="text-yellow-400 hover:text-yellow-300"
                title="Editar"
              >
                📝
              </button>
              <button
                @click.stop="eliminarAlumno(user.id)"
                class="text-red-400 hover:text-red-300"
                title="Eliminar"
              >
                🗑️
              </button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Estado -->
    <div v-if="loading" class="text-center mt-4 text-neutral-500">
      Cargando...
    </div>
    <div v-if="error" class="text-red-500 mt-4">{{ error }}</div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      users: [],
      loading: true,
      error: null,
      mostrarFormulario: false,
      alumnoSeleccionadoId: null,
      form: {
        id: null,
        nombre: "",
        telefono: "",
        edad: "",
        genero: "",
        email: "",
        password: "",
      },
      columnas: [
        "ID",
        "Nombre",
        "Telefono",
        "Edad",
        "Genero",
        "Email",
        "Acciones",
      ],
      camposForm: ["nombre", "email", "telefono", "edad", "genero", "password"],
      camposTabla: ["id", "nombre", "telefono", "edad", "genero", "email"],
    };
  },
  methods: {
    resetForm() {
      this.form = {
        id: null,
        nombre: "",
        email: "",
        telefono: "",
        edad: null,
        genero: "",
        password: "",
      };
    },
    cancelarEdicion() {
      this.resetForm();
      this.mostrarFormulario = false;
    },
    capitalizar(texto) {
      return texto.charAt(0).toUpperCase() + texto.slice(1);
    },

    cargarAlumnos() {
      this.loading = true;
      axios
        .get("http://localhost:8000/api/alumnos")
        .then((response) => {
          this.users = response.data;
          this.loading = false;
        })
        .catch((error) => {
          this.error = "Error loading data.";
          this.loading = false;
        });
    },
    guardarAlumno() {
      if (this.form.id) {
        const formData = { ...this.form };
        if (formData.password === "") {
          delete formData.password;
        }
        axios
          .put(`http://localhost:8000/api/alumnos/${formData.id}`, formData)
          .then(() => {
            this.cargarAlumnos();
            this.resetForm();
            this.mostrarFormulario = false;
          })
          .catch((error) => {
            this.error = "Error updating data.";
          });
      } else {
        axios
          .post("http://localhost:8000/api/alumnos", this.form)
          .then(() => {
            this.cargarAlumnos();
            this.resetForm();
          })
          .catch((error) => {
            this.error = "Error creating student.";
          });
      }
    },
    editarAlumno(alumno) {
      this.form = {
        id: alumno.id,
        nombre: alumno.nombre,
        email: alumno.email,
        telefono: alumno.telefono,
        edad: alumno.edad,
        genero: alumno.genero,
        password: "",
      };
      this.mostrarFormulario = true;
    },
    eliminarAlumno(id) {
      if (confirm("Are you sure you want to delete this student?")) {
        axios.delete(`http://localhost:8000/api/alumnos/${id}`).then(() => {
          this.cargarAlumnos().catch(
            (err) => (this.error = "Error deleting student.")
          );
        });
      }
    },
    resetForm() {
      this.form = {
        id: null,
        nombre: "",
        telefono: "",
        edad: "",
        genero: "",
        email: "",
        password: "",
      };
    },
  },
  mounted() {
    this.cargarAlumnos();
  },
};
</script>
