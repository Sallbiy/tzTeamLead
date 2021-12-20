<template>
    <div class="container p-4">
        <div class="mx-auto p-4" style="width:50%;">
            <form @submit.prevent="addPost">
                <div class="form-group">
                    <label>Full Name</label>
                    <input v-model="post.full_name" class="form-control" aria-describedby="emailHelp"
                           placeholder="Enter full name" required>
                </div>
                <div class="form-group">
                    <label>Phone Number</label>
                    <input v-model="post.phone_number" class="form-control" type="tel"
                           pattern="[0-9]{3}-[0-9]{3}-[0-9]{2}-[0-9]{2}"
                           placeholder="+7 XXX-XX-XX" required>
                </div>
                <div class="form-group">
                        <label>Price</label>
                    <input v-model="post.order_price" class="form-control" type="number"
                           placeholder="Price " required>
                </div>
                <button type="submit" class="btn btn-primary">Submit</button>
            </form>
        </div>
        <table class="table">
            <thead>
            <tr>
                <th scope="col">ФИО</th>
                <th scope="col">Номер телефона</th>
                <th scope="col">Прайс</th>
            </tr>
            </thead>
            <tbody>
            <tr v-for="post in posts " :key="post.id">
                <td>{{ post.full_name }}</td>
                <td>{{ post.phone_number }}</td>
                <td>{{ post.order_price }}</td>
                <td>
                    <button @click="editPost(post)" class="btn btn-success">Edit</button>
                    <button @click="deletePost(post.id)" class="btn btn-danger">Delete</button>
                </td>
            </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
export default {
    name: "PostsComponent",
    data() {
        return {
            posts: [],
            post: {
                id: '',
                full_name: '',
                phone_number: '',
                order_price: 0,
            },
            checkEdit:false,
        }
    },
    mounted() {
        this.getPost();
    },
    methods: {
        getPost() {
            axios.get('/api/posts')
                .then(res => this.posts = res.data.data)
                .catch(error => {
                    console.log(error);
                })
        },
        addPost() {
            if(this.checkEdit === false){
                axios.post('/api/posts', {
                    full_name: this.post.full_name,
                    phone_number: this.post.phone_number,
                    order_price: this.post.order_price,
                })
                    .then(res => {
                        this.post.full_name = ''
                        this.post.phone_number = ''
                        this.post.order_price = ''
                        this.getPost()

                        console.log(res);
                    })
                    .catch(error => console.log(error))
            }
            else{
                axios.put(`api/posts/${this.post.id}`,{
                    full_name: this.post.full_name,
                    phone_number: this.post.phone_number,
                    order_price: this.post.order_price,
                })
                    .then(res =>{
                        this.post.full_name = ''
                        this.post.phone_number = ''
                        this.post.order_price = ''
                        this.checkEdit = false
                        this.getPost()

                        console.log(res)
                    })
                    .catch(error => console.log(error))
            }
        },
        editPost(post){
            this.checkEdit = true
            this.post.id = post.id
            this.post.full_name = post.full_name
            this.post.phone_number = post.phone_number
            this.post.order_price = post.order_price
        },
        deletePost(id) {
            axios.delete(`/api/posts/${id}`)
                .then(res => {
                    console.log(res)
                    this.getPost()
                })
                .catch(error => {
                    console.log(error);
                })
        },
    }
}
</script>

<style scoped>

</style>
