<template>
  <div :class="'app'">
  <div class="card-scene">
      <Container 
        orientation="horizontal" 
        @drop="onColumnDrop($event)"
        drag-handle-selector=".column-drag-handle"
        @drag-start="dragStart"
      >
        <Draggable v-for="column in scene.children" :key="column.id">
          <div :class="column.props.className">
            <div class="card-column-header">
              <span class="column-drag-handle">&#x2630;</span>
              {{column.name}} 
            </div>
            <Container 
              group-name="col"
              @drop="(e) => onCardDrop(column.id, e)" 
              @drag-start="(e) => log('drag start', e)"
              @drag-end="(e) => log('drag end', e)"
              :get-child-payload="getCardPayload(column.id)"
              drag-class="card-ghost"
              drop-class="card-ghost-drop"
            >
              <Draggable v-for="card in column.children" :key="card.id">
                <div :class="card.props.className" :style="card.props.style">
                  <p contenteditable="true" v-model="newCardData" v-on:blur="addNewCardData()"> 
                    {{card.data}}
                  </p>
                </div>
              </Draggable>
            </Container>
            <button class="addCardButton" @click="addNewCard(column.id)">+</button>
          </div>
        </Draggable>
      </Container>
    </div>
  </div>
</template>

<script>
import { Container, Draggable } from "vue-smooth-dnd";
import { applyDrag, generateItems } from "./pages/utils";

const columnNames = ["To do", "In Progress ", "Finished", "Ideas/Features"];

const cardColors = [
  "azure",
  "beige",
  "bisque",
  "blanchedalmond",
  "burlywood",
  "cornsilk",
  "gainsboro",
  "ghostwhite",
  "ivory",
  "khaki"
];
const pickColor = () => {
  const rand = Math.floor(Math.random() * 10);
  return cardColors[rand];
};

const scene = {
  type: "container",
  props: {
    orientation: "horizontal"
  },
  children: generateItems(4, i => ({
    id: `column${i}`,
    type: "container",
    name: columnNames[i],
    props: {
      orientation: "vertical",
      className: "card-container"
    },
    children: generateItems(1, j => ({
      type: "draggable",
      id: `${i}${j}`,
      props: {
        className: "card",
        style: { backgroundColor: 'white' }
      },
      data: 'write something...'
    }))
  }))
};

export default {
  name: "Cards",
  components: { Container, Draggable },
  data: function() {
    return {
      scene,
      newCardData: ''
    };
  },
  methods: {
    onColumnDrop: function(dropResult) {
      const scene = Object.assign({}, this.scene);
      scene.children = applyDrag(scene.children, dropResult);
      this.scene = scene;
    },

    onCardDrop: function(columnId, dropResult) {
      if (dropResult.removedIndex !== null || dropResult.addedIndex !== null) {
        const scene = Object.assign({}, this.scene);
        const column = scene.children.filter(p => p.id === columnId)[0];
        const columnIndex = scene.children.indexOf(column);

        const newColumn = Object.assign({}, column);
        newColumn.children = applyDrag(newColumn.children, dropResult);
        scene.children.splice(columnIndex, 1, newColumn);

        this.scene = scene;
      }
    },

    getCardPayload: function(columnId) {
      return index => {
        return this.scene.children.filter(p => p.id === columnId)[0].children[
          index
        ];
      };
    },
    dragStart: function(){
      console.log('drag started');
    },
    log: function(...params){
      console.log(...params);
    },

    addNewCard: function(columnId) {

      const scene = Object.assign({}, this.scene);
      const column = scene.children.filter(p => p.id === columnId)[0];
      const columnIndex = scene.children.indexOf(column);

      const newColumn = Object.assign({}, column);
      column.children.push(({

      type: "draggable",
      //id: `${j}`,
      props: {
        className: "card",
        style: { backgroundColor: 'white' }
      },
      data: 'write something...'
      }));

    },

    addNewCardData(){

      this.card.push({data: this.newCardData});

    }

   
  }
};
</script>


<style src="../style.css"/>