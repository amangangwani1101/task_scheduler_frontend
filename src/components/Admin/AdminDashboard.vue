<template>
  <div>
    <div class="px-10 py-6 flex xl:flex-row flex-col extracss">
      <!-- <p class="text-lg">Hello {{ currentUser.name }}</p> -->
      <div
        class="left flex-initial h-full overflow-y-scroll [&::-webkit-scrollbar]:hidden px-4 xl:flex-[0.6_0.6_0%]"
      >
        <greeting-vue></greeting-vue>
      </div>
    </div>
    <AdminSectionSelector v-model="selectedOption" @change="handleSectionChange" />
    <AdminContent :selectedOption="selectedOption" :users="users" @user-deleted="updatedData" />
  </div>
</template>

<script>
import axios from 'axios'
import AdminSectionSelector from './AdminSectionSelector.vue'
import AdminContent from './AdminContent.vue'
import GreetingVue from '../dashboard/Greeting.vue'
// import { useStore } from 'vuex'
// const store = useStore()
export default {
  data() {
    return {
      currentUser: {},
      selectedOption: 'users',
      users: [],
      assignTasks: []
    }
  },
  // computed:{
  //   userName(){
  //     return store.getters.userName
  //   }
  // },
  created() {
    // this.currentUser = store.getters.userName
    this.fetchData()
  },
  methods: {
    async fetchData() {
      try {
        if (this.selectedOption === 'users') {
          const response = await axios.get('/api/admin/tasks')
          this.users = response.data.users
          console.log(this.users)
        } else if (this.selectedOption === 'assigned_tasks') {
          console.log(1)
        }
      } catch (error) {
        console.error('Error fetching data:', error)
      }
    },
    handleSectionChange(option) {
      this.selectedOption = option
      if (option == 'users') {
        // this.$router.push('/users')
        this.fetchData() // Fetch data based on the newly selected option
      }
    },
    async updatedData() {
      const response = await axios.get('/api/admin/tasks')
      this.users = response.data.users
    }
  },
  components: {
    AdminSectionSelector,
    AdminContent,
    GreetingVue
  }
}
</script>

<style scoped>
.extracss {
  display: flex;
  justify-content: center;
}
@media screen and (max-width: 1280px) {
  .extracss {
    margin: auto;
    width: 80%;
  }
}
</style>
