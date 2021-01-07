<template>
    <div class="TodoList">
        <div class="TodoList__heading">
            Organizer:
            <span class="TodoList__heading__date">{{ momentFunc(selectedDate).format('DD.MM.YYYY') }}</span>
        </div>

        <div class="TodoList__inner">
            <div
                v-if="!(!!Object.keys(list).length
                && list[generateNameOfDate(selectedDate)]
                && !!list[generateNameOfDate(selectedDate)].length)"
            >
                {{ defaultMessage }}
            </div>

            <ol
                v-else
                class="TodoList__items"
            >
                <li
                    v-for="(item, index) in list[generateNameOfDate(selectedDate)]"
                    class="TodoList__items__item"
                    :key="index"
                >
                    <div class="TodoList__items__item__inner">
                        <div class="TodoList__items__item__title">{{ item }}</div>

                        <button
                            class="button-remove"
                            @click="removeTask(index)"
                        >
                            Remove
                        </button>
                    </div>
                </li>
            </ol>

            <input
                class="input"
                v-model="task"
                @keyup.enter="!!task && addTask()"
            />

            <button
                class="button-add"
                :disabled="!task"
                @click="addTask"
            >
                Add
            </button>
        </div>
    </div>
</template>

<script>
import moment from 'moment'

export default {
    name: 'TodoList',

    props: {
        selectedDate: {
            type: [Date, String],
            required: true
        }
    },

    data() {
        return {
            momentFunc: moment,
            defaultMessage: 'No tasks yet...',
            task: '',
            list: {}
        }
    },

    created() {
        this.list = this.setupDataFromStorage('todoList') || {}
    },

    methods: {
        addTask() {
            const name = this.generateNameOfDate(this.selectedDate)

            if (!this.list[name]) this.list[name] = []

            this.list[name].push(JSON.parse(JSON.stringify(this.task)))

            this.setupDataFromStorage('todoList', this.list)

            this.task = ''
        },

        removeTask(index) {
            const name = this.generateNameOfDate(this.selectedDate)

            this.list[name].splice(index, 1)

            this.setupDataFromStorage('todoList', this.list)
        },

        generateNameOfDate(val) {
            return moment(val).format('YYYY-MM-DD')
        },

        setupDataFromStorage(key, data) {
            if (data) {
                localStorage.setItem(key, JSON.stringify(data))

                return false
            }

            return localStorage.getItem(key) ? JSON.parse(localStorage.getItem(key)) : null
        }
    }
}
</script>
