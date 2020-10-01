<template>
  <div>
 <!-- Pop up for card options -->
    <el-dialog
      :visible.sync="dialogVisible"
      width="35%"
      :modalAppendToBody="false"
      >
      

      <p><input class="cardinputbig" type="text" v-model="selectedCard.data"
        @keyup.enter="$event.target.blur()"
        @dblclick="$event.target.select()"
        @mousedown.self.prevent
        id="input-big" 
        maxlength="2000"
      >
      </p>

      <p><textarea class="carddescriptionbig" type="text" v-model="selectedCard.carddescription" id="desc-big" maxlength="2000" > 
        
      </textarea></p>

      <span slot="footer" class="dialog-footer">
        <el-button @click="">Link</el-button>
        <el-button @click="">Due Date</el-button>
        <el-button @click="colorVisible = true">Color Label</el-button>
        <el-button style="backgroundColor:#f27474; border:none; color:white" 
        @click="popdeletecard">Delete</el-button>
      </span>
    </el-dialog>

    <!-- Second pop up for color labels -->
        <el-dialog
          :visible.sync="colorVisible"
          width="30%"
          :modalAppendToBody="false">

          <div slot="footer" class="dialog-footer" style="text-align: center">
            <el-button style="backgroundColor:#f27474; border:none;" @click="chooseRed">&nbsp;  </el-button>
            <el-button style="backgroundColor:#55D2B6; border:none;" @click="chooseGreen">&nbsp;  </el-button>
            <el-button style="backgroundColor:#8083F9; border:none;" @click="choosePurple">&nbsp;  </el-button>
            <el-button style="backgroundColor:#929292; border:none;" @click="chooseGrey">&nbsp;  </el-button>
            <el-button style="" @click="chooseNone">None</el-button>
          </div>

        </el-dialog>

        <!-- Pop up for column menu -->
    <el-dialog
      :visible.sync="columnVisible"
      width="30%"
      :modalAppendToBody="false"
      >
      <span slot="footer" class="dialog-footer">
        <el-button style="backgroundColor:#f27474; border:none; color:white" 
          @click="deletecolumn">Delete</el-button>
      </span>
    </el-dialog>
    <p class="boardnumholder" v-model="boardnum"> </p>
    <div class="appinner">
      <div class="card-scene">
        <p class="colnumStore" v-model="colnum"></p>
        <p class="cardnumStore" v-model="cardnum"></p>
          <!-- drag-handle-selector=".column-drag-handle" -->
          <Container 
            orientation="horizontal" 
            @drop="onColumnDrop($event)" 
            @drag-start="dragStart"
            :key="scene.id"
          >
            <Draggable v-for="column in scene.children" :key="column.id">
              <div :class="column.props.className">
                <div class="card-column-header">
                  <!-- <span class="column-drag-handle">&#x2630;</span> -->
                  <!-- <input class="columntitle" type="text" v-model="column.name"  @mousedown.self.prevent @dblclick="$event.target.select()"-->
                  <input class="columntitle" type="text" v-model="column.name" @keyup.enter="$event.target.blur()" 
                  @mousedown.self.prevent
                  @dblclick="$event.target.select()" maxlength="2000">
                  <div class="titlemenubutton" >
                    <button class="cardmenuitem"  @click="columnpopup(column.id)" >
                      <i class="el-icon-more"></i>
                    </button>
                  </div>
                </div>
                <Container 
                  :id="column.id"
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
                          @mousedown.self.prevent maxlength="2000"
                        >
                        </p>
                        <p><textarea class="carddescription" type="text" v-model="card.carddescription" @keyup.enter="$event.target.blur()"
                          @dblclick="$event.target.select()"
                          @mousedown.self.prevent
                          maxlength="2000" 
                          >

                          
                        </textarea></p>
                        <div class="cardmenu">
                          <div class="date"><i class="fa fa-clock-o"></i> Sept</div>
                          <button class="cardmenuitem"  @click="popup(column.id, card.id)" ><i class="el-icon-more"></i>
                          </button>
                   
                              
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
    <div class="rightNav" id="right-nav">
      <div class="menutitle">Menu</div>
      <div class="menuoptions">
        <button class="menusizebutton" @click="cardSizeBig"><i class="el-icon-menu" style="padding-right:10px;"></i></button>
        
        <button class="menusizebutton" @click="cardSizeSmall"><i class="el-icon-minus" style="padding-left:10px;"></i></button>
      </div>
      <div class="menuactivity">
        <div class="activitytitle">Activity</div>
      </div>
      <div class="menuchat">
        <div class="chattitle">Chat</div>
        <div class="chatcontent"></div>
        <input class="chatinput" placeholder="Write something..." maxlength="2000">
      </div>
    </div>
    <div class="rightMenu" @click="navmenu">
      <i class="el-icon-tickets" style="color:white; position:relative; top: 25%; " ></i>
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


