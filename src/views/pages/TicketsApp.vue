<script setup lang="ts">
import DefaultCard from '@/components/DefaultCard.vue'

import { ref, onMounted, watch } from 'vue';
import { useForm } from 'vee-validate'
import * as yup from 'yup';
import { Form, Field, ErrorMessage } from 'vee-validate';
import { useField } from 'vee-validate';
import { reactive } from 'vue';

const schema: yup.SchemaOf<any> = yup.object({
    name: yup.string().required('Nombre es requerido'),
    email: yup.string().required().email(),
    nameTicket: yup.string().required('Nombre es requerido'),
    typeService: yup.string().required('Seleccione tipo de servicio'),
    descriptionTicket: yup.string().required('Descripción es requerido'),
    client: yup.string().required(),
    identification: yup.string().required(),
    phone: yup.string().required().matches(/^[0-9-]{7,}$/),
    password: yup.string().required().min(8),
    addressclient: yup.string().required(),
    bill: yup.string().required('Número de factura requerido'),
    product: yup.string().required(),
    dateBill: yup.string().required(),
});
const form = ref(null)


let { value: name } = useField<string>('name');
let { value: email } = useField<string>('email');
let { value: nameTicket } = useField<any>('nameTicket');
let { value: typeService } = useField<any>('typeService');
let { value: descriptionTicket } = reactive(useField<any>('descriptionTicket'));
let { value: client } = useField<string>('client');
let { value: identification } = useField<any>('identification');
let { value: phone } = useField<any>('phone');
let { value: password } = reactive(useField<any>('password'));
let { value: addressclient } = useField<string>('addressclient');
let { value: bill } = useField<any>('bill');
let { value: product } = useField<any>('product')
let { value: dateBill } = useField('dateBill')
const typeServiceSelected = ref('')
let validUserTicket: any = ref(false)
const typeS: any = ref([
    {
        id: 0,
        name: 'Seleccionar servicio'
    },
    {
        id: 1,
        name: 'Instalación'
    }, {
        id: 2,
        name: 'Garantía'
    }
])
let productsList = ref([])
function submit() {
    console.log('submit formbg')
}


watch(bill, async (newBill, oldbill) => {

    if (newBill.length == 7) {
        const baseUrl = `${import.meta.env.VITE_API_URL}/`;
        console.log('baseUrl', baseUrl)
        validUserTicket.value = false
        try {
            const res: any = await fetch(`${baseUrl}api/facturaCliente/${newBill}`).then(response => {

                response.json().then((data) => {
                    console.log('response', data)
                    if (data.billingUser[0].length) {
                        validUserTicket.value = true
                        productsList.value.push({
                            id: 0,
                            name: data.billingUser[0][0].Producto
                        })
                        identification.value = data.billingUser[0][0].CodCliente
                        client.value = data.billingUser[0][0].Cliente
                        dateBill.value = data.billingUser[0][0].Fecha.split('T')[0]
                    }

                });
            })



        } catch (error) {
            console.log('error', error)
        } finally {
            console.log('finally')
        }
    }
})

const pageTitle = ref('Crear Ticket')
</script>

