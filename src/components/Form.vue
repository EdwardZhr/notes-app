<script setup>
import { ref, watchEffect } from 'vue'
import {Form, FormItem, Input, Textarea} from 'ant-design-vue'

const props = defineProps({
    titleValue: String,
    descriptionValue: String,
})

const emits = defineEmits(['input'])

const title = ref('')
const description = ref('')

watchEffect(() => {
    title.value = props.titleValue
    description.value = props.descriptionValue
})



</script>

<template>
    <div>
        <Form  class="form">
            <FormItem label="Название заметки">
                <Input v-model:value="title" @input="$event => $emit('input', {title, description})"/>
            </FormItem>
            <FormItem label="Описание заметки" >
                <Textarea v-model:value="description" @input="$event => $emit('input', {title, description})"/>
            </FormItem>
        </Form>
    </div>
</template>

<style scoped>
.form {
    margin-top: 30px;
}
</style>