var store = {
  debug: true,
  state: {
    boardname: 'New Board'
  },
  setBoardAction (newValue) {
    if (this.debug) console.log('setMessageAction triggered with', newValue)
    this.state.boardname = newValue
  },
  clearBoardAction () {
    if (this.debug) console.log('clearMessageAction triggered')
    this.state.boardname = ''
  }
};


export default {

  name: "NewScene",
  components: { Container, Draggable, store },


  data: function() {

    return {

      cardSize: true,
      menuopen: false,
      columnVisible: false,
      dialogVisible: false,
      colorVisible: false,
      boardnum: 0,
      colnum: 0,
      cardnum: 0,
      scenenum: 0,
      username: "John Smith",
      month: "",
      selectedCard: {},
      selectedColumn: {},


      scene: {
        type: "container",
        id: 'scene' + this.scenenum,
        props: {
          orientation: "horizontal",
        },
        children: []
         
        }

    };
  },

  beforeCreate: function(){

  },

  methods: {

    cardSizeBig: function(){

          if(this.cardSize == false){
          const x = document.getElementsByClassName("card");

                for (let i = 0; i < x.length; i++) {
                  x[i].style.height = "136px";
              };

          const x2 = document.getElementsByClassName("cardmenu");

            for (let i = 0; i < x2.length; i++) {
              x2[i].style.display = "flex";
          };
          const x3 = document.getElementsByClassName("carddescription");

            for (let i = 0; i < x3.length; i++) {
              x3[i].style.display = "block";
          };
          const x4 = document.getElementsByClassName("cardlabel");

            for (let i = 0; i < x4.length; i++) {
              x4[i].style.height = "75px";
          };
          const x5 = document.getElementsByClassName("cardlabel");

            for (let i = 0; i < x5.length; i++) {
              x5[i].style.top = "32px";
          };

          this.cardSize = true;

        };
    },

    cardSizeSmall: function(){


      if(this.cardSize == true){
      const x = document.getElementsByClassName("card");

            for (let i = 0; i < x.length; i++) {
              x[i].style.height = "50px";
          };

      const x2 = document.getElementsByClassName("cardmenu");

        for (let i = 0; i < x2.length; i++) {
          x2[i].style.display = "none";
      };
      const x3 = document.getElementsByClassName("carddescription");

        for (let i = 0; i < x3.length; i++) {
          x3[i].style.display = "none";
      };
      const x4 = document.getElementsByClassName("cardlabel");

        for (let i = 0; i < x4.length; i++) {
          x4[i].style.height = "25px";
      };
      const x5 = document.getElementsByClassName("cardlabel");

        for (let i = 0; i < x5.length; i++) {
          x5[i].style.top = "12.5px";
      };

      this.cardSize = false;
    };

    },

    columnpopup: function(columnId){
      const scene = Object.assign({}, this.scene);
      const column = scene.children.filter(p => p.id === columnId)[0];

      this.selectedColumn = column;

      this.columnVisible = true;

    },

    deletecolumn: function(){

      const colIndex = this.scene.children.indexOf(this.selectedColumn);

      this.scene.children.splice(colIndex, 1);

      this.columnVisible = false;
    },

    navmenu: function(){
      const menu = document.getElementById('right-nav');
      if(this.menuopen == false){
        menu.style.width = "400px";
        this.menuopen = true;
      }
      else{
        menu.style.width = "0px";
        this.menuopen = false;
        }
    },

    enterCol: function(columnId){
      const scene = Object.assign({}, this.scene);
      const column = scene.children.filter(p => p.id === columnId)[0];
 
      console.log(column.props.className);
    },

    chooseRed: function(){
      this.selectedCard.props.labelstyle.backgroundColor = "#f27474"
      this.colorVisible = false;
    },
    chooseGreen: function(){
      this.selectedCard.props.labelstyle.backgroundColor = "#55D2B6"
      this.colorVisible = false;
    },
    choosePurple: function(){
      this.selectedCard.props.labelstyle.backgroundColor = "#8083F9"
      this.colorVisible = false;
    },
    chooseGrey: function(){
      this.selectedCard.props.labelstyle.backgroundColor = "#929292"
      this.colorVisible = false;
    },
    chooseNone: function(){
      this.selectedCard.props.labelstyle.backgroundColor = "white"
      this.colorVisible = false;
    },

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

      this.selectedCard = card;
      this.selectedColumn = column;
      this.dialogVisible = true;
    },

    popdeletecard: function(){

      const cardIndex = this.selectedColumn.children.indexOf(this.selectedCard);

      this.selectedColumn.children.splice(cardIndex, 1);

      this.dialogVisible = false;


    },

    inputUsername: function(){
      this.usernames.push(this.username);
    },

    setFocus: function(e){
      e.focus();

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
        id: `${this.scenenum}${this.colnum}${this.cardnum}`,
        props: {
          className: "card",
          style: { backgroundColor: 'white' },
          labelstyle: { backgroundColor: 'white' }
        },
        data: 'Card title',
        carddescription: 'Description'
        }));

      const card = column.children[column.children.length - 1];

      console.log(card);

      const ColID = document.getElementById(columnId);

      ColID.scrollTop = ColID.scrollHeight;

    },
    
    addNewColumn: function() {

      const scene = Object.assign({}, this.scene);
      this.colnum = this.colnum + 1;

      scene.children.push(({

        id: this.scenenum + `column` + this.colnum,
        type: "container",
        name: "Title",
        props: {
          orientation: "vertical",
          className: "card-container"
        },
        children: []
      }))
    }
  
  },

    mounted() {
      this.scene.children.push({

          id: this.scenenum + `column` + this.colnum,
          type: "container",
          name: "Title",
          props: {
            orientation: "vertical",
            className: "card-container"
          },
          children: generateItems(1, j => ({
            type: "draggable",
            id: `${this.scenenum}${this.colnum}${this.cardnum}`,
            props: {
              className: "card",
              style: { backgroundColor: 'white' },
              labelstyle: { backgroundColor: 'white' }
            },
            data: 'Card title',
            carddescription: 'Description'
          }))}),
      


      console.log('App mounted!');

      if (localStorage.getItem('scenenum')) this.scenenum = JSON.parse(localStorage.getItem('scenenum'));
      if (localStorage.getItem('scene')) this.scene = JSON.parse(localStorage.getItem('scene'));
      if (localStorage.getItem('username')) this.username = JSON.parse(localStorage.getItem('username'));
      if (localStorage.getItem('cardnum')) this.cardnum = JSON.parse(localStorage.getItem('cardnum'));
      if (localStorage.getItem('colnum')) this.colnum = JSON.parse(localStorage.getItem('colnum'));


    },


    watch: {

      scenenum: {
      handler() {
        console.log('Scenenum changed!');
        localStorage.setItem('scenenum', JSON.stringify(this.scenenum));
      },
      deep: true,
      },

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
          console.log('Colnum changed!');
          localStorage.setItem('colnum', JSON.stringify(this.colnum));
        },
        deep: true,
      },
      cardnum: {
        handler() {
          console.log('Cardnum changed!');
          localStorage.setItem('cardnum', JSON.stringify(this.cardnum));
        },
        deep: true,
      },

    }
  }

</script>


<style>

.el-dialog{
  border-radius: 10px;
}

.el-dialog__body{
  padding-top: 0;
  padding-bottom: 0;
}

.el-dialog__header{
  padding:0;
  padding-bottom: 10px;
}

.el-dialog__headerbtn{
  display: none;
}

</style>

<style src="../style.css"/>
<style src="../animate.css"/>


