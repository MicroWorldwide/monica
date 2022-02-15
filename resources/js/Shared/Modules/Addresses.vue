<style lang="scss" scoped>
.icon-sidebar {
  color: #737e8d;
  top: -2px;
}

.icon-note {
  top: -1px;
}
</style>

<template>
  <div class="mb-10">
    <!-- title + cta -->
    <div class="mb-3 items-center justify-between border-b border-gray-200 pb-2 sm:flex">
      <div class="mb-2 sm:mb-0">
        <span class="relative">
          <svg
            class="icon-sidebar relative inline h-4 w-4"
            viewBox="0 0 24 24"
            fill="none"
            xmlns="http://www.w3.org/2000/svg">
            <path
              d="M6 6C6 5.44772 6.44772 5 7 5H17C17.5523 5 18 5.44772 18 6C18 6.55228 17.5523 7 17 7H7C6.44771 7 6 6.55228 6 6Z"
              fill="currentColor" />
            <path
              d="M6 10C6 9.44771 6.44772 9 7 9H17C17.5523 9 18 9.44771 18 10C18 10.5523 17.5523 11 17 11H7C6.44771 11 6 10.5523 6 10Z"
              fill="currentColor" />
            <path
              d="M7 13C6.44772 13 6 13.4477 6 14C6 14.5523 6.44771 15 7 15H17C17.5523 15 18 14.5523 18 14C18 13.4477 17.5523 13 17 13H7Z"
              fill="currentColor" />
            <path
              d="M6 18C6 17.4477 6.44772 17 7 17H11C11.5523 17 12 17.4477 12 18C12 18.5523 11.5523 19 11 19H7C6.44772 19 6 18.5523 6 18Z"
              fill="currentColor" />
            <path
              fill-rule="evenodd"
              clip-rule="evenodd"
              d="M2 4C2 2.34315 3.34315 1 5 1H19C20.6569 1 22 2.34315 22 4V20C22 21.6569 20.6569 23 19 23H5C3.34315 23 2 21.6569 2 20V4ZM5 3H19C19.5523 3 20 3.44771 20 4V20C20 20.5523 19.5523 21 19 21H5C4.44772 21 4 20.5523 4 20V4C4 3.44772 4.44771 3 5 3Z"
              fill="currentColor" />
          </svg>
        </span>

        Addresses
      </div>
      <pretty-button
        :text="'Add an address'"
        :icon="'plus'"
        :classes="'sm:w-fit w-full'"
        @click="showCreateNoteModal" />
    </div>

    <!-- add a note modal -->
    <form
      v-if="createNoteModalShown"
      class="mb-6 rounded-lg border border-gray-200 bg-white"
      @submit.prevent="submit()">
      <div class="border-b border-gray-200 p-5">
        <errors :errors="form.errors" />

        <text-area
          v-model="form.body"
          :label="'Body'"
          :rows="10"
          :required="true"
          :maxlength="65535"
          :textarea-class="'block w-full mb-3'" />

        <!-- cta to add a title -->
        <span
          v-if="!titleFieldShown"
          class="inline-block cursor-pointer rounded-lg border bg-slate-200 px-1 py-1 text-xs hover:bg-slate-300"
          @click="showTitleField">
          + add title
        </span>

        <!-- title -->
        <text-input
          v-if="titleFieldShown"
          :ref="'newTitle'"
          v-model="form.title"
          :label="'Title'"
          :type="'text'"
          :input-class="'block w-full'"
          :required="false"
          :autocomplete="false"
          :maxlength="255"
          @esc-key-pressed="createNoteModalShown = false" />
      </div>

      <div class="flex justify-between p-5">
        <pretty-span :text="'Cancel'" :classes="'mr-3'" @click="createNoteModalShown = false" />
        <pretty-button :text="'Save'" :state="loadingState" :icon="'check'" :classes="'save'" />
      </div>
    </form>

    <!-- notes -->
    <div v-if="localNotes.length > 0">
      <div v-for="note in localNotes" :key="note.id" class="mb-4 rounded border border-gray-200 last:mb-0">
        <!-- body of the note, if not being edited -->
        <div v-if="editedNoteId !== note.id">
          <div v-if="note.title" class="font-semibol mb-1 border-b border-gray-200 p-3 text-xs text-gray-600">
            {{ note.title }}
          </div>

          <!-- excerpt, if it exists -->
          <div v-if="!note.show_full_content && note.body_excerpt" class="p-3">
            {{ note.body_excerpt }}
            <span class="cursor-pointer text-sky-500 hover:text-blue-900" @click="showFullBody(note)"> View all </span>
          </div>
          <!-- full body -->
          <div v-else class="p-3">
            {{ note.body }}
          </div>

          <!-- details -->
          <div
            class="flex justify-between border-t border-gray-200 px-3 py-1 text-xs text-gray-600 hover:rounded-b hover:bg-slate-50">
            <div>
              <!-- date -->
              <div class="relative mr-3 inline">
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  class="icon-note relative inline h-4 w-4 text-gray-400"
                  fill="none"
                  viewBox="0 0 24 24"
                  stroke="currentColor">
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
                </svg>
                {{ note.written_at }}
              </div>

              <!-- author -->
              <div class="relative mr-3 inline">
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  class="icon-note relative inline h-4 w-4 text-gray-400"
                  fill="none"
                  viewBox="0 0 24 24"
                  stroke="currentColor">
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M5.121 17.804A13.937 13.937 0 0112 16c2.5 0 4.847.655 6.879 1.804M15 10a3 3 0 11-6 0 3 3 0 016 0zm6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
                </svg>
                {{ note.author }}
              </div>
            </div>
            <div>
              <hover-menu
                :show-edit="true"
                :show-delete="true"
                @edit="showEditNoteModal(note)"
                @delete="destroy(note)" />
            </div>
          </div>
        </div>

        <!-- edit modal form -->
        <form
          v-if="editedNoteId === note.id"
          class="mb-6 rounded-lg border border-gray-200 bg-white"
          @submit.prevent="update(note)">
          <div class="border-b border-gray-200 p-5">
            <errors :errors="form.errors" />

            <text-area
              v-model="form.body"
              :label="'Body'"
              :rows="10"
              :required="true"
              :maxlength="65535"
              :textarea-class="'block w-full mb-3'" />

            <!-- title -->
            <text-input
              :ref="'newTitle'"
              v-model="form.title"
              :label="'Title'"
              :type="'text'"
              :input-class="'block w-full'"
              :required="false"
              :autocomplete="false"
              :maxlength="255"
              @esc-key-pressed="editedNoteId = 0" />
          </div>

          <div class="flex justify-between p-5">
            <pretty-span :text="'Cancel'" :classes="'mr-3'" @click="editedNoteId = 0" />
            <pretty-button :text="'Update'" :state="loadingState" :icon="'check'" :classes="'save'" />
          </div>
        </form>
      </div>

      <!-- view all button -->
      <div class="text-center">
        <inertia-link :href="data.url.index" class="text-sky-500 hover:text-blue-900"> View all notes </inertia-link>
      </div>
    </div>

    <!-- blank state -->
    <div v-if="localNotes.length == 0" class="mb-6 rounded-lg border border-gray-200 bg-white">
      <p class="p-5 text-center">There are no notes yet.</p>
    </div>
  </div>
