<template>
  <app-layout>
    <template #header>
      <h2 class="font-semibold text-xl text-gray-800 leading-tight">Manage Users</h2>
    </template>
    <div class="py-12">
      <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
        <div class="bg-white overflow-hidden shadow-xl sm:rounded-lg px-4 py-4">
          <div
            class="bg-teal-100 border-t-4 border-teal-500 rounded-b text-teal-900 px-4 py-3 shadow-md my-3"
            role="alert"
            v-if="$page.flash.message"
          >
            <div class="flex">
              <div>
                <p class="text-sm">{{ $page.flash.message }}</p>
              </div>
            </div>
          </div>
          <button
            @click="openModal()"
            class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded my-3"
          >Create New User</button>
          <table class="table-fixed w-full">
            <thead>
              <tr class="bg-gray-100">
                <th class="px-4 py-2 w-20">No.</th>
                <th class="px-4 py-2">Nombre</th>
                <th class="px-4 py-2">Email</th>
                <th class="px-4 py-2">Action</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="row in userList.data" :key="row.id">
                <td class="border px-4 py-2">{{ row.id }}</td>
                <td class="border px-4 py-2">{{ row.name }}</td>
                <td class="border px-4 py-2">{{ row.email }}</td>
                <td class="border px-4 py-2">
                  <button
                    @click="edit(row)"
                    class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
                  >Edit</button>
                  <button
                    @click="deleteRow(row)"
                    class="bg-red-500 hover:bg-red-700 text-white font-bold py-2 px-4 rounded"
                  >Delete</button>
                </td>
              </tr>
            </tbody>
          </table>
          <!-- Paginación -->
          <div class="p-5 flex justify-end">
            <inertia-link
              class="px-2"
              v-if="userList.prev_page_url"
              :href="userList.prev_page_url"
              preserve-scroll
              replace
            >Previous</inertia-link>
            <inertia-link
              class="px-2"
              v-if="userList.next_page_url"
              :href="userList.next_page_url"
              preserve-scroll
              replace
            >Next</inertia-link>
          </div>
          <!-- modal -->
          <transition name="fade">
            <div class="fixed z-10 inset-0 overflow-y-auto ease-out duration-400" v-if="isOpen">
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
                    <div class="bg-white px-4 pt-5 pb-4 sm:p-6 sm:pb-4">
                      <div class>
                        <div class="mb-4">
                          <label
                            for="exampleFormControlInput1"
                            class="block text-gray-700 text-sm font-bold mb-2"
                          >Nombre:</label>
                          <input
                            type="text"
                            class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                            id="exampleFormControlInput1"
                            placeholder="Ingresa el nombre"
                            v-model="form.name"
                            autocomplete="off"
                          />
                          <div
                            v-if="$page.errors.name"
                            class="text-red-500"
                          >{{ $page.errors.name[0] }}</div>
                        </div>
                        <div class="mb-4">
                          <label
                            for="exampleFormControlInput1"
                            class="block text-gray-700 text-sm font-bold mb-2"
                          >Password:</label>
                          <input
                            type="text"
                            class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                            id="exampleFormControlInput1"
                            placeholder="Enter pass"
                            v-model="form.password"
                            autocomplete="off"
                          />
                          <div
                            v-if="$page.errors.password"
                            class="text-red-500"
                          >{{ $page.errors.password[0] }}</div>
                        </div>
                        <div class="mb-4">
                          <label
                            for="exampleFormControlInput2"
                            class="block text-gray-700 text-sm font-bold mb-2"
                          >Email:</label>
                          <input
                            class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                            id="exampleFormControlInput2"
                            v-model="form.email"
                            placeholder="Correo electrónico"
                            autocomplete="off"
                          />
                          <div
                            v-if="$page.errors.email"
                            class="text-red-500"
                          >{{ $page.errors.email[0] }}</div>
                        </div>
                      </div>
                    </div>
                    <div class="bg-gray-50 px-4 py-3 sm:px-6 sm:flex sm:flex-row-reverse">
                      <span class="flex w-full rounded-md shadow-sm sm:ml-3 sm:w-auto">
                        <button
                          wire:click.prevent="store()"
                          type="button"
                          class="inline-flex justify-center w-full rounded-md border border-transparent px-4 py-2 bg-green-600 text-base leading-6 font-medium text-white shadow-sm hover:bg-green-500 focus:outline-none focus:border-green-700 focus:shadow-outline-green transition ease-in-out duration-150 sm:text-sm sm:leading-5"
                          v-if="!editMode"
                          @click="save(data)"
                        >Save</button>
                      </span>
                      <span class="flex w-full rounded-md shadow-sm sm:ml-3 sm:w-auto">
                        <button
                          wire:click.prevent="store()"
                          type="button"
                          class="inline-flex justify-center w-full rounded-md border border-transparent px-4 py-2 bg-green-600 text-base leading-6 font-medium text-white shadow-sm hover:bg-green-500 focus:outline-none focus:border-green-700 focus:shadow-outline-green transition ease-in-out duration-150 sm:text-sm sm:leading-5"
                          v-show="editMode"
                          @click="update(form)"
                        >Update</button>
                      </span>
                      <span class="mt-3 flex w-full rounded-md shadow-sm sm:mt-0 sm:w-auto">
                        <button
                          @click="closeModal()"
                          type="button"
                          class="inline-flex justify-center w-full rounded-md border border-gray-300 px-4 py-2 bg-white text-base leading-6 font-medium text-gray-700 shadow-sm hover:text-gray-500 focus:outline-none focus:border-blue-300 focus:shadow-outline-blue transition ease-in-out duration-150 sm:text-sm sm:leading-5"
                        >Cancel</button>
                      </span>
                    </div>
                  </form>
                </div>
              </div>
            </div>
          </transition>
        </div>
      </div>
    </div>
  </app-layout>
