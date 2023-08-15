<script setup>
  import axios from 'axios';
import axiosInstance from './axios';
  import {onMounted, ref} from 'vue';
  const events = ref([])

  const inputTitle = ref('')
  const inputDesc = ref('')
  const inputPrice = ref('')
  const inputImage = ref('')
  const inputDate = ref('')

  const idEvent = ref('')
  const isUpdated = ref(false)

  // get data 
  const getData = async() => {
    const res = await axiosInstance.get('/event');
    // console.log(res.data);
    events.value = res.data
  }

  onMounted(() => {
    getData();
  }) 

  // post data 
  const handleSubmit = async () => {
   try {
    if(isUpdated.value == false){
      await axiosInstance.post('/event', {
        title: inputTitle.value,
        description: inputDesc.value,
        price: inputPrice.value,
        image: inputImage.value,
        date: inputDate.value
      })
    }else{
      await axiosInstance.put('/event/' + idEvent.value, {
        title: inputTitle.value,
        description: inputDesc.value,
        price: inputPrice.value,
        image: inputImage.value,
        date: inputDate.value
      })
    }
   } catch (error) {
    console.log(error);
   }

    getData();
    clearInput();
    // alert('Data has been added successfully')
  }

  const clearInput = () => {
    inputTitle.value = ''
    inputDesc.value = ''
    inputPrice.value = ''
    inputImage.value = ''
    inputDate.value = ''
    idEvent.value = ''
    isUpdated.value = false
  }

  // delete data 
  const deleteEvent = async (id) => {
    const confirmation = confirm('Are you sure to delete this data?')
  
    confirmation ? await axiosInstance.delete('/event/' + id) : null
    // alert('Data has been deleted successfully')
    getData();
  }

  // edit data
  const editEvent = (objEvent) => {
    inputTitle.value = objEvent.title
    inputDesc.value = objEvent.description
    inputPrice.value = objEvent.price
    inputImage.value = objEvent.image
    inputDate.value = objEvent.date
    idEvent.value = objEvent.id
    isUpdated.value = true
  }


</script>

<template>  
  <div class="flex justify-center mt-5 flex-col">
    <div class="flex justify-center">
        <input type="text" placeholder="Search your meals here" class="form-control p-3 bg-slate-100 rounded w-3/4 placeholder:text-center text-lg">
    </div> 

    <div class="flex justify-center">
        <table class="mt-5 w-3/4">
            <thead>
                <tr>
                <th class="border px-4 py-2">title</th>
                <th class="border px-4 py-2">description</th>
                <th class="border px-4 py-2">price</th>
                <th class="border px-4 py-2">Image</th>
                <th class="border px-4 py-2">Date</th>
                <th class="px-4 py-2">Action</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="event in events" :key="event.id">
                <td class="border px-4 py-2">{{ event.title }}</td>
                <td class="border px-4 py-2">{{ event.description }}</td>
                <td class="border px-4 py-2">{{ event.price }}</td>
                <td class="border px-4 py-2">{{ event.image }}</td>
                <td class="border px-4 py-2">{{ event.date }}</td>
                <td class="flex justify-center gap-4">
                    <button class="p-3 bg-blue-400 rounded" value="update" @click="editEvent(event)">Update</button>
                    <button class="p-3 bg-red-400 rounded" value="delete" @click="deleteEvent(event.id)">delete</button>
                </td>
                </tr>
            </tbody>
    </table>
    </div>

    <div class="mt-3 flex justify-center">
        <form class="flex flex-col" @submit.prevent="handleSubmit">
            <label class="me-4">title</label>
            <input v-model="inputTitle" type="text" class="bg-slate-200 rounded p-2">
            <label class="me-4">description</label>
            <input v-model="inputDesc" type="text" class="bg-slate-200 rounded p-2" >
            <label class="me-4">price</label>
            <input v-model="inputPrice" type="number" class="bg-slate-200 rounded p-2">
            <label class="me-4">image</label>
            <input v-model="inputImage" type="text" class="bg-slate-200 rounded p-2">
            <label class="me-4">date</label>
            <input v-model="inputDate" type="text" class="bg-slate-200 rounded p-2">
            <div class="flex justify-center">

              <button class="bg-blue-800 text-white font-bold rounded mt-3 p-1 me-4" type="submit"
              :disabled="inputTitle === '' || inputDesc === '' || inputPrice === '' || inputImage === '' || inputDate === ''"
            :class="isUpdated ? 'bg-greeen-500' : 'bg-blue-800'"
            >
              {{ isUpdated ? 'Update' : 'Add Event' }}
            </button>

            <button class="bg-red-500 text-white font-bold rounded mt-3 p-1 me-4" 
            type="button"
            @click="clearInput"
            v-if="isUpdated"
            >
              Cancel
            </button>
            </div>
        </form>
    </div>
 </div>
</template>


