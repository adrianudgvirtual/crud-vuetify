<template>
  <v-container class="fill-height">
    <v-responsive class="fill-height">
      <v-card>
        <v-card-title>
          <v-row>
            <v-col sm="10">
              <span>Peliculas</span>
            </v-col>
            <v-col sm="2" justify="end">
              <v-dialog v-model="dialogMovie" persistent width="1024">
                <template v-slot:activator="{ props }">
                  <v-btn icon="mdi-plus" color="success" v-bind="props"></v-btn>
                </template>
                <v-card>
                  <v-card-title>
                    <span class="text-h5">{{ formMovieTitle }}</span>
                  </v-card-title>
                  <v-card-text>
                    <v-container>
                      <v-row>
                        <v-col cols="12" sm="12" md="12">
                          <v-text-field ref="title" v-model="movie.title" label="Nombre de la Pelicula*"
                            required></v-text-field>
                        </v-col>
                        <v-col cols="12" sm="6" md="6">
                          <v-text-field v-model="movie.year" type="number" label="Año de Publicación*"
                            required></v-text-field>
                        </v-col>
                        <v-col cols="12" sm="6" md="6">
                          <v-text-field v-model="movie.runtime" type="number" label="Duración en Minutos*"
                            required></v-text-field>
                        </v-col>
                        <v-col cols="12">
                          <v-text-field v-model="movie.cover" label="Link para la Portada" hint="Incluir https://"
                            persistent-hint></v-text-field>
                        </v-col>
                      </v-row>
                    </v-container>
                    <small>*indica campo requerido</small>
                  </v-card-text>
                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="info" variant="text" @click="clearFields">
                      Cerrar
                    </v-btn>
                    <v-btn color="success" variant="text" @click="saveMovie">
                      Guardar
                    </v-btn>
                  </v-card-actions>
                </v-card>
              </v-dialog>

            </v-col>
          </v-row>
        </v-card-title>
        <v-card-text>
          <v-table>
            <thead>
              <tr>
                <th class="text-left">
                  Portada
                </th>
                <th class="text-left">
                  Nombre
                </th>
                <th class="text-left">
                  Año
                </th>
                <th class="text-left">
                  Duración (Min)
                </th>
                <th class="text-left">
                  Acciones
                </th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="movie in movies" :key="movie.id">
                <td v-if=movie.cover>
                  <v-img height="100" :src="movie.cover"></v-img>
                </td>
                <td v-else=movie.cover.null>
                  <span>S/P </span>
                </td>
                <td>{{ movie.title }}</td>
                <td>{{ movie.year }}</td>
                <td>{{ movie.runtime }}</td>
                <td>
                  <v-btn color="info" icon="mdi-pencil" @click="editMovie(movie.id)"></v-btn>
                  <v-btn color="error" icon="mdi-delete" @click="deleteMovie(movie.id, $event)"></v-btn>
                </td>
              </tr>
            </tbody>
          </v-table>
        </v-card-text>


        <v-card-actions>
          <v-row justify="end">
            <v-dialog v-model="dialog" persistent width="auto">
              <template v-slot:activator="{ props }">
                <v-btn icon="mdi-reload" v-bind="props"></v-btn>
              </template>
              <v-card>
                <v-card-title class="text-h5">
                  Reestablecer datos
                </v-card-title>
                <v-card-text>Esta acción reestablecerá los datos y no será posible recuperarlos.</v-card-text>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="green-darken-1" variant="text" @click="dialog = false">
                    Cancelar
                  </v-btn>
                  <v-btn color="red-darken-1" variant="text" @click="restoreMovies(dialog = false)">
                    Aceptar
                  </v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </v-row>
        </v-card-actions>
      </v-card>
      <v-snackbar v-model="snackbar" :timeout="2000">
        {{ textSnack }}
      </v-snackbar>
    </v-responsive>
  </v-container>
</template>
<script>
import defaultMovies from "@/assets/movies.json";

export default {
  data() {
    return {
      dialog: false,
      dialogMovie: false,
      snackbar: false,
      movies: [],
      movie: {
        id: '',
        tiitle: '',
        year: '',
        runtime: '',
        cover: '',
      },
      movieIndex: -1
    }
  },
  created() {
    if (localStorage.getItem('movies') != null) {
      this.movies = JSON.parse(localStorage.getItem('movies'));
    }
    else {
      this.listMovies();
    }

  },
  mounted() {
    //this.$refs.title.focus();
  },
  computed: {
    formMovieTitle() {
      return this.movieIndex === -1 ? 'Agregar Pelicula' : 'Editar Pelicula'
    },
  },
  methods: {
    listMovies: function () {
      const data = defaultMovies;
      this.movies = data;
      this.updateLocalStorage();
    },
    restoreMovies: function () {
      const data = defaultMovies;
      //console.log(data);
      this.movies = data;
      this.updateLocalStorage();
      this.snackbar = true;
      this.textSnack = '¡Base de datos reestablecida!'
      //console.log(this.findMaxId());
    },
    updateLocalStorage: function () {
      localStorage.setItem('movies', JSON.stringify(this.movies))
    },
    deleteMovie: function (id, event) {
      const confirmation = confirm('¿Estas seguro de borrar esta pelicula?');
      if (confirmation) {
        this.movies = this.movies.filter(movie => movie.id != id);
        this.updateLocalStorage();
        this.snackbar = true;
        this.textSnack = '¡La Pelicula se borro correctamente!'
      } else {
        event.preventDefault();
      }
    },
    findMaxId: function () {
      const maxId = Math.max.apply(Math, this.movies.map(function (movie) {
        return movie.id;
      }));
      return maxId;
    },
    clearFields: function () {
      this.movieIndex = -1;
      this.movie.id = "";
      this.movie.title = "";
      this.movie.year = "";
      this.movie.runtime = "";
      this.movie.cover = "";
      this.dialogMovie = false;

    },

    saveMovie() {
      if (this.movieIndex > -1) {
        let editedMovie = {
          id: this.movie.id,
          title: this.movie.title,
          year: this.movie.year,
          runtime: this.movie.runtime,
          cover: this.movie.cover
        }
        this.movies[this.movieIndex] = editedMovie;
        localStorage.setItem('movies', JSON.stringify(this.movies))
        //console.log(editedMovie)
        this.clearFields();
        this.dialogMovie = false;
        this.snackbar = true;
        this.textSnack = '¡La Pelicula se actualizo correctamente!'
      } else {
        this.addMovie();
      }
    },

    addMovie: function (event) {
      //event.preventDefault();

      this.movie.id = this.findMaxId() + 1;
      this.movies.push({
        id: this.movie.id,
        title: this.movie.title,
        year: this.movie.year,
        runtime: this.movie.runtime,
        cover: this.movie.cover
      });
      this.updateLocalStorage();
      this.clearFields();
      this.dialogMovie = false;
      this.snackbar = true;
      this.textSnack = '¡La Pelicula se agrego correctamente!'

    },
    editMovie(id) {
      const movieFound = this.movies.find((movie, index) => {
        this.movieIndex = index;
        return movie.id === id;
      });
      this.movie.id = movieFound.id;
      this.movie.title = movieFound.title;
      this.movie.year = movieFound.year;
      this.movie.runtime = movieFound.runtime;
      this.movie.cover = movieFound.cover;
      this.dialogMovie = true;
    }
  }
}
</script> 