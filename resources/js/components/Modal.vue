<template>
  <div class="fixed z-10 inset-0 overflow-y-auto ease-out duration-400">
    <div
      class="flex items-end justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0"
    >
      <div class="fixed inset-0 transition-opacity">
        <div class="absolute inset-0 bg-gray-500 opacity-75"></div>
      </div>
      <!-- This element is to trick the browser into centering the modal contents. -->
      <span class="hidden sm:inline-block sm:align-middle sm:h-screen"></span>​
      <div
        class="inline-block align-bottom bg-white rounded-lg text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-lg sm:w-full"
        role="dialog"
        aria-modal="true"
        aria-labelledby="modal-headline"
      >
        <form>
          <div class="flex justify-between border-b border-gray-100 px-5 py-4">
            <div>
              <i class="fas fa-exclamation-circle text-blue-500"></i>
              <span class="font-bold text-gray-700 text-lg">{{ setTitle }}</span>
            </div>
            <div>
              <button>
                <i
                  class="fa fa-times-circle text-red-500 hover:text-red-600 transition duration-150"
                ></i>
              </button>
            </div>
          </div>
          <div class="bg-white px-4 pt-5 pb-4 sm:p-6 sm:pb-4">
            <div class>
              <div v-if="isAdmin" class="mb-4">
                <label
                  for="exampleFormControlInput1"
                  class="block text-gray-700 text-sm font-bold mb-2"
                >Buscar Usuario</label>
                <!-- Buscador way -->
                <div class="flex flex-col relative">
                  <div class="w-full">
                    <div class="my-2 p-1 bg-white flex border border-gray-200 rounded">
                      <div class="flex flex-auto flex-wrap"></div>
                      <input
                        v-model="term"
                        @keyup="searchUser"
                        placeholder="Buscar Usuario"
                        class="p-1 px-2 appearance-none outline-none w-full text-gray-800"
                      />
                      <div
                        v-if="isSearching"
                        class="text-gray-300 w-8 py-1 pl-2 pr-1 border-l flex items-center border-gray-200"
                      >
                        <button
                          type="button"
                          @click="closeList"
                          class="cursor-pointer w-6 h-6 text-red-600 outline-none focus:outline-none"
                        >
                          <svg
                            xmlns="http://www.w3.org/2000/svg"
                            width="100%"
                            height="100%"
                            fill="none"
                            viewBox="0 0 24 24"
                            stroke="currentColor"
                            stroke-width="2"
                            stroke-linecap="round"
                            stroke-linejoin="round"
                            class="feather feather-chevron-up w-4 h-4"
                          >
                            <polyline points="18 15 12 9 6 15" />
                          </svg>
                        </button>
                      </div>
                    </div>
                  </div>
                  <!-- Options select -->
                  <div
                    class="absolute shadow bg-white top-100 z-40 w-full lef-0 rounded max-h-select overflow-y-auto svelte-5uyqqj"
                  >
                    <!-- partial component -->
                    <Users v-if="isSearching" :userList="allUsers" @click="selectedUser" />
                  </div>
                  <!-- end Options -->
                </div>
                <!-- comienzo input -->
              </div>
              <!-- end Buscador -->
              <div class="mb-4">
                <label
                  for="exampleFormControlInput1"
                  class="block text-gray-700 text-sm font-bold mb-2"
                >Motivo:</label>
                <input
                  type="text"
                  class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                  id="exampleFormControlInput1"
                  placeholder="Ingresa el motivo de la consulta"
                  v-model="form.title"
                  autocomplete="off"
                />
                <div v-if="$page.errors.title" class="text-red-500">{{ $page.errors.title[0] }}</div>
              </div>
              <div class="mb-4">
                <label
                  for="exampleFormControlInput1"
                  class="block text-gray-700 text-sm font-bold mb-2"
                >Fecha</label>
                <input
                  disabled="true"
                  type="text"
                  class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                  id="exampleFormControlInput1"
                  placeholder="Enter pass"
                  v-model="form.date_at"
                />
              </div>
              <div class="mb-4">
                <label
                  for="exampleFormControlInput2"
                  class="block text-gray-700 text-sm font-bold mb-2"
                >Hora</label>
                <input
                  disabled="true"
                  type="time"
                  class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                  id="exampleFormControlInput2"
                  v-model="form.hour"
                />
              </div>
              <!-- start select -->
              <div class="col-span-6 sm:col-span-3">
                <label
                  for="timeSesion"
                  class="block text-sm font-medium leading-5 text-gray-700"
                >Duración</label>
                <select
                  v-model="form.session"
                  id="timeSesion"
                  class="mt-1 block form-select w-full py-2 px-3 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:shadow-outline-blue focus:border-blue-300 transition duration-150 ease-in-out sm:text-sm sm:leading-5"
                >
                  <option value="900">15 minutos</option>
                  <option value="1800">30 minutos</option>
                  <option value="3600">1 hora</option>
                </select>
              </div>
              <!-- end select -->
            </div>
          </div>
          <div class="bg-gray-50 px-4 py-3 sm:px-6 sm:flex sm:flex-row-reverse">
            <button
              wire:click.prevent="store()"
              type="button"
              class="inline-flex justify-center w-full border border-teal-500 bg-teal-500 text-white rounded-md px-4 py-2 m-2 transition duration-500 ease select-none hover:bg-teal-600 focus:outline-none focus:shadow-outline"
              @click="processForm(form)"
            >{{ getActionBtnLabel }}</button>
            <button
              type="button"
              class="inline-flex justify-center w-full border border-red-500 text-red-500 rounded-md px-4 py-2 m-2 transition duration-500 ease select-none hover:text-white hover:bg-red-600 focus:outline-none focus:shadow-outline"
              @click="deleteTo(form)"
              v-if="editMode"
            >Eliminar</button>
            <button
              @click="$emit('closeModal')"
              type="button"
              class="inline-flex justify-center w-full border border-gray-200 bg-gray-200 text-gray-700 rounded-md px-4 py-2 m-2 transition duration-500 ease select-none hover:bg-gray-300 focus:outline-none focus:shadow-outline"
            >atrás</button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import { Inertia } from "@inertiajs/inertia";
