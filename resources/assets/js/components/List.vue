<template>
    <div>
        <ul class="list-group center">
            <div class="component-header">Notes({{ notesLength }})</div>
            <li v-for="(note, index) in notes" class="list-group-item" v-if="notesLength">
                <div class="row">
                    <div class="col-sm-7">
                        <span v-if="currentEditingId !== note.id">{{ note.title }}</span>
                        <input type="text" v-if="currentEditingId === note.id && editing" :placeholder="note.title"
                               v-model="editedTitle">
                    </div>
                    &nbsp;<div class="options col-sm-4 text-right">
                        <div class="glyphicon glyphicon-trash" @click="deleteNote(note.id)" title="Delete Note"></div>
                        <div class="glyphicon glyphicon-pencil" @click="edit(note.id)"
                             v-if="currentEditingId !== note.id" title="Edit Note"></div>
                        <div class="glyphicon glyphicon-ok" @click="update(note.id, note.title)"
                             v-if="currentEditingId === note.id"></div>
                        <div :class="favouriteClass(note.is_favourite)"
                             @click="toggleFavourite(note.id, note.is_favourite)"
                             :title="favouriteTitle(note.is_favourite)"></div>
                        </div>
                </div>
            </li>
            <div v-else>No Notes Found</div>
        </ul>
    </div>
</template>

<script>
    import { mapState } from 'vuex';

    export default {
        name: 'List',
        data() {
          return {
              editing: false,
              currentEditingId: 0,
              editedTitle: ''
          }
        },
        mounted() {
            this.$store.dispatch('fetch');
        },
        methods: {
          deleteNote(id) {
              this.$store.dispatch('deleteNote', id);
              flash('Note Successfully Deleted.');
          },
          edit(id) {
              this.editing=true;
              this.currentEditingId = id;
          },
          update(id, previousTitle) {
              this.currentEditingId = 0;
              this.$store.dispatch('edit', {id: id, title:  this.editedTitle ? this.editedTitle : previousTitle});
              this.editing = false;
              flash('Note Successfully Edited.');
          },
            toggleFavourite(id, is_favourite) {
            this.$store.dispatch('toggleFavourite', id);
            const message = is_favourite ? 'Unfavourited Successfully' :'Favourited successfully.';
            flash(message);
          },
            favouriteClass(isFavourite) {
                const heartIcon = 'glyphicon glyphicon-heart';
                return isFavourite ? heartIcon : `${heartIcon}-empty`;
            },
            favouriteTitle(isFavourite) {
                return isFavourite ? 'Unfavourite' : 'Favourite';
            }
        },
        computed: {
            ...mapState(['notes']),
            notesLength() {
                return this.notes.length;
            }
        }
    }
</script>