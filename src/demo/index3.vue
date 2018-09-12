
<template>
  <div :class="'app'">
  <div class="card-scene">
      <Container 
        orientation="horizontal" 
        @drop="onColumnDrop($event)"
        drag-handle-selector=".column-drag-handle"
        @drag-start="dragStart"
      >
        <Draggable v-for="column in scene.columns" :key="column.id">
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
              <Draggable v-for="card in scene.columns.cards" :key="card.id">
                <div :class="card.props.className" :style="card.props.style">
                  <p contenteditable="true" v-model="newCardData">   
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
import { applyDrag, generateColumnID, generateItems } from "./pages/utils";


export default {

  name: "Cards",
  components: { Container, Draggable },

  data: function() {
    return {



      scene: {
      type: "container",
      props: {
        orientation: "horizontal"
      },

      columns:[{

        
        column:{
                id: `column1`,
                type: "container",
                name: "Title",
                props: {
                  orientation: "vertical",
                  className: "card-container"
                },
                      cards: [{

                        card:{
                          type: "draggable",
                                  id: `1`,
                                  props: {
                                    className: "card",
                                    style: { backgroundColor: 'white' }
                                  },
                                  data: 'write something...'
                                }
                      }]
              }
      }],
      
      }
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


   
  }
};

</script>


<style src="../style.css"/>

















