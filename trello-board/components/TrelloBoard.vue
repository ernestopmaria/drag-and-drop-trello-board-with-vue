<script setup lang="ts">
import { Column, Task } from '~~/types';
import draggable from 'vuedraggable'
import { nanoid } from 'nanoid';
import TrelloBoardTask from '/components/TrelloBoardTask.vue'
import DragHandle from '/components/DragHandle.vue';
import NewTask from '/components/NewTask.vue'


const columns = useLocalStorage<Column[]>('trelloBoard',[
    {
        id: nanoid(),
        title: 'Backlog',
        tasks: [{
            id: nanoid(),
            title: 'Creating marketing landing page',
            createdAt: new Date(),
        },
        {
            id: nanoid(),
            title: 'Develop cool new features',
            createdAt: new Date(),
        },
        {
            id: nanoid(),
            title: 'fix page nav bug',
            createdAt: new Date(),
        }

        ]

    },
    {
        id: nanoid(),
        title: 'Selected for dev',
        tasks: []
    },
    {
        id: nanoid(),
        title: 'In Progress',
        tasks: []
    },
    {
        id: nanoid(),
        title: 'QA',
        tasks: []
    },
    {
        id: nanoid(),
        title: 'Complete',
        tasks: []
    }


]);

/*if you wanna use the backend  user 
watch(columns, () => {
    request
}, {
    deep: true,
})*/
const crtl = useKeyModifier("Control")

function createColumn() {
    const column :Column= {
        id: nanoid(),
        title: "",
        tasks:[]
    }
    columns.value.push(column);
    nextTick(() => {
        (document.querySelector(".column:last-of-type .title-input") as HTMLInputElement).focus()
    })
    
}
</script>

<template>
    <div class="flex overflow-x-auto gap-4  items-start">
        <draggable v-model="columns" group="columns" item-key="id" :animation="150" handle=".drag-handle"
            class="flex gap-4  items-start ">
            <template #item="{ element: column }: { element: Column }">
                <div class="bg-gray-200 column p-5 rounded min-w-[250px]">
                    <header class="font-bold mb-4">
                        <DragHandle />
                        <input class="title-input bg-transparent focus:bg-white rounded px-1 w-4/5"
                        @keyup.enter="($event.target as HTMLInputElement).blur()"
                        @keydown.backspace="column.title==='' ? columns=columns.filter(c=>c.id!==column.id) :null"
                        type=" text" 
                        v-model="column.title"/>

                    </header>
                    <draggable v-model="column.tasks" :group="{ name: 'tasks', pull: crtl ? 'clone' : true }" item-key="id"
                        :animation="150" handle=".drag-handle">
                        <template #item="{ element: task }: { element: Task }">
                            <div>
                                <TrelloBoardTask :task="task" @delete="column.tasks=column.tasks.filter(t=>t.id!==$event)" />
                            </div>

                        </template>

                    </draggable>


                    <footer>
                  <NewTask @add="column.tasks.push($event)"/>
                    </footer>


                </div>
            </template>

        </draggable>
        <button @click="createColumn"
        class="bg-gray-200 whitespace-nowrap p-2 rounded opacity-50"
        >
            + Add Another Column
        </button>

    </div>
</template>