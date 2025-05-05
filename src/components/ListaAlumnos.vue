<template>
  <div class="p-6">
    <h1 class="text-2xl font-bold mb-4 text-white">Lista de Usuarios</h1>
    
    <form v-if="mostrarFormulario" @submit.prevent="guardarAlumno" class="mb-6 bg-gray-800 p-4 rounded-lg shadow text-white">
      <div class="grid grid-cols-2 gap-4">
        <input v-model="form.nombre" placeholder="Nombre" class="p-2 bg-gray-700 rounded" required />
        <input v-model="form.email" placeholder="Email" class="p-2 bg-gray-700 rounded" required />
        <input v-model="form.telefono" placeholder="Teléfono" class="p-2 bg-gray-700 rounded" />
        <input v-model="form.edad" type="number" placeholder="Edad" class="p-2 bg-gray-700 rounded" />
        <input v-model="form.genero" placeholder="Género" class="p-2 bg-gray-700 rounded" />
        <input v-if="!form.id" v-model="form.password" placeholder="Contraseña" type="password" class="p-2 bg-gray-700 rounded" required />
      </div>
      <button type="submit" class="mt-4 bg-blue-500 hover:bg-blue-600 px-4 py-2 rounded">
        {{ form.id ? 'Actualizar' : 'Crear' }}
      </button>
      <button v-if="form.id" type="button" @click="resetForm" class="mt-4 ml-2 bg-gray-600 hover:bg-gray-700 px-4 py-2 rounded">
        Cancelar edición
      </button>
    </form>
    <button @click="mostrarFormulario = !mostrarFormulario"
        class="mb-4 bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded">
        {{ mostrarFormulario ? 'Cerrar formulario' : 'Añadir alumno' }}
    </button>

    <table class="min-w-full bg-gray-800 text-white shadow-md rounded-lg overflow-hidden">
      <thead class="bg-gray-900">
        <tr>
          <th class="py-3 px-6 text-center font-bold uppercase">ID</th>
          <th class="py-3 px-6 text-center font-bold uppercase">Nombre</th>
          <th class="py-3 px-6 text-center font-bold uppercase">Telefono</th>
          <th class="py-3 px-6 text-center font-bold uppercase">Edad</th>
          <th class="py-3 px-6 text-center font-bold uppercase">Genero</th>
          <th class="py-3 px-6 text-center font-bold uppercase">Email</th>
          <th class="py-3 px-6 text-center font-bold uppercase"></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="user in users" :key="user.id" class="border-b border-gray-700 hover:bg-gray-700">
          <td class="py-3 text-center px-6">{{ user.id }}</td>
          <td class="py-3 text-center px-6">{{ user.nombre }}</td>
          <td class="py-3 text-center px-6">{{ user.telefono }}</td>
          <td class="py-3 text-center px-6">{{ user.edad }}</td>
          <td class="py-3 text-center px-6">{{ user.genero }}</td>
          <td class="py-3 text-center px-6">{{ user.email }}</td>
          <td class="py-3 text-center px-6">
            <button @click="editarAlumno(user)" class="text-yellow-400 hover:text-yellow-300 mr-2">Editar</button>
            <button @click="eliminarAlumno(user.id)" class="text-red-400 hover:text-red-300">Eliminar</button>
          </td>
        </tr>
      </tbody>
    </table>

    <div v-if="loading" class="text-center mt-4 text-gray-500">Cargando...</div>
    <div v-if="error" class="text-red-500 mt-4">{{ error }}</div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      users: [],
      loading: true,
      error: null,
      mostrarFormulario: false,
      form: {
        id: null,
        nombre: '',
        telefono: '',
        edad: '',
        genero: '',
        email: '',
        password:'',
      },
    };
  },
  methods: {
    resetForm() {
    this.form = {
      id: null,
      nombre: '',
      email: '',
      telefono: '',
      edad: null,
      genero: '',
      password: ''
    };
  },
    cargarAlumnos() {
      this.loading = true;
      axios.get('http://localhost:8000/api/alumnos')
        .then(response => {
          this.users = response.data;
          this.loading = false;
        })
        .catch(error=> {
          this.error = 'Error loading data.';
          this.loading = false;
        })
    },
    guardarAlumno() {
      if (this.form.id) {
        const formData = { ...this.form };
      if (formData.password === '') {
        delete formData.password;
      }
        axios.put(`http://localhost:8000/api/alumnos/${formData.id}`, formData)
          .then(() => {
            this.cargarAlumnos();
            this.resetForm();
            this.mostrarFormulario = false;
          })
          .catch(error => {
            this.error = 'Error updating data.';
          });
      } else {
        axios.post('http://localhost:8000/api/alumnos', this.form)
          .then(() => {
            this.cargarAlumnos();
            this.resetForm();
          })
          .catch(error => {
            this.error = 'Error creating student.';
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
        password: '',
      };
      this.mostrarFormulario = true;
    },
    eliminarAlumno(id) {
      if (confirm('Are you sure you want to delete this student?')) {
        axios.delete(`http://localhost:8000/api/alumnos/${id}`)
          .then(() => {this.cargarAlumnos()
          .catch(err => this.error = 'Error deleting student.');
          });
      }
    },
    resetForm() {
      this.form = {
        id: null,
        nombre: '',
        telefono: '',
        edad: '',
        genero: '',
        email: '',
        password:'',
      };
    },
  },
  mounted() {
    this.cargarAlumnos();
  }

};
</script>
