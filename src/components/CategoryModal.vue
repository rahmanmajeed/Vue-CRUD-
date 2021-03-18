<template>
  <div>
       <b-modal
       id="modal-multi-2"
       ref="my-second-modal"
       :title="formTitle"
       @show="resetModal"
       @hidden="resetModal"
       @ok="handleOk"
       :ok-title="title"
       >
        <div>
          <form ref="form" @submit.stop.prevent="handleSubmit">
            <b-form-group
              :state="nameState"
              label="Category Name"
              label-for="name-input"
              invalid-feedback="Name is required"
            >
              <b-form-input
                id="name-input"
                v-model="formSchema.text"
                :state="nameState"
                required
              ></b-form-input>
            </b-form-group>

      </form>
        </div>
      </b-modal>
</div>
</template>

<script>
import { v1 } from 'uuid'
export default {
  components: {},
  props: ['editFormSchema'],
  data () {
    return {
      title: 'Save',
      name: '',
      nameState: null,
      localCategories: [],
      formSchema: {
        value: 0,
        text: ''
      }
    }
  },
  computed: {
    formTitle () {
      return this.formSchema.value > 0 ? 'Update Category' : 'New Category'
    }
  },
  watch: {
    editFormSchema () {
      this.formSchema = this.editFormSchema
    }
  },
  methods: {
    checkFormValidity () {
      const valid = this.$refs.form.checkValidity()
      this.nameState = valid
      return valid
    },
    resetModal () {
      this.nameState = null
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
        return false
      } else {
        const localStatus = localStorage.getItem('status')
        if (localStatus === 'onground') {
          this.$emit('close', this.formSchema)
        } else if (localStatus === 'onfly') {
          this.formSchema.value = v1()
          this.localCategories = JSON.parse(localStorage.getItem('categories'))
          this.localCategories.push(this.formSchema)
          localStorage.setItem('categories', JSON.stringify(this.localCategories))
          localStorage.setItem('newCategory', JSON.stringify(this.formSchema))
          console.log(JSON.parse(localStorage.getItem('categories')))

          this.$emit('newCategory')
        }

        this.myclose()
      }
    },
    myclose () {
      this.$nextTick(() => {
        this.$bvModal.show('my-modal')
        this.$bvModal.hide('modal-multi-2')
      })
    }
  }
}
</script>
