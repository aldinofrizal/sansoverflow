<template>
  <div class="row">
    <div class="col-4"></div>
    <div class="q-pa-md col-8" style="max-width: 50%">
      <h4 style="margin:4vh;">Submit Question</h4>
      <q-form @submit="onSubmit" @reset="onReset" class="q-gutter-md">
        <q-input outlined v-model="title" label="Title" />

        <q-editor
          
          min-height="40vh"
          toolbar-bg="orange-14"
          toolbar-text-color="white"
          v-model="question"
          :definitions="{
            bold: {label: 'Bold', icon: null, tip: 'My bold tooltip'}
            }"
        />

        <q-input outlined v-model="tag" label="Tags" @keyup.space="addTags" />
        
         <q-btn outline color="orange-14" :label="tag" size="sm" icon="tags"
                v-for="(tag,i) in tags" :key="i" @click="removeTag(tag)"/>
        <div>
          <q-btn label="Submit" type="submit" color="primary" />
          <q-btn label="Reset" type="reset" color="primary" flat class="q-ml-sm" />
        </div>
      </q-form>


      <q-dialog position="top" v-model="notification" persistent transition-show="flip-down" transition-hide="flip-up">
      <q-card class="bg-orange-14 text-white">

        <q-card-section>
          <div class="text-h6 text-center">{{  message.status }}</div>
        </q-card-section>

        <q-card-section>{{ message.content }}</q-card-section>
      </q-card>
    </q-dialog>


    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      title: null,
      question: '',
      tags: [],
      tag: null,
      message : {
          status : '',
          content : ''
      },
      notification: false
    };
  },

  methods: {
    addTags() {
      this.tags.push(this.tag);
      this.tag = null;
    },
    removeTag(deleteTag){
        this.tags = this.tags.filter(tag => {
            return tag !== deleteTag
        })
    },
    onSubmit() {
        let question = {
            title : this.title,
            question : this.question,
            tags : this.tags,
        }
        this.$store.dispatch('postQuestion' , question )
        .then( data => {
            this.title = ''
            this.question = ''
            this.tag = ''
            this.tags = []
            this.notification = true,
            this.message.status = "Post Question Success"
            this.message.content = "Successfully post a question!"
            setTimeout(() => {
                this.notification = false
                this.message.status = ''
                this.message.content = ''
            this.$router.push('/')
            }, 1000);            
        })
        .catch( err => {
            this.notification = true,
            this.message.status = "Post Question Fail"
            this.message.content = "Oops something wrong!"
            err.data.message.forEach(element => {
                this.message.content += ` ${element}`
            });
            setTimeout(() => {
                this.notification = false
                this.message.status = ''
                this.message.content = ''
            }, 2000);
        })
        
    },

    onReset() {
      this.title = null;
      this.question = null;
      (this.tags = []), (this.tag = null);
    }
  }
};
</script>

<style>
</style>