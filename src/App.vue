<template>
    <form class="m-4">
        <div>
            <h1 class="font-bold">Instructions</h1>

            <ol>
                <li>1. Press "Add New Cars"</li>
                <li>2. Press "Validate Form"</li>
                <li>- Since isCarBrandRequired is "false", we expect the form to be valid (good)</li>
                <li>3. Press "Toggle isCarBrandRequired"</li>
                <li>- Now isCarBrandRequired is "true" and the form should be invalid but you can see that cars is still valid. But why? The validation rules are a computed property with isCarBrandRequired as reactive dependency. 🤷</li>
                <li>4. If you change the value of isCarBrandRequired in the code to be "true" initially you will see that it works initially but if you toggle it at runtime, it doesn't work.</li>
            </ol>
        </div>

        <div class="flex gap-3 w-1/2">
            <button @click="addNewCars" type="button" class="rounded-md px-4 py-2 shadow bg-gray-200 mt-4 w-full hover:bg-gray-300">
                Add New Cars
            </button>

            <button @click="validateForm" type="button" class="rounded-md px-4 py-2 shadow bg-gray-200 mt-4 w-full hover:bg-gray-300">
                Validate Form
            </button>

            <button @click="isCarBrandRequired = !isCarBrandRequired" type="button" class="rounded-md px-4 py-2 shadow bg-gray-200 mt-4 w-full hover:bg-gray-300">
                Toggle isCarBrandRequired
            </button>
        </div>

        <div class="mb-4">
            <div class="p-2 inline-block rounded mt-2" :class="{ 'bg-yellow-100': isCarBrandRequired, 'bg-gray-200': !isCarBrandRequired}"><pre>isCarBrandRequired: {{ isCarBrandRequired }}</pre></div>

            <div class="italic text-gray-600">* If car brand is required and not filled form should be invalid, otherwise it should be valid.</div>
        </div>

        <div class="flex gap-16 items-start">
            <div>
                <b>Form:</b>
                <pre>{{ form }}</pre>
            </div>
            
            <div>
                <b>"Username" status:</b>
                
                <div
                    v-if="v$.username.$dirty"
                    class="p-2 rounded mt-2 tracking-widest"
                    :class="{ 'bg-green-100': !v$.username.$errors.length, 'bg-red-100': v$.username.$errors.length }">
                    {{ (v$.username.$errors.length) ? 'Invalid' : 'Valid' }}
                </div>
            </div>

            <div>
                <b>"Cars" status:</b>
                
                <div
                    v-if="v$.cars.$dirty"
                    class="p-2 rounded mt-2 tracking-widest"
                    :class="{ 'bg-green-100': !v$.cars.$errors.length, 'bg-red-100': v$.cars.$errors.length }">
                    {{ (v$.cars.$errors.length) ? 'Invalid' : 'Valid' }}
                </div>
            </div>

            <div>
                <b>Validator</b>
                
                <div style="font-size: 11px; max-height:400px;overflow:auto;padding: 10px;background: #f8f8f8;border-radius: 4px;font-family: monospace;"><pre>{{ v$ }}</pre></div><br><br>
            </div>
        </div>
    </form>
</template>

<script setup lang="ts">
    import { ref, reactive, computed } from 'vue'
    import { useVuelidate } from '@vuelidate/core'
    import { requiredIf, helpers } from '@vuelidate/validators'

    interface Form {
        username: string
        cars: { name: string, brand: string }[]
    }

    const isCarBrandRequired = ref(false)

    const form = reactive<Form>({
        username: '',
        cars: [],
    })

    const formRules = computed(() => {
        console.log('Recomputing formRules...')
        
        return {
            username: { required: requiredIf(isCarBrandRequired.value) },
            cars: {
                $each: helpers.forEach({
                    name: { true: () => true },
                    brand: { required: requiredIf(isCarBrandRequired.value) },
                })
            }
        }
    })

    const v$ = useVuelidate(formRules, form)

    const addNewCars = () => {
        form.cars = []

        form.cars.push({
            name: '',
            brand: '',
        })

        form.cars.push({
            name: '',
            brand: '',
        })
    }

    const validateForm = () => {
        v$.value.$validate()
    }
</script>
