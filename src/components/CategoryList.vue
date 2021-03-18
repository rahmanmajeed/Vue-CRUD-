<template>
<div class="card">

    <div class="card-body">
      <div class="float-right">
      <b-button @click="create" ref="btnShow">Create Category</b-button>
      </div>
      <hr />
      <br>
      <table class="table table-bordered">
        <thead>
          <tr>
            <th scope="col">#</th>
            <th scope="col">Category Name</th>
            <th scope="col">Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in categories" :key="index">
            <th scope="row">{{ ++index }}</th>
            <td>{{ item.text }}</td>
            <td>
              <button
                data-toggle="tooltip"
                data-placement="top"
                title="edit"
                class="px-md-2 btn btn-primary mx-2"
                @click="editCategory(item)" ref="btnShow"
              >
                <font-awesome-icon icon="edit" />
              </button>
              <button
                data-toggle="tooltip"
                data-placement="top"
                title="delete"
                class="btn btn-danger"
                @click="deleteCategory(item)"
              >
                <font-awesome-icon icon="trash" />
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <CategoryCreate :editFormSchema="formSchema" @close="close"></CategoryCreate>
  </div>
</template>
<script>
import { v1 } from 'uuid'
import CategoryCreate from '@/components/CategoryModal'

export default {
  components: {
    CategoryCreate
  },
  data () {
    return {

      categories: [
        { value: 1, text: 'category01' },
        { value: 2, text: 'category02' },
        { value: 3, text: 'category03' }

      ],
      item: [],
      formSchema: {
        value: 0,
        text: ''
      }
    }
  },
  created () {
    if (localStorage.getItem('categories') === null) {
      localStorage.setItem('categories', JSON.stringify(this.categories))
    } else {
      this.categories = JSON.parse(localStorage.getItem('categories'))
    }
    console.log(this.categories)
  },
  watch: {

  },
  methods: {
    showModal () {
      // this.$refs['my-modal'].show()
      localStorage.setItem('status', 'onground')
      this.$root.$emit('bv::show::modal', 'modal-multi-2', '#btnShow')
      // this.$bvModal.show('modal-multi-2')
    },
    hideModal () {
      this.$refs['my-modal'].hide()
    },
    myclose () {
      this.$refs['my-modal'].show()
      this.$refs['my-second-modal'].hide()
    },
    create () {
      this.formSchema = {
        value: 0,
        text: ''
      }
      this.showModal()
    },
    editCategory (item) {
      this.formSchema = item
      localStorage.setItem('currentEdit', JSON.stringify(this.formSchema))
      this.showModal()
      console.log(this.formSchema)
    },
    deleteCategory (item) {
      const index = this.categories.indexOf(item)
      if (index > -1) {
        this.categories.splice(index, 1)
        this.localCategories = JSON.parse(localStorage.getItem('categories'))
        this.localCategories = this.categories
        localStorage.setItem('categories', JSON.stringify(this.localCategories))
      }
    },
    close (item) {
      if (item.value) {
        console.log('update', this.categories)
        const currentItem = this.categories.findIndex(c => c.id === item.value)
        this.$set(this.categories, currentItem, { ...item })
        const unique = [...new Set(this.categories)]
        this.categories = unique
        this.localCategories = JSON.parse(localStorage.getItem('categories'))
        this.localCategories = this.categories
        localStorage.setItem('categories', JSON.stringify(this.localCategories))
        this.formSchema = {
          value: 0,
          text: ''
        }
      } else {
        item.value = v1()
        this.formSchema = item
        this.categories.push(this.formSchema)

        this.localCategories = JSON.parse(localStorage.getItem('categories'))
        this.localCategories.push(this.formSchema)
        localStorage.setItem('categories', JSON.stringify(this.localCategories))
        localStorage.setItem('newCategory', JSON.stringify(this.formSchema))
      }

      console.log(this.formSchema)

      this.formSchema = {
        value: 0,
        text: ''
      }
    },
    addCategory (item) {
      // this.categories.push(item)
    }
  }
}
</script>
