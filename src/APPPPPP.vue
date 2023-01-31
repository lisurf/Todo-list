
<template>
    <section class="todo-list">
        <div class="list" id="todo-list">

            <div v-for="(group, category) in groupedTodos" :key="category">
                <div v-for="todo in group" :class="`todo-item ${todo.done && 'done'}`">
                    <label>
                        <input type="checkbox" v-model="todo.done" />
                        <span :class="`bubble ${
                            todo.category == 'work'
                                ? 'work'
                                : todo.category == 'personal'
                                ? 'personal'
                                : todo.category == 'house'
                                ? 'house'
                                : 'none'
                        }`"></span>
                    </label>

                    <div class="todo-content">
                        <input type="text" v-model="todo.content" />
                    </div>

                    <div class="actions">
                        <button class="delete" @click="removeTodo(todo)"> X </button>
                    </div>
                </div>
            </div>
        </div>
	</section>
</template>
