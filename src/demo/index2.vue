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
                  <p contenteditable="true" v-model="newCardData" v-on:blur="addNewCardData(column.id, card.id, newCardData)">   
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

    addNewCardData: function(columnId, cardId, newCardData) {
      const scene = Object.assign({}, this.scene);
      const column = scene.children.filter(p => p.id === columnId)[0];
      const card = column.children.filter(p => p.id === cardId)[0];

      card.data = newCardData;
    }

   
  }
};
// if (userexists) {

      //   console.log('user does exist and fetching data');

      //   console.log('userexists =', userexists);

      //   gboards.once('value', (boards) => {
      //     boards.forEach((board) => {
            
      //       this.boards.push({
      //         ref: board.ref,
      //         data: board.child('data').val(),
      //         id: board.child('id').val()
      //       })
      //       this.alldata.currentselectedboard = this.boards[this.boards.length - 1];
      //       console.log(this.boards);
      //     })
      //   });


      //   this.alldata.allBoards.push({

      //   type: "container",
      //   id: 'scene' + this.alldata.scenenum,
      //   props: {
      //     orientation: "horizontal",
      //   },

      //   children: generateItems(1, i => ({

      //     id: this.alldata.scenenum + `column` + this.alldata.colnum,
      //     type: "container",
      //     name: "Title",
      //     props: {
      //       orientation: "vertical",
      //       className: "card-container"
      //     },
      //     children: generateItems(1, j => ({
      //       type: "draggable",
      //       id: `${this.alldata.scenenum}${this.alldata.colnum}${this.alldata.cardnum}`,
      //       props: {
      //         className: "card",
      //         labelstyle: { backgroundColor: 'transparent' }
      //       },
      //       data: 'Card title',
      //       carddescription: 'Write something...',
      //       showcheckbox: false,
      //       check: [],
      //       checkdone: [],
      //       percentage: 0
      //     }))}))}),

      //       /////////// most current ////////////////
      //       galldata.once('value')
      //       .then(res => {
      //         return res.val();
      //       })
      //       .then(newdata => {

      //         console.log(newdata);
      //         console.log(this.alldata);

      //         this.alldata = newdata;
              
      //         console.log(newdata);
      //         console.log(this.alldata);
      //       })
      //       //////////////////////////////////////////


      //       /////////// most current ////////////////
      //       gscene.once('value')
      //         .then(nu => {
      //           return nu.val();
      //         })
      //         .then(newscene => {
                
      //           this.scene = newscene;

      //           // this.scene = newscene;
      //           console.log('inside fetching gscene from db', this.scene);
      //         })
      //       //////////////////////////////////////////
      //       // const thisboard = this.alldata.allBoards.filter(a => a.id === ('scene' + this.alldata.currentselectedboard.id))[0];
      //       // const boarditem = this.boards.filter(a => a.id === (this.alldata.currentselectedboard.id))[0];

      //       // this.alldata.currentselectedboard = boarditem;
      //       // this.scene = thisboard;
      // }else{

      //   console.log('user logged in, but no db data');
      //   console.log('load new db data');

      //   this.boards.push({ data: 'New board', id: this.alldata.scenenum });

      //   this.alldata.currentselectedboard = this.boards[0];

      //   ///////////////

      //   this.alldata.allBoards.push({

      //   type: "container",
      //   id: 'scene' + this.alldata.scenenum,
      //   props: {
      //     orientation: "horizontal",
      //   },

      //   children: generateItems(1, i => ({

      //     id: this.alldata.scenenum + `column` + this.alldata.colnum,
      //     type: "container",
      //     name: "Title",
      //     props: {
      //       orientation: "vertical",
      //       className: "card-container"
      //     },
      //     children: generateItems(1, j => ({
      //       type: "draggable",
      //       id: `${this.alldata.scenenum}${this.alldata.colnum}${this.alldata.cardnum}`,
      //       props: {
      //         className: "card",
      //         labelstyle: { backgroundColor: 'transparent' }
      //       },
      //       data: 'Card title',
      //       carddescription: 'Write something...',
      //       showcheckbox: false,
      //       check: [],
      //       checkdone: [],
      //       percentage: 0
      //     }))}))}),
            



      //       this.scene = this.alldata.allBoards[this.alldata.allBoards.length -1];

      //       // this.saveuserdata();


      //       // const thisboard = this.alldata.allBoards.filter(a => a.id === ('scene' + this.alldata.currentselectedboard.id))[0];
      //       // const boarditem = this.boards.filter(a => a.id === (this.alldata.currentselectedboard.id))[0];

      //       // this.alldata.currentselectedboard = boarditem;
      //       // this.scene = thisboard;



      // }
      // this.boards.push({ data: "", id: null });

    //   this.alldata.currentselectedboard = this.boards[0];

    //   ///////////////

    //   this.alldata.allBoards.push({

    //   type: "container",
    //   id: null,
    //   props: {
    //     orientation: "horizontal",
    //   },

    //   children: generateItems(1, i => ({

    //     id: null,
    //     type: "container",
    //     name: "",
    //     props: {
    //       orientation: "vertical",
    //       className: "card-container"
    //     },
    //     children: generateItems(1, j => ({
    //       type: "draggable",
    //       id: null,
    //       props: {
    //         className: "card",
    //         labelstyle: { backgroundColor: 'transparent' }
    //       },
    //       data: "",
    //       carddescription: "",
    //       showcheckbox: false,
    //       check: [],
    //       checkdone: [],
    //       percentage: null
    //     }))}))}),
    
    // this.scene = this.alldata.allBoards[this.alldata.allBoards.length -1];

    //   this.boards.push({ data: 'New board', id: this.alldata.scenenum });

    //   this.alldata.currentselectedboard = this.boards[0];

    //   ///////////////

    //   this.alldata.allBoards.push({

    //   type: "container",
    //   id: 'scene' + this.alldata.scenenum,
    //   props: {
    //     orientation: "horizontal",
    //   },

    //   children: generateItems(1, i => ({

    //     id: this.alldata.scenenum + `column` + this.alldata.colnum,
    //     type: "container",
    //     name: "Title",
    //     props: {
    //       orientation: "vertical",
    //       className: "card-container"
    //     },
    //     children: generateItems(1, j => ({
    //       type: "draggable",
    //       id: `${this.alldata.scenenum}${this.alldata.colnum}${this.alldata.cardnum}`,
    //       props: {
    //         className: "card",
    //         labelstyle: { backgroundColor: 'transparent' }
    //       },
    //       data: 'Card title',
    //       carddescription: 'Write something...',
    //       showcheckbox: false,
    //       check: [],
    //       checkdone: [],
    //       percentage: 0
    //     }))}))}),
    
    // this.scene = this.alldata.allBoards[this.alldata.allBoards.length -1];






      // if (localStorage.getItem('boards')) this.boards = JSON.parse(localStorage.getItem('boards'));
      // if (localStorage.getItem('alldata')) this.alldata = JSON.parse(localStorage.getItem('alldata'));
      // if (localStorage.getItem('scene')) this.scene = JSON.parse(localStorage.getItem('scene'));

      // const gboards = db.ref(firebase.auth().currentUser.uid + '/boards/');
      // const gscene = db.ref(firebase.auth().currentUser.uid + '/scene/');
      // const galldata = db.ref(firebase.auth().currentUser.uid + '/alldata/');

      // if(nouserloggedin ==  false){
      //   console.log('no user logged in');
      //   console.log("this.currentselectedboard array is empty");

      //   this.boards.push({ data: 'New board', id: this.alldata.scenenum });

      //   this.alldata.currentselectedboard = this.boards[0];

      //   ///////////////

      //   this.alldata.allBoards.push({

      //   type: "container",
      //   id: 'scene' + this.alldata.scenenum,
      //   props: {
      //     orientation: "horizontal",
      //   },

      //   children: generateItems(1, i => ({

      //     id: this.alldata.scenenum + `column` + this.alldata.colnum,
      //     type: "container",
      //     name: "Title",
      //     props: {
      //       orientation: "vertical",
      //       className: "card-container"
      //     },
      //     children: generateItems(1, j => ({
      //       type: "draggable",
      //       id: `${this.alldata.scenenum}${this.alldata.colnum}${this.alldata.cardnum}`,
      //       props: {
      //         className: "card",
      //         labelstyle: { backgroundColor: 'transparent' }
      //       },
      //       data: 'Card title',
      //       carddescription: 'Write something...',
      //       showcheckbox: false,
      //       check: [],
      //       checkdone: [],
      //       percentage: 0
      //     }))}))}),
      
      // this.scene = this.alldata.allBoards[this.alldata.allBoards.length -1];

      // ///////////////

      // };
      

      // const thisboard = this.alldata.allBoards.filter(a => a.id === ('scene' + this.alldata.currentselectedboard.id))[0];
      // const boarditem = this.boards.filter(a => a.id === (this.alldata.currentselectedboard.id))[0];

      // this.alldata.currentselectedboard = boarditem;
      // this.scene = thisboard;

    //   if(!Array.isArray(galldata.allBoards) || !galldata.allBoards.length){

    //     console.log("allboards array is empty");

    //   this.alldata.allBoards.push({

    //     type: "container",
    //     id: 'scene' + this.alldata.scenenum,
    //     props: {
    //       orientation: "horizontal",
    //     },

    //     children: generateItems(1, i => ({

    //       id: this.alldata.scenenum + `column` + this.alldata.colnum,
    //       type: "container",
    //       name: "Title",
    //       props: {
    //         orientation: "vertical",
    //         className: "card-container"
    //       },
    //       children: generateItems(1, j => ({
    //         type: "draggable",
    //         id: `${this.alldata.scenenum}${this.alldata.colnum}${this.alldata.cardnum}`,
    //         props: {
    //           className: "card",
    //           labelstyle: { backgroundColor: 'transparent' }
    //         },
    //         data: 'Card title',
    //         carddescription: 'Write something...',
    //         showcheckbox: false,
    //         check: [],
    //         checkdone: [],
    //         percentage: 0
    //       }))}))}),
      
    //   this.scene = this.alldata.allBoards[this.alldata.allBoards.length -1];

    // };

      // const thisboard = this.alldata.allBoards.filter(a => a.id === ('scene' + this.alldata.currentselectedboard.id))[0];
      // const boarditem = this.boards.filter(a => a.id === (this.alldata.currentselectedboard.id))[0];

      // this.alldata.currentselectedboard = boarditem;
      // this.scene = thisboard;






      // watch: {

    //   boards: {
    //     handler() {
    //       console.log('boards changed!');
    //       // localStorage.setItem('boards', JSON.stringify(this.boards));
    //       db.ref(firebase.auth().currentUser.uid + '/boards/').set(this.boards);
    //     },
    //     deep: true,
    //   },

    //   scene: {
    //     handler() {
    //       console.log('Scene changed!');
    //       // localStorage.setItem('scene', JSON.stringify(this.scene));
    //       db.ref(firebase.auth().currentUser.uid + '/scene/').set(this.scene);
    //     },
    //     deep: true,
    //   },
    //   alldata: {
    //     handler() {
    //       console.log('alldata changed!');
    //       // localStorage.setItem('alldata', JSON.stringify(this.alldata));
    //       db.ref(firebase.auth().currentUser.uid + '/alldata/').set(this.alldata);
    //     },
    //     deep: true,
    //   },

    // }
</script>


<style src="../style.css"/>

