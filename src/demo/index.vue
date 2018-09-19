<template>
  <div :class="'app'">
    <p class="boardnumholder" v-model="boardnum"> </p>
    <div class="topnav">
<!--       <div class="boardmenu">
       <input class="currentboardname" v-model="scene.props.name"> 
        <div class="boardarrow">&#8681;</div>
      </div> -->
      <div class="logo">&#9782; tasker</div>
      <div class="usermenu">
        <div class="usericon"></div>
       
<!--         <input type="text" class="usernameinput" v-model="username"> -->
        
      </div>
    </div>
    <div class="appinner">
      <div class="card-scene">
        <p class="colnumStore" v-model="colnum"></p>
        <p class="cardnumStore" v-model="cardnum"></p>
          <!-- drag-handle-selector=".column-drag-handle" -->
          <Container 
            orientation="horizontal" 
            @drop="onColumnDrop($event)" 
            @drag-start="dragStart"
          >
            <Draggable v-for="column in scene.children" :key="column.id">
              <div :class="column.props.className">
                <div class="card-column-header">
                  <!-- <span class="column-drag-handle">&#x2630;</span> -->
                  <!-- <input class="columntitle" type="text" v-model="column.name"  @mousedown.self.prevent @dblclick="$event.target.select()"-->
                  <input class="columntitle" type="text" v-model="column.name" @keyup.enter="$event.target.blur()" 
                  @mousedown.self.prevent
                  @dblclick="$event.target.select()">
                  <div class="titlemenubutton"><i class="fa fa-trash" aria-hidden="true" id="titlemenu-button"></i></div>
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
                      <div class="cardlabel" id="card-label" :style="card.props.labelstyle">
                      </div>
                      <div class="cardinner">
                        <p><input class="cardinput" type="text" v-model="card.data"
                          @keyup.enter="$event.target.blur()"
                          @dblclick="$event.target.select()"
                          @mousedown.self.prevent
                        >
                        </p>
                        <p><textarea class="carddescription" type="text" v-model="card.carddescription" @keyup.enter="$event.target.blur()"
                          @dblclick="$event.target.select()"
                          @mousedown.self.prevent
                          >
                          
                        </textarea></p>
                        <div class="cardmenu">
                          <div class="date"><i class="fa fa-clock-o"></i> Sept</div>
                          <button class="cardmenuitem"  @click="popup(column.id, card.id)" >&hellip;
                          </button>
                           <el-dialog
                              :visible.sync="dialogVisible"
                              width="30%"
                              :modalAppendToBody="false">

                              <p><input class="cardinputbig" type="text" v-model="selected.data"
                                @keyup.enter="$event.target.blur()"
                                @dblclick="$event.target.select()"
                                @mousedown.self.prevent
                                id="input-big" 
                              >
                              </p>

                              <p><textarea class="carddescriptionbig" type="text" v-model="selected.carddescription" id="desc-big" > 
                                
                              </textarea></p>

                              <span slot="footer" class="dialog-footer">
                                <el-button @click="dialogVisible = false">Color Label</el-button>
                                <el-button @click="popdeletecard(column.id, card.id)">Delete</el-button>
                              </span>
                            </el-dialog>
                        </div>
                      </div>
                    </div>
                  </Draggable>

                </Container>
                <button class="addCardButton" @click="addNewCard(column.id)">+ Add new card</button>
              </div>

            </Draggable>

          </Container>
          <button class="addColumnButton" @click="addNewColumn()">+</button>
        </div>
    </div>
  </div>
</template>

<script>
import Vue from 'vue';
import { Container, Draggable } from "vue-smooth-dnd";
import { applyDrag, generateItems } from "./pages/utils";
import Element from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';

Vue.use(Element);