</template>
<script>
import AppLayout from "../../Layouts/AppLayout";
import Welcome from "../../Jetstream/Welcome";
import { Inertia } from "@inertiajs/inertia";

export default {
  components: {
    AppLayout,
    Welcome
  },
  props: {
    errors: Object,
    userList: Object
  },
  data() {
    return {
      editMode: false,
      isOpen: false,
      url: "/users",
      form: {
        name: null,
        password: null,
        email: null
      }
    };
  },
  methods: {
    openModal() {
      this.isOpen = true;
    },
    closeModal() {
      this.isOpen = false;
      this.reset();
      this.editMode = false;
    },
    save(data) {
      Inertia.post(this.url, this.form, {
        onSuccess: page => {
          if (Object.entries(page.props.errors).length === 0) {
            this.isOpen = false;
            this.reset();
          }
        }
      });
      Inertia.on("error", event => {
        console.log("errores detectados: ", event.detail.error);
        event.preventDefault();
        // podemos manejar los errores como queramos
      });
    },
    reset() {
      this.form = {
        name: null,
        email: null
      };
    },
    edit(data) {
      this.form = Object.assign({}, data);
      this.editMode = true;
      this.openModal();
    },
    update(data) {
      Inertia.put(this.url + `/${data.id}`, data, {
        onSuccess: page => {
          if (Object.entries(page.props.errors).length === 0) {
            this.isOpen = false;
            this.reset();
            return;
          }
        }
      });
      Inertia.on("error", event => {
        event.preventDefault();
        console.log(event.detail.error);
      });
    },
    deleteRow(data) {
      console.log(data);
      if (!confirm("Estas seguro de borrar este elemento?")) return;

      Inertia.delete(`/users/${data.id}`, {
        onSuccess: page => {
          if (Object.entries(page.props.errors).length === 0) {
            this.isOpen = false;
          }
        }
      });
      Inertia.on("error", event => {
        console.log("Corregir: ", event.detail.error);
      });
      this.reset();
      this.closeModal();
    }
  }
};
</script>