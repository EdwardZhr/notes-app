<script setup>
import { ref, createVNode, watchEffect, onMounted } from 'vue'
import Form from './components/Form.vue'
import { Button, PageHeader, Modal, List, ListItem, ListItemMeta } from 'ant-design-vue'
import { ExclamationCircleOutlined } from '@ant-design/icons-vue';

const notes = ref([]);
const titleValue = ref('');
const descriptionValue = ref('');
const indexValue = ref(0);
const isCreateModalOpen = ref(false);
const isEditModalOpen = ref(false);

function handleCreateBtnClick() {
    isCreateModalOpen.value = true
    titleValue.value = '';
    descriptionValue.value = '';
}

// Отлавливаем изменения в инпутах
function handleChangeNoteData({title, description}) {
    titleValue.value = title;
    descriptionValue.value = description;
};

function handleCreateNote() {
    notes.value.push({
        title: titleValue.value, 
        description: descriptionValue.value,
        index: notes.value.length
    })
    isCreateModalOpen.value = false;
    updateLocalStorage();
};

function handleEditBtnClick(title, description, index) {
    isEditModalOpen.value = true
    titleValue.value = title
    descriptionValue.value = description
    indexValue.value = index
};

function handleEditNote() {
    notes.value[indexValue.value].title = titleValue.value
    notes.value[indexValue.value].description = descriptionValue.value
    isEditModalOpen.value = false
    updateLocalStorage();
};

function handleDeletetNote(index) {
    notes.value.splice(index, 1);
};

function updateLocalStorage() {
    const notesJson = JSON.stringify(notes.value);
    localStorage.setItem('notes', notesJson);
}

onMounted(() => {
    const notesJson = localStorage.getItem('notes');
    if (notesJson) {
      notes.value = JSON.parse(notesJson);
    }
})

function showDeleteConfirm(index) {
    Modal.confirm({
        title: () => 'Удалить заметку?',
        icon: () => createVNode(ExclamationCircleOutlined),
        keyboard: true,
        okText: () => 'Да',
        okType: 'danger',
        cancelText: () => 'Нет',
        onOk() {
            handleDeletetNote(index)
            // Не разобрался, почему модальное окно подтверждения не закрывается и закрываю его сам
            document.querySelectorAll('.ant-modal-root')[1].parentElement.remove()
        },
        onCancel() {
            document.querySelectorAll('.ant-modal-root')[1].parentElement.remove()
        },
    });
};  
</script>

<template>
    <header>
        <PageHeader title="Мои заметки" style="padding: 0"/>
    </header>

    <main>
        <Button type="primary" style="margin-top: 20px" @click="handleCreateBtnClick">Создать заметку</Button>

        <List class="notes-list" :data-source="notes" style="max-width: 800px;">
            <template #renderItem="{ item, index }">
                <ListItem>
                    <template #actions>
                        <a @click="handleEditBtnClick(item.title, item.description, index)">Редактировать</a>
                        <a @click="showDeleteConfirm(index)" style="color: red;">Удалить</a>
                    </template>
                    <ListItemMeta :description= "item.description" style="word-wrap: break-word;">
                        <template #title>
                            <span>{{ item.title }}</span>
                        </template>
                    </ListItemMeta>
                </ListItem>
            </template>
        </List>
        
        <Modal v-model:open="isCreateModalOpen" title="Создать заметку" cancelText="Отмена"	okText="Создать" @ok="handleCreateNote" :ok-button-props="{ disabled: !Boolean(titleValue) || !Boolean(descriptionValue) }">
            <Form @input="handleChangeNoteData" :titleValue="titleValue" :descriptionValue="descriptionValue"/>
        </Modal>
        
        <Modal v-model:open="isEditModalOpen" title="Редактировать заметку" cancelText="Отмена"	okText="Сохранить" @ok="handleEditNote" :ok-button-props="{ disabled: !Boolean(titleValue) || !Boolean(descriptionValue) }">
            <Form @input="handleChangeNoteData" :titleValue="titleValue" :descriptionValue="descriptionValue" :indexValue="indexValue"/>
        </Modal>
        
    </main>
</template>

<style scoped>
.notes-list {
   margin-top: 20px;
   display: flex;
   flex-direction: column;
   gap: 10px;
}
</style>