export default {

  name: "Cards",
  components: { Container, Draggable },


  data: function() {

    return {

      dialogVisible: false,
      boardnum: 0,
      colnum: 0,
      cardnum: 0,
      username: "John Smith",
      month: "",
      selected: {},

      scene: {
        type: "container",
        props: {
          orientation: "horizontal"
        },
        children: generateItems(1, i => ({
          id: `column` + this.colnum,
          type: "container",
          name: "Title",
          props: {
            orientation: "vertical",
            className: "card-container"
          },
          children: generateItems(1, j => ({
            type: "draggable",
            id: `${this.colnum}${this.cardnum}`,
            props: {
              className: "card",
              style: { backgroundColor: 'white' },
              labelstyle: { backgroundColor: 'white' }
            },
            data: 'Card title',
            carddescription: 'Description'
          }))
        }))
      }

    };
  },

  methods: {

    // handleClose: function(done) {

    // this.$confirm('Are you sure to close this dialog?')
    // .then(_ => {
    //   done();
    // })
    // .catch(_ => {});
    // },

    popup: function(columnId, cardId){

      const scene = Object.assign({}, this.scene);
      const column = scene.children.filter(p => p.id === columnId)[0];
      const card = column.children.filter(p => p.id === cardId)[0];
      const cardIndex = column.children.indexOf(card);


      this.selected = card;

      this.dialogVisible = true;


        //    document.getElementById('input-big').value = card.data;


      console.log(card.data, cardId);

      console.log(document.getElementById('input-big'));


    },

    popdeletecard: function(columnId, cardId){

      return new Promise((resolve) => {

      const scene = Object.assign({}, this.scene);
      const column = scene.children.filter(p => p.id === columnId)[0];
      const card = column.children.filter(p => p.id === cardId)[0];
      const cardIndex = column.children.indexOf(card);
      console.log(cardIndex);

      column.children.splice(card);

      this.dialogVisible = false;

      resolve();

    })},

    newpopup: function(columnId, cardId){

      const scene = Object.assign({}, this.scene);
      const column = scene.children.filter(p => p.id === columnId)[0];
      const card = column.children.filter(p => p.id === cardId)[0];
      const cardIndex = column.children.indexOf(card);



      swal( {

        content: {

          element: "input",
          attributes: {
            type: "text",
            value: card.data
          }, 

          element: "input",
            attributes: {
              type: "text",
              className: "cardinput",
              value: card.carddescription
            },


        },

        buttons: {
          setcolor: {
            text: "Color label",
            value: "setcolor"
          },
          save: {
            text: "Save",
            value: "save"

          },
          deletecard:{
            text:"Delete",
            value: "deletecard",
            className: "deletebutton"
          }

        }

      })

      .then((value) => {

        const state = swal.getState();

        console.log(swal.getState());
        

        if(!value) throw null;

        switch(value){

        case "save": card.carddescription = state.actions.confirm.value;
        break;

        case "deletecard": column.children.splice(cardIndex, 1);
        break;

        case "setcolor": swal({
          buttons: {
            none: {
              color: "none"
            },
            red: {
              color: "red"
            },
            purple: {
              color: "purple"
            },
            green: {
              color: "green"
            }
          }

          
          })
      

        .then((color) => {
          if(!color) throw null;

          switch(color){
            case "none": card.props.labelstyle.backgroundColor = "white"
            break;

            case "red": card.props.labelstyle.backgroundColor = "#f27474"
            break;

            case "purple": card.props.labelstyle.backgroundColor = "#8083F9"
            break;

            case "green": card.props.labelstyle.backgroundColor = "#55D2B6"
            break;

          }
        })
      }});

    },

    deletethiscard: function(){

    },

    nolabel: function(){


    },
    redlabel: function(){
      const scene = Object.assign({}, this.scene);
      const column = scene.children.filter(p => p.id === columnId)[0];
      const card = column.children.filter(p => p.id === cardId)[0];

      card.style.border = "3px solid red";

    },

    bluelabel: function(){
      console.log("bluelabel clicked");
    },

    greenlabel: function(){

    },


    inputUsername: function(){
      this.usernames.push(this.username);
    },

    setFocus: function(e){
      e.focus();

    },

    deletethis: function(columnId){
        const scene = Object.assign({}, this.scene);
        const column = scene.children.filter(p => p.id === columnId)[0];
        const columnIndex = scene.children.indexOf(column);

        scene.children.splice(columnIndex, 1);
    },

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

      this.cardnum = this.cardnum + 1;

      column.children.push(({

        type: "draggable",
        id: `${this.colnum}${this.cardnum}`,
        props: {
          className: "card",
          style: { backgroundColor: 'white' },
          labelstyle: { backgroundColor: 'white' }
        },
        data: 'Card title',
        carddescription: 'Description'
        }));

      // const thiscardelement = column.children[column.children.length - 1];

      // const cardelements = Object.values(thiscardelement);

      // const cardfather = cardelements[0];

      // console.log(cardfather);

      // const columnelement = cardfather.parentElement;

      //       console.log(columnelement);


      // columnelement.scrollBy(0,150);



      //Scroll down to the element you just made, doesn't work because push insert an object into array not element
      //
      // const thiscardelement = column.children[column.children.length - 1];
      // console.log(thiscardelement);
      // const cardparent = thiscardelement.parentNode;
      // cardparent.scrollBy(0,150);

    },
    
    addNewColumn: function() {

      console.log(this.colnum);

      const scene = Object.assign({}, this.scene);
      this.colnum = this.colnum + 1;

      scene.children.push(({

        id: `column` + this.colnum,
        type: "container",
        name: "Title",
        props: {
          orientation: "vertical",
          className: "card-container"
        },
        children: []

        /*
        children: generateItems(1, j => ({
          type: "draggable",
          id: `${this.colnum}${this.cardnum}`,
          props: {
            className: "card",
            style: { backgroundColor: 'white' }
          },
          data: 'Card title',
          carddescription: 'Description'
        }))
        */
      }))
    }
  
  },

    mounted() {
      console.log('App mounted!');
      if (localStorage.getItem('scene')) this.scene = JSON.parse(localStorage.getItem('scene'));
      if (localStorage.getItem('username')) this.username = JSON.parse(localStorage.getItem('username'));
      if (localStorage.getItem('cardnum')) this.cardnum = JSON.parse(localStorage.getItem('cardnum'));
      if (localStorage.getItem('colnum')) this.colnum = JSON.parse(localStorage.getItem('colnum'));


    },


    watch: {

      scene: {
        handler() {
          console.log('Scene changed!');
          localStorage.setItem('scene', JSON.stringify(this.scene));
        },
        deep: true,
      },

      username: {
        handler() {
          console.log('Username changed!');
          localStorage.setItem('username', JSON.stringify(this.username));
        },
        deep: true,
      },
      colnum: {
        handler() {
          console.log('Username changed!');
          localStorage.setItem('colnum', JSON.stringify(this.colnum));
        },
        deep: true,
      },
      cardnum: {
        handler() {
          console.log('Username changed!');
          localStorage.setItem('cardnum', JSON.stringify(this.cardnum));
        },
        deep: true,
      },

    }
  }