import Users from "../partials/users";
import EventBus from "../bus/event-bus";

export default {
  name: "Modal",
  components: {
    Users
  },
  props: {
    userApt: String,
    errors: Object,
    editMode: {
      type: Boolean,
      default: false
    },
    form: {
      type: Object,
      default: () => {}
    },
    users: Array,
    default: () => {}
  },
  data() {
    return {
      term: "",
      searching: false
    };
  },
  mounted() {
    if (this.userApt !== "") {
      this.term = this.userApt;
    }else{
      this.term = ''
    }
  },
  computed: {
    isAdmin() {
      return this.$page.user.isAdmin;
    },
    allUsers() {
      return this.users;
    },
    getActionBtnLabel() {
      return this.form.id !== "" ? "Actualizar" : "Reservar";
    },
    setTitle() {
      return this.editMode ? "Modificar evento" : "Reservar evento";
    },
    isSearching() {
      return this.searching;
    }
  },
  methods: {
    closeList() {
      return (this.searching = !this.searching);
    },
    selectedUser(user) {
      this.term = user.name;
      this.searching = false;
      if(this.$page.user.isAdmin){
        this.form.user_id = user.id;
        return
      }else{
        this.form.user_id = this.$page.user.id
      }
    },
    searchUser() {
      if (this.term !== "") {
        this.searching = true;
        this.$emit("searchUser", this.term);
        return;
      }
      alert("No puedes dejar vacío este campo de búsqueda");
    },
    processForm(form) {
      if (this.form.id === "") {
        this.$emit("saveAppt", form);
      }
      if (this.form.id !== "") {
        this.$emit("editAppt", form);
      }
    },
    deleteTo(item) {
      this.$emit("removeApt", item.id);
    }
  }
};
</script>

<style scoped>
.top-100 {
  top: 100%;
}
.bottom-100 {
  bottom: 100%;
}

.max-h-select {
  max-height: 300px;
}
</style>


