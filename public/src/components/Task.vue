<template>
  <div class="tasks">
    <div class="card">
      <div class="card-header" data-toggle="modal" :data-target="'#'+task._id" draggable="true" v-on:dragstart.capture="changeList"
        :task='task' id="task-modal">
        <h5>{{task.title}}</h5>
      </div>
      <div class="modal" tabindex="-1" role="dialog" :id='task._id'>
        <div class="modal-dialog" role="document">
          <div class="modal-content">
            <div class="card-body">
              <div class="dropdown">
                <div class="modal-header">
                  <h5 class="task-title">{{task.title}}</h5>
                  <span class="editTask">
                    <i class="fas fa-edit dropdown-toggle m-1" :id="task._id" data-toggle="dropdown"></i>
                    <div class="dropdown-menu dropdown-menu-right">
                      <form class="px-4 py-3" @submit.prevent="editTask(task)">
                        <div class="form-group">
                          <label for="task-title">Title</label>
                          <input v-model="task.title" type="text" class="form-control" id="task-title" placeholder="Title">
                        </div>
                        <div class="form-group">
                          <label for="task-description">Description</label>
                          <textarea v-model="task.description" type="text" class="form-control" id="task-description" placeholder="Description" rows="3"></textarea>
                        </div>
                        <button type="submit" class="btn btn-primary">Edit Task</button>
                      </form>
                    </div>
                    <i class="fas fa-times-circle m-1" :id="task._id" @click="deleteTask(task)"></i>
                  </span>
                </div>
              </div>
              <div class="modal-body">
                <p>{{task.description}}</p>
              </div>
              <div class="comments">
                <form class="px-4 py-3" @submit.prevent="createComment">
                  <div class="form-group">
                    <textarea v-model="comment.comment" type="text" class="form-control" id="comment-comment" placeholder="New Comment" rows="3"></textarea>
                  </div>
                  <button type="submit" class="btn btn-primary">Add Comment</button>
                </form>
              </div>
            </div>
            <div class="modal-footer">
              <div :key="comment._id" v-for="comment in comments">
                <Comments :comment="comment"></Comments>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
  </div>
</template>

<script>
  import Comments from './Comment'
  export default {
    name: 'Tasks',
    props: ['task'],
    mounted() {
      this.$store.dispatch('getComments', this.task._id)
    },
    data() {
      return {
        comment: {
          boardId: this.task.boardId,
          listId: this.task.listId,
          taskId: this.task._id
        }
      }
    },
    methods: {
      createComment(comment) {
        this.$store.dispatch('createComment', this.comment)
        
      },
      editTask(task) {
        this.$store.dispatch('editTask', task)
        $('#'+task._id).dropdown('toggle')
      },
      deleteTask(task) {
        this.$store.dispatch('deleteTask', task)
        $('#'+task._id).modal('hide')
      },
      changeList(event) {
        event.dataTransfer.setData('text/javascript', JSON.stringify(this.task))
      }
    },
    computed: {
      user() {
        return this.$store.state.user
      },
      comments() {
        return this.$store.state.comments[this.task._id]
      },
      lists() {
        return this.$store.state.boardLists
      },
      board() {
        return this.$store.state.boardLists
      }
    },
    components: {
      Comments
    }
  }
</script>

<style scoped>
  .model-header{
    display: flex;
    justify-content: space-between;
    align-items: center
  }
  .editTask {
    display: flex;
    justify-content: flex-end;
    padding: 4px;
  }

  .modal-footer {
    display: block
  }
</style>