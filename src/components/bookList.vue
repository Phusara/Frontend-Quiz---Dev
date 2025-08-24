<script setup>
import { onMounted, ref } from "vue";
import {
  getItems,
  addItem,
  editItem,
  deleteItemById,
  getItemById,
} from "../../utils/fetchUtils.js";

const editMode = ref(false);
const books = ref([]);
const bookbyID = ref(null);
// Fetch a book by its ID from the backend
const fetchBookById = async (id) => {
  try {
    const book = await getItemById(itemUrl, id);
    bookbyID.value = book;
    return book;
  } catch (error) {
    console.error("Failed to fetch book by ID:", error);
    return null;
  }
};
const selectedBooks = ref([]);
const itemUrl = `${import.meta.env.VITE_APP_URL}/books`;
const showEditModal = ref(false);
const editBook = ref({ id: null, name: "", author: "", date: "" });
const showAddModal = ref(false);
const newBook = ref({ name: "", author: "", date: "" });

onMounted(async () => {
  try {
    books.value = await getItems(itemUrl);
    console.log(books.value);
  } catch (error) {
    console.error("Failed to fetch books:", error);
    // Optionally show a user-friendly message in the UI
  }
});

const openEditModal = async (book) => {
  // Always fetch the latest book data by ID
  const latestBook = await fetchBookById(book.id);
  if (latestBook) {
    editBook.value = { ...latestBook };
    showEditModal.value = true;
  } else {
    // Optionally show a user-friendly message in the UI
    console.error("Book not found for editing.");
  }
};

const openAddModal = () => {
  newBook.value = { name: "", author: "", date: "" };
  showAddModal.value = true;
};

const saveAdd = async () => {
  try {
    const added = await addItem(itemUrl, newBook.value);
    books.value.push(added);
    closeAddModal();
  } catch (error) {
    console.error("Failed to add book:", error);
    // Optionally show a user-friendly message in the UI
  }
};

const closeAddModal = () => {
  showAddModal.value = false;
};

const toggleEditMode = () => {
  editMode.value = !editMode.value;
  if (!editMode.value) {
    selectedBooks.value = [];
  }
};

const toggleSelectBook = (id) => {
  const idx = selectedBooks.value.indexOf(id);
  if (idx === -1) {
    selectedBooks.value.push(id);
  } else {
    selectedBooks.value.splice(idx, 1);
  }
};

const closeEditModal = () => {
  showEditModal.value = false;
};

const saveEdit = async () => {
  try {
    // Fetch the latest book before editing
    const bookToEdit = await fetchBookById(editBook.value.id);
    if (!bookToEdit) {
      console.error("Book not found for editing.");
      return;
    }
    const updated = await editItem(itemUrl, editBook.value.id, editBook.value);
    const idx = books.value.findIndex((b) => b.id === editBook.value.id);
    if (idx !== -1) {
      books.value[idx] = updated;
    }
    closeEditModal();
  } catch (error) {
    console.error("Failed to edit book:", error);
    // Optionally show a user-friendly message in the UI
  }
};

const deleteSelectedBooks = async () => {
  try {
    // Delete each selected book by id, fetching each before deleting
    for (const id of selectedBooks.value) {
      const bookToDelete = await fetchBookById(id);
      if (bookToDelete) {
        await deleteItemById(itemUrl, id);
      } else {
        console.error(`Book with id ${id} not found for deletion.`);
      }
    }
    // Remove deleted books from local list
    books.value = books.value.filter(
      (book) => !selectedBooks.value.includes(book.id)
    );
    selectedBooks.value = [];
  } catch (error) {
    console.error("Failed to delete selected books:", error);
    // Optionally show a user-friendly message in the UI
  }
};
</script>

<template>
  <div class="h-full w-full 2 p-2 border rounded shadow bg-white">
    <div class="flex w-full relative">
      <div class="pl-6">
      <button class="btn btn-soft btn-primary" @click="openAddModal">Add</button>
    </div>
      <div class="absolute right-0 pr-6">
        <button class="btn btn-soft btn-warning" @click="toggleEditMode">
        {{ editMode ? "Cancel" : "Edit" }}
      </button>
      <span v-if="editMode && selectedBooks.length">
        <button
          class="ml-2 bg-red-500 text-white px-2 py-1 rounded"
          @click="deleteSelectedBooks"
        >
          Delete Selected
        </button>
      </span>
    </div>
    </div>
    <div class="flex flex-wrap justify-around">
      <div
        v-for="(book, index) in books"
        :key="index"
        class="m-2 p-2 card lg:card-side bg-base-100 shadow-sm w-full sm:w-[50%] md:w-[48%] lg:w-[28%]"
      >
        <div class="flex relative">
          <img
            src="/src/assets/bookcover.jpg"
            class="w-[35%]"
            alt="Book Cover"
          />
          <div class="pl-6">
            <p>{{ book.name }}</p>
            <p class="pt-4">Author: {{ book.author }}</p>
            <p class="absolute bottom-0">Relese {{ book.date }}</p>
          </div>
      <div v-if="editMode" class="absolute flex flex-col top-2 right-2">
  <input
    type="checkbox"
    class="checkbox checkbox-error"
    :checked="selectedBooks.includes(book.id)"
    @change="toggleSelectBook(book.id)"
  />
  <button @click="openEditModal(book)">
    <img src="../assets/edit.svg" class="w-[25px]" alt="Edit" />
  </button>
</div>


            <div
              v-if="showEditModal"
              class="fixed inset-0 bg-white/20 flex items-center justify-center z-50"
            >
              <div class="bg-white p-6 rounded shadow w-80">
                <h2 class="text-lg font-bold mb-4">Edit Book</h2>
                <label class="block mb-2">
                  Name:
                  <input
                    v-model="editBook.name"
                    class="border rounded w-full px-2 py-1 mt-1"
                  />
                </label>
                <label class="block mb-2">
                  Author:
                  <input
                    v-model="editBook.author"
                    class="border rounded w-full px-2 py-1 mt-1"
                  />
                </label>
                <label class="block mb-4">
                  Date:
                  <input
                    v-model="editBook.date"
                    class="border rounded w-full px-2 py-1 mt-1"
                  />
                </label>
                <div class="flex justify-end gap-2">
                  <button
                    @click="closeEditModal"
                    class="px-3 py-1 rounded bg-gray-300"
                  >
                    Cancel
                  </button>
                  <button
                    @click="saveEdit"
                    class="px-3 py-1 rounded bg-blue-500 text-white"
                  >
                    Save
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

  <!-- Add Book Modal -->
  <div
    v-if="showAddModal"
  class="fixed inset-0 bg-black/10 flex items-center justify-center z-50"
  >
    <div class="bg-white p-6 rounded shadow w-80">
      <h2 class="text-lg font-bold mb-4">Add Book</h2>
      <label class="block mb-2">
        Name:
        <input
          v-model="newBook.name"
          class="border rounded w-full px-2 py-1 mt-1"
        />
      </label>
      <label class="block mb-2">
        Author:
        <input
          v-model="newBook.author"
          class="border rounded w-full px-2 py-1 mt-1"
        />
      </label>
      <label class="block mb-4">
        Date:
        <input
          v-model="newBook.date"
          class="border rounded w-full px-2 py-1 mt-1"
        />
      </label>
      <div class="flex justify-end gap-2">
        <button @click="closeAddModal" class="px-3 py-1 rounded bg-gray-300">
          Cancel
        </button>
        <button
          @click="saveAdd"
          class="px-3 py-1 rounded bg-green-500 text-white"
        >
          Add
        </button>
      </div>
    </div>
  </div>
</template>
<style scoped></style>
