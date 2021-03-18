<template>
  <div>

      <b-modal
        ref="my-modal"
        id="my-modal"
        :title="formTitle"
        size="lg"
        @show="resetModal"
        @hidden="resetModal"
        @ok="handleOk"
        :ok-title="btnTitle"
      >
        <!-- <b-button @click="secondModal" ref="btnSecModal">Create Category</b-button> -->
        <div>
          <form ref="form" @submit.stop.prevent="handleSubmit">
            <b-form-input
            hidden
            id="id-input"
            v-model="formSchema.id"
          ></b-form-input>
        <b-form-group
          :state="nameState"
          label="Name"
          label-for="name-input"
          invalid-feedback="Name is required"
        >
          <b-form-input
            id="name-input"
            v-model="formSchema.name"
            :state="nameState"
            required
          ></b-form-input>
        </b-form-group>

        <b-form-group
          :state="categoryState"
          label="Category"
          label-for="category-input"
          invalid-feedback="Category is required"
        >
        <b-button v-if="formSchema.id" @click="secondModal" ref="btnSecModal" class="float-right">Create Category</b-button>
        &nbsp;
        <b-form-select
        v-model="formSchema.categories"
        :options="categories"
        multiple
        :select-size="4"
        :state="categoryState"
        required
        >
        </b-form-select>
        </b-form-group>

        <b-form-group
          :state="descriptionState"
          label="Description"
          label-for="description-input"
          invalid-feedback="Description is required"
        >
          <b-form-textarea
          id="description-input"
          v-model="formSchema.description"
          placeholder="Enter description..."
          rows="3"
          max-rows="6"
          :state="descriptionState"
          required
        ></b-form-textarea>
        </b-form-group>

      </form>
        </div>
      </b-modal>

      <CategoryCreate @newCategory="setNewCategory"></CategoryCreate>
</div>
</template>

<script>
import CategoryCreate from '@/components/CategoryModal'
export default {
  components: {
    CategoryCreate
  },
  props: ['formSchema', 'categories'],
  data () {
    return {
      name: '',
      description: '',
      nameState: null,
      descriptionState: null,
      categoryState: null,
      title: '',
      selected: ['b'], // Array reference
      options: [
        { value: '', html: '<b-button @click="secondModal" ref="btnSecModal">Create Category</b-button>' },
        { value: 'a', text: 'This is First option' },
        { value: 'b', text: 'Default Selected Option' },
        { value: 'c', text: 'This is another option' },
        { value: 'd', text: 'This one is disabled', disabled: true },
        { value: 'e', text: 'This is option e' },
        { value: 'f', text: 'This is option f' },
        { value: 'g', text: 'This is option g' }
      ]
    }
  },
  watch: {
    'formSchema.name': function (newVal, oldVal) {
      // return this.name.length > 2 ? this.nameState = true : false
      if (this.formSchema.name.length > 2) {
        this.nameState = true
      } else {
        this.nameState = false
      }
    },
    'formSchema.description': function (newVal, oldVal) {
      // return this.name.length > 2 ? this.nameState = true : false
      if (this.formSchema.description.length > 2) {
        this.descriptionState = true
      } else {
        this.descriptionState = false
      }
    },
    'formSchema.categories': function (newVal, oldVal) {
      // return this.name.length > 2 ? this.nameState = true : false
      if (this.formSchema.categories !== undefined) {
        this.categoryState = true
      } else {
        this.categoryState = false
      }
    }
  },
  computed: {
    formTitle () {
      return this.formSchema.id > 0 ? 'Update Post' : 'New Post'
    },
    btnTitle () {
      return this.formSchema.id > 0 ? 'Update' : 'Create'
    }
  },
  methods: {
    showModal () {
      // this.$refs['my-modal'].show()
      this.$root.$emit('bv::show::modal', 'my-modal', '#btnShow')
    },
    secondModal () {
      localStorage.setItem('status', 'onfly')
      this.$nextTick(() => {
        this.$bvModal.hide('my-modal')
        this.$bvModal.show('modal-multi-2')
      })
    },
    checkFormValidity () {
      const valid = this.$refs.form.checkValidity()
      this.nameState = valid
      this.categoryState = valid
      this.descriptionState = valid
      return valid
    },
    resetModal () {
      this.nameState = null
      this.descriptionState = null
      this.categoryState = null
    },
    handleOk (bvModalEvt) {
      // Prevent modal from closing
      bvModalEvt.preventDefault()
      // Trigger submit handler
      this.handleSubmit()
    },
    handleSubmit () {
      // Exit when the form isn't valid
      if (!this.checkFormValidity()) {
        return
      }

      // Push the name to submitted names
      // this.submittedNames.push(this.name)
      // Hide the modal manually
      this.$nextTick(() => {
        this.$bvModal.hide('my-modal')
        this.$emit('close', this.formSchema)
      })
    },
    setNewCategory () {
      const newCategory = JSON.parse(localStorage.getItem('newCategory'))
      this.formSchema.categories.push(newCategory.value)
      this.$emit('addCategory', newCategory)
      console.log('hello from category', newCategory.value)
    }
  }
}
</script>
