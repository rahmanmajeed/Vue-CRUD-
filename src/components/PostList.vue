<template>
  <div class="card">

    <div class="card-body">
      <div class="float-right">
      <b-button @click="create" ref="btnShow">Create Post</b-button>
      </div>
      <POSTMODAL :formSchema="formSchema" :categories="categories" @close="close" @addCategory="addCategory"></POSTMODAL>
      <hr />
      <br>
      <table class="table table-bordered">
        <thead>
          <tr>
            <th scope="col">#</th>
            <th scope="col">Post Title</th>
            <th scope="col">Description</th>
            <th scope="col">Category</th>
            <th scope="col">Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in postList" :key="index">
            <th scope="row">{{ ++index }}</th>
            <td>{{ item.name }}</td>
            <td>{{ item.description }}</td>
            <td>{{ item.categories.toString() }}</td>
            <td>
              <button
                data-toggle="tooltip"
                data-placement="top"
                title="edit"
                class="px-md-2 btn btn-primary mx-2"
                @click="editPost(item)" ref="btnShow"
              >
                <font-awesome-icon icon="edit" />
              </button>
              <button
                data-toggle="tooltip"
                data-placement="top"
                title="delete"
                class="btn btn-danger"
                @click="deletePost(item)"
              >
                <font-awesome-icon icon="trash" />
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>
<script>
import POSTMODAL from '@/components/PostModal'
import { v1 } from 'uuid'
export default {
  components: {
    POSTMODAL
  },
  data () {
    return {
      postList: [
        {
          id: 1,
          name: 'abc',
          categories: [1, 2, 3],
          description: 'this is test post.....'
        }
      ],
      categories: [
        { value: 1, text: 'category01' },
        { value: 2, text: 'category02' },
        { value: 3, text: 'category03' }

      ],
      item: [],
      formSchema: {
        id: 0,
        name: '',
        categories: [],
        description: ''
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
    categories () {
      console.log('sense the change form root...')
    }
  },
  methods: {
    showModal () {
      // this.$refs['my-modal'].show()
      this.$root.$emit('bv::show::modal', 'my-modal', '#btnShow')
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
        id: 0,
        name: '',
        categories: [],
        description: ''
      }
      localStorage.setItem('postCreate', 'yes')
      this.showModal()
    },
    editPost (item) {
      this.formSchema = item
      localStorage.setItem('currentEdit', JSON.stringify(this.formSchema))
      this.showModal()
      console.log(this.formSchema)
    },
    deletePost (item) {
      const index = this.postList.indexOf(item)
      if (index > -1) {
        this.postList.splice(index, 1)
      }
    },
    close (item) {
      if (item.id) {
        console.log('update ')
        const currentItem = this.postList.findIndex(c => c.id === item.id)
        this.$set(this.postList, currentItem, { ...item })
        const unique = [...new Set(this.postList)]
        this.postList = unique
        this.formSchema = {
          id: 0,
          name: '',
          categories: [],
          description: ''
        }
      } else {
        console.log('create ')

        item.id = v1()
        this.formSchema = item
        this.postList.push(this.formSchema)
        this.formSchema = {
          id: 0,
          name: '',
          categories: [],
          description: ''
        }
      }

      this.formSchema = {
        id: 0,
        name: '',
        categories: [],
        description: ''
      }
      console.log(this.formSchema)
    },
    addCategory (item) {
      this.categories.push(item)
    }
  }
}
</script>
