<template>
    <div class="formNotes">
        
        <form @submit="submitNote">
            <div class="menu">
                <button type="button" @click="submitRemove" class="bg-danger btn btn-delete">Delete</button>
                <button type="button" @click="submitSave" v-if="mode == 'save'" class="bg-success btn">Save</button>
                <button type="button" @click="submitUpdate" v-if="mode == 'update'" class="bg-success btn">Update</button>
            </div>

            <div class="content">
                <input type="text" class="text" placeholder="id" v-model="id">
                <input type="text" class="text" placeholder="Title" v-model="title">
                <textarea class="text textarea" placeholder="Write your description..." v-model="description"></textarea>
            </div>
        </form>

    </div>
</template>

<script type="text/javascript">
    import emitter from 'tiny-emitter/instance'
    import axios from 'axios'

    export default {
        name: 'formNotes',
        props: { },
        data: function() {
            return {
                id: 0,
                title: '',
                description: '',
                mode: 'save'
            }
        },
        methods: {
            submitSave() {
                let params = new URLSearchParams();
                params.append('title', this.title);
                params.append('description', this.description);

                axios.post('http://localhost/testing/api.php?f=CREATE', params).then(response => {
                    let data = {
                        id: response.data.id,
                        title: this.title,
                        description: this.description
                    }

                    emitter.emit('emitSaveNote', data);
                });
            },
            submitUpdate() {
                let params = new URLSearchParams();
                params.append('id', this.id);
                params.append('title', this.title);
                params.append('description', this.description);

                axios.post('http://localhost/testing/api.php?f=UPDATE', params).then(response => {
                    let data = {
                        id: response.data.id,
                        title: this.title,
                        description: this.description
                    }

                    emitter.emit('emitUpdateNote', data);
                });
            },
            submitRemove() {
                let params = new URLSearchParams();
                params.append('id', this.id);

                axios.post('http://localhost/testing/api.php?f=DELETE', params).then(response => {
                    let data = {
                        id: response.data.id
                    }

                    emitter.emit('emitRemoveNote', data);
                    this.resetInput();
                });
            },
            resetInput() {
                this.id = 0;
                this.title = '';
                this.description = '';
            }
        },
        mounted() {
            emitter.on('emitForm', data => {
                this.id = data.id;
                this.title = data.title;
                this.description = data.description;
                this.mode = data.mode;
            })
        }
    }
</script>

<style scoped>
.menu{
     background: #f7f7f7;
     padding:10px 25px;
     margin-bottom: 25px;
     text-align:right;
     border-bottom: 1px solid #e8e6e6;
}
.btn-delete{ margin-right:10px; }
.content{
     padding: 0px 25px;
}
.text{
     display: block;
     width: 100%;
     padding: 0px;
     font-size: 20px;
     font-weight: bold;
     color: #2c3e50;
     border: none;
     margin-bottom: 10px;
     box-sizing: border-box;
     outline: none;
}
.textarea{
     min-height: 350px;
     font-size: 15px;
     font-weight: lighter;
     line-height: 30px;
}
.loader{
     vertical-align: middle;
}
</style>