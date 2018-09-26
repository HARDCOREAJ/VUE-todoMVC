<template>
 <div id="todoMVC">
   <h1> todos</h1>
   <div class="content">
       <span class="icon-down el-icon-arrow-down" 
       v-show="todoLists.length>0"
       @click="selectAllTodos"></span>
       <input type="text" class="todos_add" placeholder="what needs to be done?"
       @keyup.enter="addTodo($event.target)" ref="currentInput">
       <ul class="content_todoLists">
        <li class="content_todoList"
           v-for="(list,index) in todoLists" :key="index" @mouseover="list.isActive = true"
           @mouseleave="list.isActive=false" 
           v-show="defaultShow || (whichShow?list.isChecked:!list.isChecked)"
           >
           <input type="checkbox" class="checkBox" v-model="list.isChecked">
                <div class="content_todoList_main" @dblclick="toEdit(list)" v-show="!list.isEditing" :class="{deleted:list.isChecked}">
                        {{list.value}}
                </div>
                <input type="text" class="content_todoList_main main_input" 
                v-model="list.value" 
                v-show="list.isEditing" 
                v-todo-focus="list.value"
                @blur="unEdit(list)">
           <span class="el-icon-close content_todoList_delete"
           :class="{show: list.isActive}"
           @click="deleteTodo(index)"></span>
        </li>
       </ul>
       <div class="data" v-show="todoLists.length>0">
            <div class="data_times" v-show="times === 0">
                <span>{{times}}</span>item left
            </div>
            <div class="data_times" v-show="times > 0">
                <span>{{times}}</span>items left
            </div>
            <div class="data_status">
                <a href="#" :class="{active:index === dataStatusIndex}" v-for="(item,index) in dataStatus" @click="switchStatus(index)" :key="index" >{{item}}</a></div>
            <div class="data_clearTodos" @click="clearTodos" v-show="times<todoLists.length">
                <a href="#">clear completed</a>
            </div>
            <div class="data_clearTodos"  v-show="times===todoLists.length">
                <a href="#"></a>
            </div>
    </div>

   </div>

 </div>

</template>

<script>
export default {
  name: "todoProject",
  data() {
    return {
      todoLists: [],
      dataStatus: ["All", "Active", "Completed"],
      dataStatusIndex: 0,
      whichShow: true,
      defaultShow: true
    };
  },
  computed:{
      times(){//使用计算属性计算待办todos的个数 
          let todoArr = this.todoLists
          let times =0
          for(let i=0;i<todoArr.length;i++){
              if(todoArr[i].isChecked===false){
                  times++
              }
          }
          return times  
      }
  },
  methods: {
    addTodo(e) {
      var val = e.value;
      if (val === "") {
        return;
      }
      this.todoLists = this.todoLists.concat({
        value: val, //内容
        isEditing: false, //是否在编辑状态
        isActive: false, //X图标是否激活
        isChecked: false //是否已完成
      });
      this.$refs.currentInput.value = "";
      window.localStorage.setItem("content", JSON.stringify(this.todoLists));
    },
    deleteTodo(index) {
      this.todoLists.splice(index, 1); //删除todoLists中的todo
      window.localStorage.setItem("content", JSON.stringify(todoLists));
    },
    toEdit(value) {//双击时间绑定Edit
      value.isEditing = true;
    },
    unEdit(value) {//离开输入框时是UnEdit
      value.isEditing = false;
    },
    selectAllTodos() {
      this.todoLists.map(//设置所有todo为已完成状态
        todo => (todo.isChecked = todo.isChecked ? false : true)
      );
    },
    clearTodos(){//清除已完成的todo
        this.todoLists = this.todoLists.filter(todo => todo.isChecked===false)
        window.localStorage.setItem("content",JSON.stringify(this.todoLists))
    },
    switchStatus(index) { //通过if条件判断操作 "Active" "All" "Completed"
    this.dataStatusIndex = index
    if (this.dataStatus[index] === "Active") {
        this.defaultShow = false
        this.whichShow = false
    } else if (this.dataStatus[index] === "Completed") {
        this.defaultShow = false
        this.whichShow = true
    } else if (this.dataStatus[index] === "All") {
        this.defaultShow = true
    }
},
  },
  directives: {//编辑todo时，在输入框focus
    "todo-focus": function(el, binding) {
      if (binding.value) {
        el.focus();
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}

h1 {
  top: -155px;
  width: 100%;
  font-size: 100px;
  font-weight: 100;
  text-align: center;
  color: rgba(175, 47, 47, 0.15);
}

input {
  outline: none;
}

ul,
li,
ol {
  list-style: none;
}

.content {
  width: 72%;
  margin: 0 auto;
  box-shadow: 0 3px 3px 2px rgba(0, 0, 0, 0.25);
  position: relative;
}

.icon-down {
  position: absolute;
  font-size: 24px;
  top: 16px;
  left: 16px;
  cursor: pointer;
}

#todoMVC .content .todos_add {
  width: 100%;
  height: 56px;
  padding: 16px 56px;
  font-size: 24px;
  border: 1px solid transparent;
}

.content_todoLists {
  position: relative;
  z-index: 3;
}

.content_todoList {
  display: flex;
  flex-direction: row;
  border-top: 1px solid #ccc;
  font-size: 24px;
  padding: 8px;
  background-color: white;
  align-items: center;
}

.checkBox {
  width: 20px;
  height: 20px;
  margin-left: 10px;
}

.content_todoList_main {
  flex: 1;
  text-align: left;
  margin-left: 16px;
  font-size: 20px;
  padding: 6px 0;
}

.main_input {
  position: relative;
  z-index: 1;
}

.content_todoList_delete {
  position: absolute;
  right: 16px;
  color: rgb(252, 55, 55);
  font-weight: 500;
  display: none;
  cursor: pointer;
}

.show {
  display: block;
}

.deleted {
  text-decoration-line: line-through;
  color: #bbb;
}

.show:hover {
  color: rgb(255, 0, 0);
  font-weight: 700;
}

::-moz-placeholder {
  color: rgb(221, 218, 218);
}

::-webkit-input-placeholder {
  color: rgb(221, 218, 218);
}

:-ms-input-placeholder {
  color: rgb(221, 218, 218);
}

.data {
  display: flex;
  justify-content: space-between;
  padding: 8px;
  font-size: 14px;
  font-weight: 300;
  color: rgb(145, 145, 145);
}

a {
  text-decoration: none;
  color: rgb(145, 145, 145);
}

.data_times {
  width: 73px;
}

.data_clearTodos {
  width: 142px;
}

.data_status a {
  display: inline-block;
  border: 1px solid transparent;
  border-radius: 2px;
  padding: 1px 4px;
  margin: 0 2px;
}

.data_status a:hover {
  border-color: #bbb;
}

.data_clearTodos a:hover {
  text-decoration-line: underline;
}

.active {
  box-shadow: 0 0 1px black;
}
</style>