</script>


<style>

.el-dialog__body{
  padding-top: 0;
  padding-bottom: 0;
}
.el-dialog__header{
  padding:0;
  padding-bottom: 10px;
}

.swal-button{
  padding: 7px 19px;
  border-radius: 5px;
  background-color: #868788;
  font-size: 12px;
  border: none;
}
.swal-button--deletecard {
  padding: 7px 19px;
  border-radius: 5px;
  background-color: #cc524c;
  font-size: 12px;
  border: none;
  outline: none;
  text-shadow: 0px -1px 0px rgba(0, 0, 0, 0.3);
}
.swal-button--setcolor {
  padding: 7px 19px;
  border-radius: 5px;
  background-color: #868788;
  font-size: 12px;
  border: none;
  text-shadow: 0px -1px 0px rgba(0, 0, 0, 0.3);
}
.swal-button--red {
  background-color: #f27474;
}
.swal-button--green {
  background-color: #55D2B6;
}
.swal-button--purple {
  background-color: #8083F9;
}
.swal-modal:focus {
  overflow: hidden;
}

.swal-title {
  display: block;
  padding-left: 25px;
  text-align: left;
}
.swal-title:first-child{
  margin-top: 20px;
}
.swal-title:not(:last-child){
  margin-bottom: 0;
}
.swal-text {
  text-align: left;
  display: block;
  padding-left: 25px;
}
.swal-modal{
  height: 300px;
  transition: none;
  animation: none;
  -webkit-animation: none;
}
.swal-overlay--show-modal{
  animation: none;
  transition: none;
  -webkit-animation: none;
}
.swal-overlay{
  transition: none;
  animation: none;
  -webkit-animation: none;
}
.swal-footer{
  bottom: 0;
  right: 0;
  position: absolute;
}

</style>

<style src="../style.css"/>
<style src="../animate.css"/>


