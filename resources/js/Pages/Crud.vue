<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { Head } from '@inertiajs/vue3';
import axios from 'axios';

</script>
<template>
     <Head title="Dashboard" />
     <AuthenticatedLayout>
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">Dashboard</h2>
        </template>
        <div class="py-12">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg">
                    <div class="p-6 text-gray-900">
                        <table>
                           
                        </table>
                    </div>
                </div>
            </div>
        </div>
    </AuthenticatedLayout>
</template>

<script>
    export default {
            data(){
                return{
                    datos:[]
                }
            },
            methods:{
                    getProductos(){
                        axios.get("productos").then(response=>{
                                //this.datos = response.data
                                console.log( Object.entries(response.data))
                            }).catch(error=>{
                            console.log(error)
                        })
                                    }
            },
            mounted(){
                    this.getProductos()
            }
        }

      
</script>