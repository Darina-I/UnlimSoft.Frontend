<script setup>
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';

defineProps(["data"]);
const emit = defineEmits(["doubleСlick"]);

const onRowDblClick = (e) => {  
    emit('doubleСlick', e.data.id);
}
</script>

<template>
    <DataTable
        :value="data"
        removableSort
        row-hover
        @row-dblclick="onRowDblClick"
        class="cursor-pointer w-full"
    >
        <Column header="№" bodyClass="text-center">
            <template #body="{index}">
                {{ index + 1 }}
            </template>
        </Column>
        <Column field="fio" header="ФИО" sortable>
        </Column>
        <Column field="birthday" header="Дата рождения" sortable>
            <template #body="{data}">
                {{ new Date(data.birthday).toLocaleDateString('ru-RU') }}
            </template>
        </Column>
    </DataTable>
</template>