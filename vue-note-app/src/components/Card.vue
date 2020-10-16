<template>
<div>
    <v-dialog v-model="dialog" width="550">
        <template v-slot:activator="{ on, attrs }">
            <v-card @mouseenter="mouseEnter" @mouseleave="mouseLeave" class="note" :style="{ 'background-color': note.theme }">
                <div class="Container">
                    <v-icon v-if="note.important" class="starIcon">mdi-pin</v-icon>
                    <v-spacer></v-spacer>
                    <div class="iconContainer">
                        <v-icon class="deleteIcon" @click.prevent="deleteNote">mdi-close-circle</v-icon>
                    </div>
                </div>
                <div v-bind="attrs" v-on="on" @click.prevent="initData">
                    <v-card-title class="cardTitle noteTitle">
                        <p>{{ note.title }}</p>
                    </v-card-title>
                    <v-card-text class="noteText">
                        <p>{{ note.text }}</p>
                    </v-card-text>
                </div>
            </v-card>
        </template>
        <ModifyEditor :note="note" @noteModified="modifyNote" />
    </v-dialog>
</div>
</template>

<script>
import ModifyEditor from "./ModifyEditor";
export default {
    props: {
        note: {
            type: Object,
            required: true,
        },
        date: {
            type: String,
            required: true,
        },
    },

    data() {
        return {
            dialog: false,
            isSubmit: false,
            tempNote: {},
        };
    },

    watch: {
        dialog: {
            handler() {
                if (this.dialog === false && this.isSubmit === false) {
                    console.log("대화상자 꺼짐!")
                    this.note.title = this.tempNote.title;
                    this.note.text = this.tempNote.text;
                    this.note.theme = this.tempNote.theme;
                    this.note.important = this.tempNote.important;
                }
            },
        },
    },

    methods: {
        mouseEnter(e) {
            e.target.firstChild.lastChild.style.visibility = "visible";
        },

        mouseLeave(e) {
            e.target.firstChild.lastChild.style.visibility = "hidden";
        },

        modifyNote(title, text, theme, time, date, originDate, important) {
            this.isSubmit = true;
            this.dialog = false;
            this.$emit("modifyNote", title, text, theme, time, date, originDate, this.note.guid, important);

        },

        initData() {
            this.tempNote.title = this.note.title;
            this.tempNote.text = this.note.text;
            this.tempNote.theme = this.note.theme;
            this.tempNote.important = this.note.important

            this.isSubmit = false;
        },

        deleteNote() {
            this.$emit("deleteNote", this.note.guid);
        },
    },

    components: {
        ModifyEditor,
    },
};
</script>

<style scoped>
.cardTitle {
    margin-top: -25px;
}

.Container {
    display: flex;
}

.iconContainer {
    visibility: hidden;
}

.deleteIcon:hover {
    cursor: pointer;
}

p {
    white-space: pre-line;
}
</style>