<style lang="scss" scoped>
pre {
  background-color: #1f2937;
  color: #c9ef78;
}

.example {
  border-bottom-left-radius: 9px;
  border-bottom-right-radius: 9px;
}
</style>

<template>
  <div>
    <!-- title + cta -->
    <div class="sm:flex items-center justify-between mb-3 sm:mt-0 mt-8">
      <h3 class="mb-4 sm:mb-0">
        <span class="mr-1">
          👉
        </span> Customize how contacts should be displayed
      </h3>
      <pretty-button :text="'Edit'" @click="enableEditMode" />
    </div>

    <!-- help text -->
    <div class="px-3 py-2 border mb-6 flex rounded text-sm bg-slate-50">
      <svg xmlns="http://www.w3.org/2000/svg" class="grow h-6 pr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
      </svg>

      <div>
        <p>You can customize how contacts should be displayed according to your own taste/culture. Perhaps you would want to use James Bond instead of Bond James. Here, you can define it at will.</p>
      </div>
    </div>

    <!-- normal mode -->
    <div v-if="!editMode" class="bg-white border border-gray-200 rounded-lg mb-6">
      <p class="px-5 py-2 border-b border-gray-200">
        <span class="mb-2 block">Current way of displaying contact names:</span>
        <pre class="px-5 py-2 text-sm rounded block mb-2">{{ localNameOrder }}</pre>
      </p>
      <p class="px-5 py-2 text-sm bg-orange-50 font-medium example"><span class="font-light">Contacts will be shown as follow:</span> {{ localNameExample }}</p>
    </div>

    <!-- edit mode -->
    <form v-if="editMode" class="bg-white border border-gray-200 rounded-lg mb-6" @submit.prevent="submit()">
      <div class="px-5 py-2 border-b border-gray-200">
        <errors :errors="form.errors" />

        <div class="flex items-center mb-2">
          <input id="first_name_last_name" v-model="form.nameOrder" value="%first_name% %last_name%" name="name-order" type="radio"
                 class="h-4 w-4 text-sky-500 border-gray-300"
          >
          <label for="first_name_last_name" class="ml-3 block text-sm font-medium text-gray-700 cursor-pointer">
            First name Last name <span class="text-gray-500 font-normal ml-4">
              James Bond
            </span>
          </label>
        </div>
        <div class="flex items-center mb-2">
          <input id="last_name_first_name" v-model="form.nameOrder" value="%last_name% %first_name%" name="name-order" type="radio"
                 class="h-4 w-4 text-sky-500 border-gray-300"
          >
          <label for="last_name_first_name" class="ml-3 block text-sm font-medium text-gray-700 cursor-pointer">
            Last name First name <span class="text-gray-500 font-normal ml-4">
              Bond James
            </span>
          </label>
        </div>
        <div class="flex items-center mb-2">
          <input id="first_name_last_name_surname" v-model="form.nameOrder" value="%first_name% %last_name% (%surname%)" name="name-order" type="radio"
                 class="h-4 w-4 text-sky-500 border-gray-300"
          >
          <label for="first_name_last_name_surname" class="ml-3 block text-sm font-medium text-gray-700 cursor-pointer">
            First name Last name (Surname) <span class="text-gray-500 font-normal ml-4">
              James Bond (007)
            </span>
          </label>
        </div>
        <div class="flex items-center mb-2">
          <input id="surname" v-model="form.nameOrder" value="%surname%" name="name-order" type="radio"
                 class="h-4 w-4 text-sky-500 border-gray-300"
          >
          <label for="surname" class="ml-3 block text-sm font-medium text-gray-700 cursor-pointer">
            Surname <span class="text-gray-500 font-normal ml-4">
              007
            </span>
          </label>
        </div>
        <div class="flex items-center mb-2">
          <input id="custom" name="name-order" type="radio" class="h-4 w-4 text-sky-500 border-gray-300" @click="focusNameOrder">
          <label for="custom" class="ml-3 block text-sm font-medium text-gray-700 cursor-pointer">
            Custom name order
          </label>
        </div>
        <div class="ml-8">
          <text-input :ref="'nameOrder'"
                      v-model="form.nameOrder" :type="'text'"
                      :autofocus="true"
                      :input-class="'block w-full'"
                      :div-outer-class="'block mb-2'"
                      :disabled="disableNameOrder"
                      :autocomplete="false"
                      :maxlength="255"
          />

          <p class="mb-4 text-sm">Please read <a href="https://www.notion.so/monicahq/Customize-your-account-8e015b7488c143abab9eb8a6e2fbca77#b3fd57def37445f4a9cf234e373c52ca" target="_blank" class="text-sky-500 hover:text-blue-900">our documentation</a> to know more about this feature, and which variables you have access to.</p>
        </div>
      </div>

      <div class="p-5 flex justify-between">
        <pretty-link :text="'Cancel'" :classes="'mr-3'" @click="editMode = false" />
        <pretty-button :text="'Save'" :state="loadingState" :icon="'check'" :classes="'save'" />
      </div>
    </form>
  </div>
</template>

<script>
import PrettyButton from '@/Shared/PrettyButton';
import PrettyLink from '@/Shared/PrettyLink';
import TextInput from '@/Shared/TextInput';
import Errors from '@/Shared/Errors';

export default {
  components: {
    PrettyButton,
    PrettyLink,
    TextInput,
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
      editMode: false,
      localNameOrder: '',
      localNameExample: '',
      disableNameOrder: true,
      form: {
        nameOrder: '',
        choice: '',
        errors: [],
      },
    };
  },

  mounted() {
    this.localNameOrder = this.data.name_order;
    this.localNameExample = this.data.name_example;
    this.form.nameOrder = this.data.name_order;
  },

  methods: {
    enableEditMode() {
      this.editMode = true;
    },

    setNameOrder() {
      this.disableNameOrder = true;
      this.form.nameOrder = this.form.choice;
    },

    focusNameOrder() {
      this.disableNameOrder = false;

      this.$nextTick(() => {
        this.$refs.nameOrder.focus();
      });
    },

    submit() {
      this.loadingState = 'loading';

      axios.post(this.data.url.store, this.form)
        .then(response => {
          this.flash('Changes saved', 'success');
          this.localNameOrder = this.form.nameOrder;
          this.localNameExample = response.data.data.name_example;
          this.choice = this.form.nameOrder;
          this.editMode = false;
          this.loadingState = null;

        })
        .catch(error => {
          this.loadingState = null;
          this.form.errors = error.response.data;
        });
    },
  },
};
</script>