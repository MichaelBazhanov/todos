<template>
	<div
		:class="['todo-input', {'valid-error': validation.hasError('todo.name')}]">
		<div class="error">{{ validation.firstError('todo.name') }}</div>

		<div
			v-if="todos.length"
			@click="arrowTodo"
			:class="['arrow', {select:select}]"
		>
			<svg aria-hidden="true" focusable="false" data-prefix="fas" data-icon="chevron-down" class="svg-inline--fa fa-chevron-down fa-w-14" role="img" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 448 512"><path fill="currentColor" d="M207.029 381.476L12.686 187.132c-9.373-9.373-9.373-24.569 0-33.941l22.667-22.667c9.357-9.357 24.522-9.375 33.901-.04L224 284.505l154.745-154.021c9.379-9.335 24.544-9.317 33.901.04l22.667 22.667c9.373 9.373 9.373 24.569 0 33.941L240.971 381.476c-9.373 9.372-24.569 9.372-33.942 0z"></path></svg>
		</div>

		<input
			v-model="todo.name"
			@keydown.enter="addNewTodo"
			class="input" type="text" placeholder="Todo Name" autofocus
		>
	</div>
</template>

<script>
import { Validator } from 'simple-vue-validator'
import { mapMutations, mapGetters, mapActions } from 'vuex'

export default {
	mixins: [require('simple-vue-validator').mixin],
	validators: {
		'todo.name'(value) {
			return Validator.value(value).required('Поле не должно быть пустым!')
		}
	},
	props: {
		todos: Array
	},
	data() {
		return {
			todo: {
				id: 0,
				name: '',
				checked: false,
			},
		}
	},
	computed: {
		...mapGetters(['select', 'plus']),
	},
	methods: {
		...mapMutations(['addTodo', 'arrowTodoFilter_V', 'arrowTodoSelectToggle_V', 'ID']),
		...mapActions(['changeAddTodo','changeSelect']),

		addNewTodo(e) {
			this.$validate().then(succes => {
				//неудачная валидация
				if(!succes) return

				//удачная валидация
				this.todo.id = this.plus;//vuex (увеличиваем id счетчик на 1)
				this.ID(this.todo.id); //записываем текущий ID во vuex для сравнения

				// this.addTodo({...this.todo});//vuex
				this.changeAddTodo({...this.todo});//vuex (переписан на Action)
				this.todo.name =''
				this.validation.reset()
			});

			if(this.select) { //add
				this.arrowTodoSelectToggle_V();//vuex
			}

		},
		arrowTodo() {
			this.arrowTodoSelectToggle_V();//vuex
			// this.select == false ? this.select = true : this.select = false
			// this.$emit('arrowTodo', this.select)
			this.arrowTodoFilter_V();//vuex

			this.changeSelect()//vuex-action
		}
	}
}
</script>

<style lang="postcss" scoped>
.todo-input {
	position: relative;
	color: #607d8b;
	border: 2px solid transparent;
	background-color: #fff;
}
.input {
	font-size: 24px;
	padding: 16px 16px 16px 57px;
	border: 1px solid transparent;
	box-shadow: 0 2px 1px rgba(0, 0, 0, 0.03);
	line-height: 1.4em;
	outline: none;
	color: inherit;
	width: 100%;
	background: #fff;
}
.arrow {
	padding: 0;
	margin: 0;
	border: none;
	background-color: transparent;
	outline: none;
	cursor: pointer;
	position: absolute;
	top: 50%;
	left: 0;
	transform: translate(19px, -50%);
	width: 20px;
	height: 20px;
	opacity: 0.2;
	user-select: none;
}
.arrow.select {
	opacity: 1;
}
.todo-input {
	position: relative;
}
.error {
	color: #607d8b;
	position: absolute;
	bottom: 100%;
	left: 0;
	font-style: italic;
}
.valid-error {
	border: 2px solid #607d8b;
}
</style>