</template>

<script>
import HoverMenu from '@/Shared/HoverMenu';
import PrettyButton from '@/Shared/Form/PrettyButton';
import PrettySpan from '@/Shared/Form/PrettySpan';
import TextInput from '@/Shared/Form/TextInput';
import TextArea from '@/Shared/Form/TextArea';
import Errors from '@/Shared/Form/Errors';

export default {
  components: {
    HoverMenu,
    PrettyButton,
    PrettySpan,
    TextInput,
    TextArea,
    Errors,
  },

  props: {
    data: {
      type: Object,
      default: null,
    },
  },

  data() {
    return {
      loadingState: '',
      titleFieldShown: false,
      createNoteModalShown: false,
      localNotes: [],
      editedNoteId: 0,
      form: {
        title: '',
        body: '',
        errors: [],
      },
    };
  },

  created() {
    this.localNotes = this.data.notes;
  },

  methods: {
    showCreateNoteModal() {
      this.form.title = '';
      this.form.body = '';
      this.createNoteModalShown = true;
    },

    showEditNoteModal(note) {
      this.editedNoteId = note.id;
      this.form.title = note.title;
      this.form.body = note.body;
    },

    showTitleField() {
      this.titleFieldShown = true;
      this.form.title = '';

      this.$nextTick(() => {
        this.$refs.newTitle.focus();
      });
    },

    showFullBody(note) {
      this.localNotes[this.localNotes.findIndex((x) => x.id === note.id)].show_full_content = true;
    },

    submit() {
      this.loadingState = 'loading';

      axios
        .post(this.data.url.store, this.form)
        .then((response) => {
          this.flash('The note has been created', 'success');
          this.localNotes.unshift(response.data.data);
          this.loadingState = '';
          this.createNoteModalShown = false;
        })
        .catch((error) => {
          this.loadingState = '';
          this.form.errors = error.response.data;
        });
    },

    update(note) {
      this.loadingState = 'loading';

      axios
        .put(note.url.update, this.form)
        .then((response) => {
          this.loadingState = '';
          this.flash('The note has been edited', 'success');
          this.localNotes[this.localNotes.findIndex((x) => x.id === note.id)] = response.data.data;
          this.editedNoteId = 0;
        })
        .catch((error) => {
          this.loadingState = '';
          this.form.errors = error.response.data;
        });
    },

    destroy(note) {
      if (confirm('Are you sure? This will delete the note permanently.')) {
        axios
          .delete(note.url.destroy)
          .then((response) => {
            this.flash('The note has been deleted', 'success');
            var id = this.localNotes.findIndex((x) => x.id === note.id);
            this.localNotes.splice(id, 1);
          })
          .catch((error) => {
            this.loadingState = null;
            this.form.errors = error.response.data;
          });
      }
    },
  },
};
</script>