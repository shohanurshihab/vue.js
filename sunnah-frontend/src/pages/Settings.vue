<script setup>
import axios from 'axios'
import { ref, onMounted } from 'vue';
import { useRoute, useRouter } from 'vue-router'

const route = useRoute()
const router = useRouter()

const userData = ref({
    Id: '',
    Name: '',
    Email: '',
    Address: '',
    Phone: '',
    Dob: '',
    Image: '',
    Password: '',
    
})
const usid = ref({
    Id: ''
})
const typeMap = {
    'Admin': 0,
    'Manager': 1,
    'Employee': 2,
    'Customer': 3
}

const handleUpdate = () => {
    if (Object.keys(typeMap).includes(userData.value.Type)) {
        userData.value.Type = typeMap[userData.value.Type]
    }
    axios.post(`/api/user/update`, userData.value)
        .then(() => window.location.reload())
        .catch(error => console.log(error.response))
}


const uid = () => {
     return new Promise((resolve, reject) => {
    const userEmail = localStorage.getItem('userEmail')
         if (localStorage.getItem('authToken')) {
             axios.get(`api/user?email=${userEmail}`)
                 .then(response => {
                     usid.value.Id = response.data.Id
                     resolve(usid.value.Id)
                     // console.log(usid.value.Id)
                 }
                 )
                 // .then(response =>console.log(response.data.Id))
                 .catch(error => console.log(error.response))
         }
    })
}

const fetchProduct = async () => {
    await uid()
    const userEmail = localStorage.getItem('userEmail')
    if (localStorage.getItem('authToken')) {
        console.log(usid.value.Id)
        axios.get(`api/user/${usid.value.Id}`)
            .then(response => userData.value =response.data)
            .catch(error => console.log(error.response))
    }
}
const handleFileUpload = (event) => {
    const file = event.target.files[0];
    const reader = new FileReader();
    reader.readAsDataURL(file);
    reader.onload = () => {
        userData.value.Image = reader.result;
    };
};
onMounted(() => uid())
onMounted(() => fetchProduct())

</script>

<template>
    <div class="container">
        <div class="row"> 
            
               
              
            <div class="col-md-8">
                
                     <form @submit.prevent="handleUpdate" class="update-form" method="post">
                         <div class="profile-pic-container">
                        <img :src="userData.Image" alt="Profile Pic" >
                        <input type="file" name="Image" @change="handleFileUpload">
                    </div>
                    <input type="hidden" name="Id" v-model="userData.Id">
                    <label>Name:</label>
                    <input type="text" name="Name" v-model="userData.Name">
                    <label>Email:</label>
                    <input type="email" name="Email" v-model="userData.Email">
                    <label>Date of Birth:</label>
                    <input type="text" name="Dob" v-model="userData.Dob">
                    <label>Phone:</label>
                    <input type="tel" name="Phone" v-model="userData.Phone">
                    <label>Type:</label>
                    <input type="text" name="Type" v-model="userData.Type">
                    <label>Address:</label>
                    <textarea name="Address" v-model="userData.Address"></textarea>
                    <button type="submit" class="btn">Update Profile</button>
               </form> 
            </div> 
       
        
        </div>
    </div>
</template>



<style>
.container{
    background: #eef3fe47;
    width: 50%;
    margin-left: 25%;
    margin-top: -10%;
}
.profile-pic-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-bottom: 1rem;
}

.profile-pic-container img {
    width: 150px;
    height: 150px;
    object-fit: cover;
    border-radius: 50%;
}

.update-form {
    display: flex;
    flex-direction: column;
    align-items: center;
}

label {
    margin-bottom: 0.5rem;
}

input,
textarea {
    width: 50%;
    padding: 0.5rem;
    margin-bottom: 1rem;
}

.btn {
    padding: 0.5rem 1rem;
    background-color: #007bff;
    color: #fff;
    border: none;
    border-radius: 4px;
    cursor: pointer;
}
</style>