<template>
    <div class="shadow-inner p-4">
        <DefaultCard cardTitle="Datos Generales">
            <Form @submit="submit" :validation-schema="schema"
                class="flex flex-row gap-5.5 p-6.5 justify-between px-2 py-2 flex-wrap">

                <div class="flex flex-col">
                    <label class="mb-3 block text-sm font-medium text-black dark:text-white">
                        Nombre
                    </label>
                    <Field name="nameTicket" as="input"
                        class="w-full rounded-lg border-[1px] border-[#dee2e6] border-stroke text-black border-stroke bg-transparent py-3 px-5 font-normal outline-none transition focus:border-primary active:border-primary disabled:cursor-default disabled:bg-whiter dark:text-white dark:border-form-strokedark dark:bg-form-input dark:focus:border-primary" />
                    <ErrorMessage name="nameTicket" class="text-[#c82a08] text-sm" />
                </div>

                <div class="flex flex-col">
                    <label class="mb-3 block text-sm font-medium text-black dark:text-white">
                        Tipo de Servicio
                    </label>
                    <Field name="typeService" as="select" placeholder="Seleccione el tipo de servicio"
                        class="w-full rounded-lg border-[1px] border-[#dee2e6] border-stroke text-black bg-transparent py-3 px-5 font-normal outline-none transition">
                        <option v-for="category in typeS" :key="category.id" :value="category.id" class="w-full">
                            {{ category.name }}
                        </option>
                    </Field>
                    <ErrorMessage name="typeService" class="text-[#c82a08] text-sm" />
                </div>

                <div class="flex flex-col">
                    <label class="mb-3 block text-sm font-medium text-black dark:text-white">
                        Factura
                    </label>
                    <Field name="bill" v-model="bill"
                        class="w-full rounded-lg border-[1px] border-stroke text-black border-stroke bg-transparent py-3 px-5 font-normal outline-none transition active:border-primary disabled:cursor-default disabled:bg-whiter dark:text-white dark:border-form-strokedark dark:bg-form-input dark:focus:border-primary" />
                    <ErrorMessage name="bill" class="text-[#c82a08] text-sm" />
                </div>

                <div class="flex flex-col">
                    <label class="mb-3 block text-sm font-medium text-black dark:text-white">
                        Fecha factura
                    </label>

                    <div class="relative max-w-sm">
                        <div class="absolute inset-y-0 start-0 flex items-center ps-3.5 pointer-events-none">
                            <svg class="w-4 h-4 text-gray-500 dark:text-gray-400" aria-hidden="true"
                                xmlns="http://www.w3.org/2000/svg" fill="currentColor" viewBox="0 0 20 20">
                                <path
                                    d="M20 4a2 2 0 0 0-2-2h-2V1a1 1 0 0 0-2 0v1h-3V1a1 1 0 0 0-2 0v1H6V1a1 1 0 0 0-2 0v1H2a2 2 0 0 0-2 2v2h20V4ZM0 18a2 2 0 0 0 2 2h16a2 2 0 0 0 2-2V8H0v10Zm5-8h10a1 1 0 0 1 0 2H5a1 1 0 0 1 0-2Z" />
                            </svg>
                        </div>
                        <Field name="dateBill" v-slot="{ field }" v-model="dateBill">
                            <input datepicker v-bind="field" type="date"
                                class="w-full min-w-[223px] rounded-lg border-[1px] border-stroke text-black border-stroke bg-transparent py-3 px-5 font-normal outline-none transition active:border-primary disabled:cursor-default disabled:bg-whiter dark:text-white dark:border-form-strokedark dark:bg-form-input dark:focus:border-primary"
                                placeholder="Select date">
                        </Field>
                    </div>

                    <ErrorMessage name="dateBill" class="text-[#c82a08] text-sm" />
                </div>
                <div>
                    <div class="flex flex-col">
                        <label class="mb-3 block text-sm font-medium text-black dark:text-white">
                            Descripción
                        </label>
                        <Field as="textarea" name="descriptionTicket" id="description" cols="30" rows="2"
                            class="w-full rounded-lg border-[1px] border-[#dee2e6] border-stroke text-black border-stroke bg-transparent py-3 px-5 font-normal outline-none transition focus:border-primary active:border-primary disabled:cursor-default disabled:bg-whiter dark:text-white dark:border-form-strokedark dark:bg-form-input dark:focus:border-primary" />
                        <ErrorMessage name="descriptionTicket" class="text-[#c82a08] text-sm" />
                    </div>
                </div>

                <div>

                </div>
            </Form>

        </DefaultCard>
    </div>
    <div class="shadow-inner p-4" v-if="validUserTicket">
        <DefaultCard cardTitle="Datos del cliente">
            <Form @submit="submit" :validation-schema="schema"
                class="flex flex-row gap-5 p-6.5 px-2 py-2 w-full flex-wrap">

                <div class="flex flex-col">
                    <label class="mb-3 block text-sm font-medium text-black dark:text-white">
                        Nombre del cliente
                    </label>
                    <Field name="client" v-model="client"
                        class="w-full rounded-lg border-[1px] text-black border-stroke bg-transparent py-3 px-5 font-normal outline-none transition focus:border-primary active:border-primary disabled:cursor-default disabled:bg-whiter dark:text-white dark:border-form-strokedark dark:bg-form-input dark:focus:border-primary" />
                    <ErrorMessage name="client" class="text-[#c82a08] text-sm" />
                </div>

                <div class="flex flex-col">
                    <label class="mb-3 block text-sm font-medium text-black dark:text-white">
                        Cédula
                    </label>
                    <Field name="identification" v-model="identification"
                        class="w-full rounded-lg border-[1px] text-black border-stroke bg-transparent py-3 px-5 font-normal outline-none transition focus:border-primary active:border-primary disabled:cursor-default disabled:bg-whiter dark:text-white dark:border-form-strokedark dark:bg-form-input dark:focus:border-primary" />
                    <ErrorMessage name="identification" class="text-[#c82a08] text-sm" />
                </div>

                <div class="flex flex-col">
                    <label class="mb-3 block text-sm font-medium text-black dark:text-white">
                        Producto
                    </label>
                    <Field name="product" as="select" placeholder="Seleccione el tipo de servicio"
                        class="w-full rounded-lg border-[1px]  border-[#dee2e6] border-stroke text-black border-stroke bg-transparent py-3 px-5 font-normal outline-none transition focus:border-primary active:border-primary disabled:cursor-default disabled:bg-whiter dark:text-white dark:border-form-strokedark dark:bg-form-input dark:focus:border-primary">
                        <option v-for="category in productsList" :key="category.id" :value="category.id" class="w-full">
                            {{ category.name }}
                        </option>
                    </Field>
                    <ErrorMessage name="product" class="text-[#c82a08] text-sm" />
                </div>


                <div class="flex flex-col">
                    <label class="mb-3 block text-sm font-medium text-black dark:text-white">
                        Dirección
                    </label>
                    <Field name="address" v-model="address"
                        class="w-full rounded-lg border-[1px] border-stroke text-black border-stroke bg-transparent py-3 px-5 font-normal outline-none transition active:border-primary disabled:cursor-default disabled:bg-whiter dark:text-white dark:border-form-strokedark dark:bg-form-input dark:focus:border-primary" />

                    <ErrorMessage name="address" class="text-[#c82a08] text-sm" />
                </div>
                <div>

                </div>
            </Form>
        </DefaultCard>

    </div>

    <div class="shadow-inner p-4" v-if="validUserTicket">
        <DefaultCard cardTitle="Datos del servicio">
            <Form @submit="submit" :validation-schema="schema"
                class="flex flex-row gap-5 p-6.5 px-2 py-2 w-full flex-wrap">

                <div class="flex flex-col">
                    <label class="mb-3 block text-sm font-medium text-black dark:text-white">
                        Nombre de contacto
                    </label>
                    <Field name="client" v-model="client"
                        class="w-full rounded-lg border-[1px] text-black border-stroke bg-transparent py-3 px-5 font-normal outline-none transition focus:border-primary active:border-primary disabled:cursor-default disabled:bg-whiter dark:text-white dark:border-form-strokedark dark:bg-form-input dark:focus:border-primary" />
                    <ErrorMessage name="client" class="text-[#c82a08] text-sm" />
                </div>

                <div class="flex flex-col">
                    <label class="mb-3 block text-sm font-medium text-black dark:text-white">
                        Cédula
                    </label>
                    <Field name="identification" v-model="identification"
                        class="w-full rounded-lg border-[1px] text-black border-stroke bg-transparent py-3 px-5 font-normal outline-none transition focus:border-primary active:border-primary disabled:cursor-default disabled:bg-whiter dark:text-white dark:border-form-strokedark dark:bg-form-input dark:focus:border-primary" />
                    <ErrorMessage name="identification" class="text-[#c82a08] text-sm" />
                </div>

                <div class="flex flex-col">
                    <label class="mb-3 block text-sm font-medium text-black dark:text-white">
                        Producto
                    </label>
                    <Field name="product" as="select" placeholder="Seleccione el tipo de servicio"
                        class="w-full rounded-lg border-[1px]  border-[#dee2e6] border-stroke text-black border-stroke bg-transparent py-3 px-5 font-normal outline-none transition focus:border-primary active:border-primary disabled:cursor-default disabled:bg-whiter dark:text-white dark:border-form-strokedark dark:bg-form-input dark:focus:border-primary">
                        <option v-for="category in productsList" :key="category.id" :value="category.id" class="w-full">
                            {{ category.name }}
                        </option>
                    </Field>
                    <ErrorMessage name="product" class="text-[#c82a08] text-sm" />
                </div>

                <div class="flex flex-col">
                    <label class="mb-3 block text-sm font-medium text-black dark:text-white">
                        Dirección
                    </label>
                    <Field name="address" v-model="address"
                        class="w-full rounded-lg border-[1px] border-stroke text-black border-stroke bg-transparent py-3 px-5 font-normal outline-none transition active:border-primary disabled:cursor-default disabled:bg-whiter dark:text-white dark:border-form-strokedark dark:bg-form-input dark:focus:border-primary" />

                    <ErrorMessage name="address" class="text-[#c82a08] text-sm" />
                </div>
                <div>

                </div>
            </Form>
        </DefaultCard>

    </div>
</template>
