<template>
  <div :class="'app'">

<!-- Landing screen -->

    <div id="p-l-i">
      <div class="maincontent">
        <div class="appname">KEVVLAR</div>
        <div class="maintext" id="main-text">
          <div class="innertext">
            <h2>Simple. Fast. Clean.</h2>
            <!-- <div class="topline"></div> -->
            <h5 style="color:#868686; margin-top: 0px">Everyone could use some better organization, so we built an app just for that. With dead-simple drag and drop functionality and color coded notes, you will be sure to finish your work faster and never miss a thing.
            </h5>
            <div class="ctainner">
              <button class="ctabtn" @click="gsignintest">Get started</button>
              <div id="pricing" @click="pricinginfo();">
                Pricing
              </div>
            </div>
            <div id="pricing-hidden">
              <p>Free! But donations are appreciated.</p>
              <button class="donatebutton" @click="donation();">Donate</button>
            </div>
          </div>
        </div>
        <div class="heropic">
          <!-- <img src="./images/tsk2.png" class="heroimg"> -->
          <video width="80%" height="100%" controls loop>
            <source src="./images/prog.mp4" type="video/mp4">
          </video>
        </div>
        <div class="contactus">

        </div>
      </div>
    </div>



    <div class="topnav">
     <div class="boardmenu">
          <div class="hamicon" style="padding-right: 10px; font-size: 18px; color: #ffffff69; height: 50px;" 
      @click="openBoardsMenu"
      ><i class="fas fa-bars"></i></div> 
          <p>
            <input class="boardname" id="board-name" type="text" v-model="alldata.currentselectedboard.data" @keydown.enter="$event.target.blur()" @blur="saveuserdata(); updateboardtaskname(alldata.currentselectedboard.id);">
              <!-- <i class="el-icon-arrow-down el-icon--right" style="color: white;"></i> -->
            </input>
          </p>
      </div> 
      <div class="logo"><p>KEVVLAR</p></div>
      <div class="usermenu">
        <div class="notification" style="color:#ffffff70; font-size:25px; padding-right: 9px;" @click="navmenu"><img id="notification-icon" src="./images/notification.svg" width="25px"><img id="notificationOn-icon" src="./images/notificationOn.svg" width="25px"></div>
        <button id="gbutton" @click="gsignintest">Sign in</button>
        <img class="iconimg" id="user-icon"></img>
        <div class="usericon" id="usernameandpic"></div>
      </div>
    </div>
  <!-- Pop up for board menu options -->
    <el-dialog
            :visible.sync="boardmenuVisible"
            :modalAppendToBody="false"
            custom-class="dialogimported"
            >
            <div slot="footer" class="dialog-footer" style="text-align: center">
              <p class="cardinputbig" style="min-height:50px;">{{ this.alldata.currentselectedboard.data }}</p>
              <!-- <el-button style="backgroundColor: transparent;color:white;" @click="thememenuVisible = true;" >Theme</el-button> -->
              <el-button style="backgroundColor: transparent;" disabled >Change icon</el-button>
              <el-button style="backgroundColor:#f27474; border:none; color: white;" @click="boardmenudeleteVisible = true;">Delete Board</el-button>
            </div>

          </el-dialog>
  <!-- Change theme. -->
           <!-- <el-dialog
            :visible.sync="thememenuVisible"
            width="30%"
            :modalAppendToBody="false"
            custom-class="dialogimported"
            >
            <p class="cardinputbig">Change theme</p>
            <div slot="footer" class="dialog-footer" style="text-align: center">
              <el-button style="backgroundColor: transparent;color:white;" @click="darktheme">Dark theme</el-button>
              <el-button style="backgroundColor: transparent;color:white;" @click="lighttheme">Light theme</el-button>
            </div>

          </el-dialog> -->
  <!-- Are you sure you want to delete entire board pop up -->
           <el-dialog
            :visible.sync="boardmenudeleteVisible"
            :modalAppendToBody="false"
            custom-class="dialogimported"
            
            >
            <p class="cardinputbig">Are you sure? All of your board information will be lost?</p>
            <div slot="footer" class="dialog-footer" style="text-align: center">
              <el-button style="backgroundColor: transparent; color:white;"  @click="boardmenuVisible = false; boardmenudeleteVisible = false">Cancel</el-button>
              <el-button style="backgroundColor:#f27474; border:none; color: white;" @click="deleteBoard">Yes I'm sure.</el-button>
            </div>

          </el-dialog>
 <!-- Pop up for bigger card view -->
        <!-- <el-dialog
          :visible.sync="dialogVisible"
          width="40%"
          :modalAppendToBody="false"
          top="10vh"
          custom-class="dialogimported"
          :close="colordates()"
          > -->
          <el-dialog
          :visible.sync="dialogVisible"
          :modalAppendToBody="false"
          top="10vh"
          custom-class="dialogimported"
          :close="colordates()"
          >
          <p><input class="cardinputbig" type="text" v-model="alldata.selectedCard.data"
            @keyup.enter="$event.target.blur()"
            id="input-big" 
            maxlength="2000"
            @blur="saveuserdata(); updatetaskitem(alldata.selectedCard.data, alldata.selectedCard.id);"
          >
          </p>
          <p><textarea class="carddescriptionbig" type="text" v-model="alldata.selectedCard.carddescription" 
            id="desc-big" maxlength="2000" @blur="saveuserdata"> 
            
          </textarea></p>

          <div class="checkboxarea">
            <div class="checkboxbar"><i class="el-icon-check"></i>
              <div class="cbabar">
                <div class="cbabarprogress" :id="'cba' + this.alldata.selectedCard.id" v-bind:style="{width: this.alldata.selectedCard.percentage + '%'}"></div>
              </div>
              <div class="cbapercentage">{{this.alldata.selectedCard.percentage + ' %'}}</div>
            </div>
            <div class="checkcolumns">
              <div class="cbaitemholder" v-for="(checkitem, index) in this.alldata.selectedCard.check" :key="checkitem.id">

                <input type="checkbox" :value="checkitem.id" v-model="alldata.selectedCard.checkdone" @change="percentchange">
                <div class="checkboxdisplay"> {{ checkitem.itemtitle }} </div>
                <i class="cbaitemdelete el-icon-close" @click="deletecheckboxitem(index)"></i>

              </div>
            </div>
            <input class="checkboxinput" placeholder="+ Add item" 
            @keydown.enter="addcheckboxitem" 
            v-model="tempcheckboxinput" @blur="tempcheckboxinput = ''">
          </div>

          <span slot="footer" class="dialog-footer">
            <!-- <el-button disabled>Link</el-button> -->
            <el-button style="backgroundColor: transparent; color:none; border: none; padding-right:0px !important" >
              <el-date-picker
                v-model="alldata.selectedCard.duedate"
                type="date"
                placeholder="Due Date"
                format="MM/dd/yyyy"
                value-format="MM/dd/yyyy"
                @change="saveuserdata(); tasks();"
                @blur="colordates();">
              </el-date-picker>
            </el-button>
            <el-button style="backgroundColor: transparent; color:white;" @click="colorVisible = true">Color Label</el-button>
            <el-button style="backgroundColor:#f27474; border:none; color:white" 
            @click="popdeletecard">Delete</el-button>
          </span>
        </el-dialog>

    <!-- Second pop up for color labels -->
        <el-dialog
          :visible.sync="colorVisible"
          :modalAppendToBody="false"
          style="border-radius: 10px;">

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
      :modalAppendToBody="false"
      style="border-radius: 10px;"
      center
      >
      <div class="columntitle">{{this.alldata.selectedColumn.name}}</div>
      <span slot="footer" class="dialog-footer">
        <el-button style="backgroundColor:#f27474; border:none; color:white" 
          @click="deletecolumn();">Delete</el-button>
      </span>
    </el-dialog>
    <div class="appinner">
      <div class="card-scene">
        <p class="colnumStore" v-model="alldata.colnum"></p>
        <p class="cardnumStore" v-model="alldata.cardnum"></p>
        <div class="rightNav" id="right-nav">
          <div class="menutitle">
            <p>{{ this.alldata.currentselectedboard.data }}</p>
            <button class="boardMenuItem"  @click="boardpopup" >
              <i class="el-icon-more"></i>
            </button>
            <button class="menusizebutton" @click="cardSizeBig"><i class="el-icon-menu"></i></button>
            <button class="menusizebutton" @click="cardSizeSmall"><i class="el-icon-minus"></i></button>
          </div>
          <!-- <div class="menuoptions">
            <button class="menusizebutton" @click="cardSizeBig"><i class="el-icon-menu"></i></button>
            <button class="menusizebutton" @click="cardSizeSmall"><i class="el-icon-minus"></i></button>
          </div> -->
          <div class="menuactivity">
            <div class="activitytitle">Tasks due</div>
            <div class="taskholder" v-for="(task, index) in orderBy(alldata.tasks, 'date', 1)" :key="task.id">
              <div class="taskitem">{{task.name + '  -  ' + task.date}}
                <div class="taskitemboard">{{task.board.data}}</div>
              </div>
              <i class="el-icon-close" style="color:red; cursor: pointer; padding-right: 10px;" 
              @click="deletetask(task.id)"></i>
            </div>
            <div class="logout" @click="loguserout">Log out</div>
          </div>
          <!-- <div class="menuchat">
            <div class="chattitle">Chat</div>
            <div class="chatcontent"></div>
            <input class="chatinput" placeholder="Write something..." maxlength="2000">
          </div> -->
        </div>
          <!-- drag-handle-selector=".column-drag-handle" -->
          <Container 
            orientation="horizontal" 
            @drop="onColumnDrop($event)" 
            @drag-start="dragStart"
            :key="scene.id"
            drag-handle-selector=".card-column-header"
            v-dragscroll:nochilddrag
            id="fullboard"
          >
            <Draggable data-dragscroll v-for="column in scene.children" :key="column.id">
              <div data-dragscroll :class="column.props.className" >
                <div class="card-column-header">
                  <!-- <span class="column-drag-handle">&#x2630;</span> -->
                  <!-- <input class="columntitle" type="text" v-model="column.name"  @mousedown.self.prevent @dblclick="$event.target.select()"-->
                  <input class="columntitle" type="text" v-model="column.name" @keyup.enter="$event.target.blur()" @blur="saveuserdata"
                  @mousedown.self.prevent
                  @dblclick="$event.target.select()" maxlength="100">
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
                      <div class="cardlabelholder">
                        <div class="cardlabel" id="card-label" :style="card.props.labelstyle"></div>
                      </div>
                      <div class="cardinner">
                        <input class="cardinput" type="text" v-model="card.data"
                          @keyup.enter="$event.target.blur(); updatetaskitem(card.data,card.id);"
                          @dblclick="$event.target.select()"
                          @mousedown.self.prevent maxlength="100"
                          @blur="saveuserdata">
                        
                        <textarea class="carddescription" type="text" v-model="card.carddescription" @keyup.enter="$event.target.blur()"
                          @dblclick="$event.target.select()"
                          @mousedown.self.prevent
                          maxlength="2000" 
                          @blur="saveuserdata"
                          >
                        </textarea>
                        <div class="cardmenu">
                          <div class="date">{{card.duedate}}</div>
                          <div class="checkboxlist" :id="card.id" v-if="(card.check || []).length > 0">
                            <i class="el-icon-check"></i>
                            {{ (card.checkdone || []).length }}/{{ card.check.length }}
                          </div>
                          <button class="cardmenuitem"  @click="popup(column.id, card.id)" ><i class="el-icon-more"></i>
                          </button>
                        </div>
                      </div>
                    </div>
                  </Draggable>
                  <!-- <button class="addCardButton" @click="addNewCard(column.id)">+ Add new card</button> -->
                </Container>
                <button class="addCardButton" @click="addNewCard(column.id)">+ Add new card</button>
              </div>

            </Draggable>

          </Container>
          <button class="addColumnButton" @click="addNewColumn()">+</button>
        </div>
    </div>
<!-- Right side navigation menu -->
    <div class="boardsmenubackdrop" id="back-drop" @click="openBoardsMenu"></div>
    <div id="boards-menu" class="leftboardsmenu">
      <div class="boardsmenutop">
        <div class="hamicon" style="padding-left: 14px; font-size: 18px; color: #ffffff69;" 
      @click="openBoardsMenu"
      ><i class="fas fa-bars"></i></div>
        <p style="padding-left: 15px; padding-right: 14px; font-size: 14px">{{ this.alldata.currentselectedboard.data }}</p>
        <button class="boardMenuItem"  @click="boardpopup" >
          <i class="el-icon-more" style="right: 10px; top: 16px; position: absolute;"></i>
        </button>
      </div>
      <div class="boardholder">
        <div class="boardstitle">Boards</div>
        <div class="dropitem" v-for="item in boards" :key="item.id" @click="selectcurrentboard(item.id)">{{item.data}}</div>
        <div class="addnewboardbutton" @click="addnewboard">+ Add new board</div>
      </div>
    </div>
    <!-- <div class="rightNav" id="right-nav"> -->
      <!-- <div class="menutitle">
        <p>{{ this.alldata.currentselectedboard.data }}</p>
        <button class="boardMenuItem"  @click="boardpopup" >
          <i class="el-icon-more"></i>
        </button>
        <button class="menusizebutton" @click="cardSizeBig"><i class="el-icon-menu"></i></button>
        <button class="menusizebutton" @click="cardSizeSmall"><i class="el-icon-minus"></i></button>
      </div> -->
      <!-- <div class="menuoptions">
        <button class="menusizebutton" @click="cardSizeBig"><i class="el-icon-menu"></i></button>
        <button class="menusizebutton" @click="cardSizeSmall"><i class="el-icon-minus"></i></button>
      </div> -->
      <!-- <div class="menuactivity">
        <div class="activitytitle">Activity</div>
      </div>
      <div class="menuchat">
        <div class="chattitle">Chat</div>
        <div class="chatcontent"></div>
        <input class="chatinput" placeholder="Write something..." maxlength="2000">
      </div>
    </div> -->
    <!-- <div class="rightMenu" @click="navmenu">
      <i class="el-icon-tickets" style="color:white; position:relative; top: 25%; " ></i>
    </div> -->
  </div>
</template>

<script>
import Vue from 'vue';
import { Container, Draggable } from "vue-smooth-dnd";
import { applyDrag, generateItems } from "./pages/utils";
import { ElDropdownItem } from 'element-ui/lib/dropdown-item.js';
import ElementUI from 'element-ui';
import locale from 'element-ui/lib/locale/lang/en';
import 'element-ui/lib/theme-chalk/index.css';
import '../style.css';
import GSignInButton from 'vue-google-signin-button';
import Vue2Filters from 'vue2-filters';
import VueDragscroll from 'vue-dragscroll';

Vue.use(Vue2Filters);
Vue.use(VueDragscroll);
Vue.config.devtools = true;
Vue.use(GSignInButton);

// Vue.use(ElementUI);
Vue.use(ElementUI, { locale });

const db = firebase.database();

export default {

  name: "NewScene",
  components: { Container, Draggable },


  data: function() {

    return {
      
      googleSignInParams: {
        client_id: '719985560993-sn84mhsc7v8pmtin0dipmajhrqsao0dg.apps.googleusercontent.com'
      },

      // array that holds board names and ids
      boards: [],

      // temporary string holder for checkbox input
      tempcheckboxinput: '',

      // pop up for boardmenu
      boardmenuVisible: false,

      // are you sure you want to delete entire board pop up
      boardmenudeleteVisible: false,
      
      // are the cards big or small
      cardSize: true,

      // right side menu
      menuopen: false,

      // left side boards menu
      boardsmenuopen: false,

      // is the column menu pop up displayed
      columnVisible: false,

      // is the card menu pop up displayed
      dialogVisible: false,

      // is the color label pop up displayed
      colorVisible: false,

      // menu for theme selection
      thememenuVisible: false,

      // array that holds all important data
      alldata:{

          // menu for theme selection
          darkthemeOn: false,

          checkitemnum: 100000,
          colnum: 0,
          cardnum: 0,
          scenenum: 0,
          username: "John Smith",
          month: "Oct",
          selectedCard: {id: ""},
          selectedColumn: {id: ""},
          currentselectedboard: {id: ""},
          allBoards: [],
          tasks: [],

      },

      // current displayed board
      scene: {

        children: [],

        id: "",
        props: {
          className: "",
          orientation: ""
        },
        type: ""

      },
  }
},

  watch: {

      'alldata.selectedCard.check'(values) {
      let checkdone = this.alldata.selectedCard.checkdone;
      console.log(`values is equal to ${values}`);
      console.log(`checkdone is equal to ${checkdone}`);

      if (!this.alldata.selectedCard.checkdone) {
        return;
      }else{
      this.alldata.selectedCard.checkdone = checkdone.filter((id) => {
        console.log(`id is equal to ${id}`);
        const found = values.find(checkbox => checkbox.id === id)
        console.log(`found is equal to ${found}`);
        return found;
        
      })};
      this.percentchange();
    },
  },
  

  methods: {

    pricinginfo: function(){
      document.getElementById('pricing-hidden').style.width = "300px";
    },

    donation: function(){
      var win = window.open("https://www.paypal.me/damirpay");
      win.focus();
    },

    checknotifications: function(){
      var today = new Date();
      var dd = today.getDate();
      var mm = today.getMonth()+1; //January is 0!
      var yyyy = today.getFullYear();

      if(dd<10) {
          dd = '0'+dd
      } 

      if(mm<10) {
          mm = '0'+mm
      } 

      today = mm + '/' + dd + '/' + yyyy;

      if (this.alldata.tasks !== undefined) {
        for (var i = 0; i < this.alldata.tasks.length; i++) {
          if (this.alldata.tasks[i].date == today) {
            document.getElementById('notification-icon').style.display = "none";
            document.getElementById('notificationOn-icon').style.display = "block";
            this.notify();
            break;
          }else{
            //do nothing
          }
        }
      }
      
    },

    colordates: function(){
      var zz = document.querySelectorAll(".date");
      var today = new Date();
      var dd = today.getDate();
      var mm = today.getMonth()+1; //January is 0!
      var yyyy = today.getFullYear();

      if(dd<10) {
          dd = '0'+dd
      } 

      if(mm<10) {
          mm = '0'+mm
      } 

      today = mm + '/' + dd + '/' + yyyy;

      for (var i = 0; i < zz.length; i++) {
        console.log(zz[i].innerHTML);

        if (zz[i].innerHTML == today) {
          console.log('same due date, notification on');
          // let taskinfo = zz[i].parentNode.parentNode.childNodes[0].value;
          console.log(zz[i]);
          
          zz[i].style.color = "#f44336b8";

        }else{
          zz[i].style.color = "#6b6b6b";
        }
      }

    },

    duetoday: function(){

      const zz = document.querySelectorAll(".date");

      var taskduetoday = false;

      var today = new Date();
      var dd = today.getDate();
      var mm = today.getMonth()+1; //January is 0!
      var yyyy = today.getFullYear();

      if(dd<10) {
          dd = '0'+dd
      } 

      if(mm<10) {
          mm = '0'+mm
      } 

      today = mm + '/' + dd + '/' + yyyy;

      console.log('today',today);

      for (var i = 0; i < this.alldata.tasks.length; i++) {
        if (this.alldata.tasks[i].date == today) {
          taskduetoday = true;
          document.getElementById('notification-icon').style.display = "none";
          document.getElementById('notificationOn-icon').style.display = "block";
          
        }else{
          //do nothing
        }
      }

      if (taskduetoday) {
        //do nothing
      }else{
        document.getElementById('notification-icon').style.display = "block";
        document.getElementById('notificationOn-icon').style.display = "none";
      }

      this.colordates();

    },

    deletetask: function(location){

      for (var i = 0; i < this.alldata.tasks.length; i++) {
        if (this.alldata.tasks[i].id == location) {
          this.alldata.tasks.splice(i, 1);
        }
      }
     
      this.duetoday();
      this.saveuserdata();
    },

    tasks: function(){

      const taskname = this.alldata.selectedCard.data;
      const taskdate = this.alldata.selectedCard.duedate;
      const taskboard = this.alldata.currentselectedboard;
      var thistask;

      if (this.alldata.tasks == null || undefined) {
        this.alldata.tasks = [];
      }else{
        //nothing
      }

      console.log('tasks length',this.alldata.tasks.length);

      if (this.alldata.tasks.length <= 0) {
          this.alldata.tasks.push({name: taskname, date: taskdate, board: taskboard, id: this.alldata.selectedCard.id});
        }else{
          //do nothing
        }



      for (var i = 0; i < this.alldata.tasks.length; i++) {

        if (this.alldata.tasks[i].id == this.alldata.selectedCard.id) {

          thistask = this.alldata.tasks[i];

          this.alldata.tasks[i].date = taskdate;

        }else{
          //do nothing
        }
      }


      if (this.alldata.tasks.includes(thistask)) {
        console.log('thistask is found');
      }else{
        console.log('thistask not found');
        this.alldata.tasks.push({name: taskname, date: taskdate, board: taskboard, id: this.alldata.selectedCard.id});
      }

      this.duetoday();
      this.saveuserdata();
    },

    updatetaskitem: function(carddata,cardId){
      //const task = this.alldata.tasks.indexOf(cardId);
      const task = this.alldata.tasks.findIndex(x => x.id == cardId);
      const thistask = this.alldata.tasks[task];

      if (thistask) {
        thistask.name = carddata;
      }else{
        //do nothing
      }
      
    },

    updateboardtaskname: function(boardid){
      if (this.alldata.tasks !== undefined) {
        for (var i = 0; i < this.alldata.tasks.length; i++) {
          if (this.alldata.tasks[i].board.id == boardid) {
            this.alldata.tasks[i].board.data = this.alldata.currentselectedboard.data;
          }
        }
      }
    },

    notify: function(taskinfo) {
        // const h = this.$createElement;
        this.$notify({
          title: 'Task Due!',
          message: 'You have tasks due today',
          duration: 0,
          type: 'warning'
        });
    },

    openBoardsMenu: function(){
      const bmenu = document.getElementById('boards-menu');
      const bd = document.getElementById('back-drop');

      if(this.boardsmenuopen == false){
        bmenu.style.display = "flex";
        bmenu.style.width = "200px";
        bmenu.style.boxShadow = "-5px 0px 10px 1px rgba(68, 68, 68, 0.3)";
        bd.style.display = "block";
        this.boardsmenuopen = true;
      }
      else{
        bmenu.style.display = "none";
        bmenu.style.width = "0px";
        bmenu.style.boxShadow = "none";
        bd.style.display = "none";
        this.boardsmenuopen = false;
        }
    },

    saveuserdata: function(){

      console.log('saveuserdata executed');

      const db = firebase.database();

      db.ref(firebase.auth().currentUser.uid + '/boards/').set(this.boards);

      db.ref(firebase.auth().currentUser.uid + '/scene/').set(this.scene);

      db.ref(firebase.auth().currentUser.uid + '/alldata/').set(this.alldata);

      

    },

    loaduserdata: function(){

      const db = firebase.database();

      var userexists = false;
      console.log('userexists at variable  =', userexists);


      const gboards = db.ref(firebase.auth().currentUser.uid + '/boards/');
      const gscene = db.ref(firebase.auth().currentUser.uid + '/scene/');
      const galldata = db.ref(firebase.auth().currentUser.uid + '/alldata/');


      console.log('outside if gboards is true', gboards);
      console.log('outside if gscene is true', gscene);
    
      gscene.once('value')
        .then(xo => {
          console.log('inside first checker in loaduserdata', xo);
          return xo.val();
        })
        .then(newboards => {
          console.log('inside second checker in loaduserdata', newboards);
          if (newboards !== null) {
            userexists = true;
            console.log('switched userexists to true', userexists);
            console.log('checking to see if gboards has value in it', newboards);
          }else{
            userexists = false;
            console.log('second checker userexists = ', userexists);
          }

            if (userexists) {

            console.log('user does exist and fetching data');

            console.log('userexists =', userexists);

            

            gboards.once('value', (boards) => {
              boards.forEach((board) => {
                
                this.boards.push({
                  // ref: board.ref,
                  data: board.child('data').val(),
                  id: board.child('id').val()
                })
                //this.alldata.currentselectedboard = this.boards[this.boards.length - 1];
                console.log(this.boards);
              })
            });
                /////////// most current ////////////////
                gscene.once('value')
                  .then(nu => {
                    return nu.val();
                  })
                  .then(newscene => {
                    this.scene = newscene;

                    console.log('inside fetching gscene from db', this.scene);
                  })
                //////////////////////////////////////////

                /////////// most current ////////////////
                galldata.once('value')
                .then(res => {
                  return res.val();
                })
                .then(newdata => {

                  // console.log(newdata);
                
                  this.alldata = newdata;

                  this.checknotifications();
                  this.colordates();

                  // console.log(newdata);
                  // console.log(this.alldata);

                })

                //this.istheme();
                //////////////////////////////////////////
          }else{

            console.log('user logged in, but no db data');
            console.log('load new db data');

            this.boards.push({ data: 'New board', id: this.alldata.scenenum });

            this.alldata.currentselectedboard = this.boards[0];

            ///////////////

            this.alldata.allBoards.push({

            type: "container",
            id: 'scene' + this.alldata.scenenum,
            props: {
              orientation: "horizontal",
            },

            children: generateItems(1, i => ({

              id: this.alldata.scenenum + `column` + this.alldata.colnum,
              type: "container",
              name: "Title",
              props: {
                orientation: "vertical",
                className: "card-container"
              },
              children: generateItems(1, j => ({
                type: "draggable",
                id: `${this.alldata.scenenum}${this.alldata.colnum}${this.alldata.cardnum}`,
                props: {
                  className: "card",
                  labelstyle: { backgroundColor: 'transparent' }
                },
                data: 'Card title',
                carddescription: 'Write something...',
                showcheckbox: false,
                check: [],
                checkdone: [],
                percentage: 0,
                duedate: ''
              }))}))}),
                
            this.scene = this.alldata.allBoards[this.alldata.allBoards.length -1];

          }

        });
    },

    gsignintest: function(){

      const provider = new firebase.auth.GoogleAuthProvider();

      firebase.auth().signInWithPopup(provider);

      const profile = firebase.auth().currentUser; 

      const fullname = profile.displayName;

      const usricon = profile.photoURL;

      firebase.auth().setPersistence(firebase.auth.Auth.Persistence.LOCAL);

      document.getElementById('gbutton').style.display = "none";

      document.getElementById('usernameandpic').innerHTML = fullname;

      document.getElementById('user-icon').style.display = "block";

      document.getElementById('user-icon').src = usricon;

      console.log(firebase.auth().currentUser);


    },

    onSignInSuccess (googleUser) {

      const profile = googleUser.getBasicProfile(); 

      const fullname = profile.getName();

      const usricon = profile.getImageUrl();

      document.getElementById('gbutton').style.display = "none";

      document.getElementById('usernameandpic').innerHTML = fullname;

      document.getElementById('user-icon').style.display = "block";

      document.getElementById('user-icon').src = usricon;

      console.log(profile);
    },

    onSignInError (error) {
      console.log('OH NOES', error)
    },

    loguserout: function(){
      firebase.auth().signOut();
      window.location.reload(false); 
    },

    percentchange: function(){
      if (this.alldata.selectedCard.check == null || undefined) {
        this.alldata.selectedCard.check = [];
      };
      if (this.alldata.selectedCard.checkdone == null || undefined) {
        this.alldata.selectedCard.checkdone = [];
      };
      const p = (this.alldata.selectedCard.checkdone.length / this.alldata.selectedCard.check.length) * 100;
      const prounded = Math.round(p);
      const s = isNaN(p);
      const z = document.getElementById('cba' + this.alldata.selectedCard.id);
      console.log(z);

      if (p < 1 || s == true) {
        this.alldata.selectedCard.percentage = 0;
        
      }else{
        this.alldata.selectedCard.percentage = prounded;
      }

      // if (this.alldata.selectedCard.percentage > 99) {
      //   z.style.backgroundColor = "#35d66f";
      // }else{
      //   z.style.backgroundColor = "#409eff";
      // }

      this.saveuserdata();
    },

    addcheckboxitem: function(){
      this.alldata.checkitemnum++;

      if (this.alldata.selectedCard.check == null || undefined) {
        this.alldata.selectedCard.check = [];
      };
      this.alldata.selectedCard.check.push({id: this.alldata.checkitemnum, done: false, itemtitle: this.tempcheckboxinput});
      this.tempcheckboxinput = '';
      this.percentchange();
      this.saveuserdata();
    },

    deletecheckboxitem: function(index){

      // const chitem = this.alldata.selectedCard.check[index];
      // this.alldata.selectedCard.checkdone.splice
      this.alldata.selectedCard.check.splice(index, 1);


      //this.alldata.selectedCard.checkdone.splice(this.alldata.selectedCard.checkdone.indexOf(this.alldata.selectedCard.check[index]), 1);
      this.percentchange();
      this.saveuserdata();

    },

    // istheme: function(){
    //   if (this.alldata.darkthemeOn) {
    //     this.darktheme();
    //   }
    //   else{
    //     this.lighttheme();
    //   }
    // },

    darktheme: function(){

      this.alldata.darkthemeOn = true;

      console.log("darktheme method running");

      document.getElementsByClassName("app")[0].style.backgroundColor = "#1F252A";

      const x = document.getElementsByClassName("cardinput");

        for (let i = 0; i < x.length; i++) {
          x[i].style.color = "#41BA8E";
      };

      const y = document.getElementsByClassName("card");

        for (let i = 0; i < y.length; i++) {
          y[i].style.backgroundColor = "#343D46";
      };
      const z = document.getElementsByClassName("columntitle");

        for (let i = 0; i < z.length; i++) {
          z[i].style.color = "#E4745D";
      };
      const nav = document.getElementsByClassName("topnav");

        for (let i = 0; i < nav.length; i++) {
          nav[i].style.backgroundColor = "#343D46";
      };
      const btn = document.getElementsByClassName("addColumnButton");

        for (let i = 0; i < btn.length; i++) {
          btn[i].style.backgroundColor = "#343D46";
      };

      this.saveuserdata();
      
    },

    lighttheme: function(){

      this.alldata.darkthemeOn = false;

      document.getElementsByClassName("app")[0].style.backgroundColor = "#EEF2F5";

      const x = document.getElementsByClassName("cardinput");

                for (let i = 0; i < x.length; i++) {
                  x[i].style.color = "#565656";
              };
      const y = document.getElementsByClassName("card");

              for (let i = 0; i < y.length; i++) {
                y[i].style.backgroundColor = "white";
            };
      const z = document.getElementsByClassName("columntitle");

        for (let i = 0; i < z.length; i++) {
          z[i].style.color = "#737373";
      };
      const nav = document.getElementsByClassName("topnav");

        for (let i = 0; i < nav.length; i++) {
          nav[i].style.backgroundColor = "#076CFD";
      };
      const btn = document.getElementsByClassName("addColumnButton");

        for (let i = 0; i < btn.length; i++) {
          btn[i].style.backgroundColor = "transparent";
      };
      this.saveuserdata();
      
    },



    boardpopup: function(){
      // get the board user clicked on
      const thisboard = this.alldata.currentselectedboard;

      this.boardmenuVisible = true;

    },

    deleteBoard: function(){


      const thisboard = this.alldata.allBoards.filter(a => a.id === ('scene' + this.alldata.currentselectedboard.id))[0];
      const boarditem = this.boards.filter(a => a.id === (this.alldata.currentselectedboard.id))[0];

      if (this.alldata.tasks !== undefined) {
        for (var i = 0; i < this.alldata.tasks.length; i++) {
          if (this.alldata.tasks[i].board.data == this.alldata.currentselectedboard.data) {
            this.alldata.tasks.splice(i, 1);
          }
        }
      }

      const indexBoard = this.boards.indexOf(boarditem);
      const indexOf = this.alldata.allBoards.indexOf(thisboard);

      this.alldata.allBoards.splice(indexOf, 1);
      this.boards.splice(indexBoard, 1);

      console.log(this.alldata.allBoards);
      console.log(this.boards);

      const newboard = this.alldata.allBoards[this.alldata.allBoards.length - 1];
      const newitem = this.boards[this.boards.length - 1];

      if(newboard == undefined){
        this.addnewboard();

      }else{
      this.alldata.currentselectedboard = newitem;
      this.scene = newboard;
      };

      this.boardmenudeleteVisible = false;

      this.boardmenuVisible = false;

      this.saveuserdata();
      
    },

    selectcurrentboard: function(itemId){

      console.log('selectcurrentboard clicked');
      console.log(itemId);

      const thisboard = this.alldata.allBoards.filter(a => a.id === ('scene' + itemId))[0];
      const boarditem = this.boards.filter(a => a.id === (itemId))[0];

      this.alldata.currentselectedboard = boarditem;

      this.scene = thisboard;

      // this.$nextTick(() => {
      //   this.istheme();
      // });

      this.saveuserdata();
      this.openBoardsMenu();
    },

    addnewboard: function(){

      this.alldata.scenenum = this.alldata.scenenum + 1;

      this.alldata.colnum = this.alldata.colnum + 1;

      this.alldata.cardnum = this.alldata.cardnum + 1;

      this.boards.push({data: 'New board', id: this.alldata.scenenum});

      this.alldata.currentselectedboard = this.boards[this.boards.length - 1];

      this.alldata.allBoards.push({

        type: "container",
        id: 'scene' + this.alldata.scenenum,
        props: {
          orientation: "horizontal",
        },

        children: generateItems(1, i => ({

          id: this.alldata.scenenum + `column` + this.alldata.colnum,
          type: "container",
          name: "Title",
          props: {
            orientation: "vertical",
            className: "card-container"
          },
          children: generateItems(1, j => ({
            type: "draggable",
            id: `${this.alldata.scenenum}${this.alldata.colnum}${this.alldata.cardnum}`,
            props: {
              className: "card",
              labelstyle: { backgroundColor: 'transparent' }
            },
            data: 'Card title',
            carddescription: 'Write something...',
            showcheckbox: false,
            check: [],
            checkdone: [],
            percentage: 0,
            duedate: ''
          }))}))}),

      this.scene = this.alldata.allBoards[this.alldata.allBoards.length -1];

      // this.$nextTick(() => {
      //   this.istheme();
      // });
      this.openBoardsMenu();
      this.saveuserdata();
      document.getElementById('board-name').focus();
      document.getElementById('board-name').select();
    },

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

      this.alldata.selectedColumn = column;

      this.columnVisible = true;

    },

    deletecolumn: function(){

      const colIndex = this.scene.children.indexOf(this.alldata.selectedColumn);
      const cards = this.alldata.selectedColumn.children;

      console.log(colIndex);
      console.log(cards);

      

      console.log(temparr);
      // const task = this.alldata.tasks.findIndex(x => x.id == cardId);
      // const thistask = this.alldata.tasks[task];

      if (this.scene.children.length > 1) {

        if (cards !== undefined) {
          // check 
          for (var i = 0; i < cards.length; i++) {

            var temparr = [];

            for (var z = 0; z < this.alldata.tasks.length; z++) {
              temparr.push(this.alldata.tasks[z].id);
            }

            var temp = temparr.indexOf(cards[i]);

            this.alldata.tasks.splice(temp, 1);
          }
        }else{
          // no cards in column
        }

        this.scene.children.splice(colIndex, 1);

        this.columnVisible = false;

        this.saveuserdata();

      }else{
        console.log('tried deleting last column in scene');
      }
      
    },

    navmenu: function(){
      const menu = document.getElementById('right-nav');
      if(this.menuopen == false){
        menu.style.width = "300px";
        this.menuopen = true;
      }
      else{
        menu.style.width = "0px";
        menu.style.boxShadow = "none";
        this.menuopen = false;
        }
    },

    chooseRed: function(){
      this.alldata.selectedCard.props.labelstyle.backgroundColor = "#f27474"
      this.colorVisible = false;
      this.saveuserdata();
    },
    chooseGreen: function(){
      this.alldata.selectedCard.props.labelstyle.backgroundColor = "#55D2B6"
      this.colorVisible = false;
      this.saveuserdata();
    },
    choosePurple: function(){
      this.alldata.selectedCard.props.labelstyle.backgroundColor = "#8083F9"
      this.colorVisible = false;
      this.saveuserdata();
    },
    chooseGrey: function(){
      this.alldata.selectedCard.props.labelstyle.backgroundColor = "#929292"
      this.colorVisible = false;
      this.saveuserdata();
    },
    chooseNone: function(){
      this.alldata.selectedCard.props.labelstyle.backgroundColor = "transparent"
      this.colorVisible = false;
      this.saveuserdata();
    },

    popup: function(columnId, cardId){

      const scene = Object.assign({}, this.scene);
      const column = scene.children.filter(p => p.id === columnId)[0];
      const card = column.children.filter(p => p.id === cardId)[0];
      const cardIndex = column.children.indexOf(card);

      this.alldata.selectedCard = card;
      this.alldata.selectedColumn = column;
      this.dialogVisible = true;

      if (this.alldata.selectedCard.check == null || undefined) {
        this.alldata.selectedCard.check = [];
      };
      if (this.alldata.selectedCard.checkdone == null || undefined) {
        this.alldata.selectedCard.checkdone = [];
      };

      if (this.alldata.currentselectedboard.showcheckbox == true) {

      };

    },

    popdeletecard: function(){

      const cardIndex = this.alldata.selectedColumn.children.indexOf(this.alldata.selectedCard);

      if (this.alldata.tasks !== undefined) {
        for (var i = 0; i < this.alldata.tasks.length; i++) {
          if (this.alldata.tasks[i].date == this.alldata.selectedCard.duedate) {
            this.alldata.tasks.splice(i, 1);
            break;
          }
        }
      }

      this.alldata.selectedColumn.children.splice(cardIndex, 1);

      this.dialogVisible = false;

      
      this.saveuserdata();
    },

    onColumnDrop: function(dropResult) {
      const scene = Object.assign({}, this.scene);
      scene.children = applyDrag(scene.children, dropResult);
      this.scene = scene;
      this.saveuserdata();
      
    },

    onCardDrop: function(columnId, dropResult) {
      if (dropResult.removedIndex !== null || dropResult.addedIndex !== null) {
        const scene = Object.assign({}, this.scene);
        const column = scene.children.filter(p => p.id === columnId)[0];
        const columnIndex = scene.children.indexOf(column);

        if (column.children == null || undefined) {
          column.children = [];
        };

        const newColumn = Object.assign({}, column);
        newColumn.children = applyDrag(newColumn.children, dropResult);
        scene.children.splice(columnIndex, 1, newColumn);

        
        // this.$nextTick(() => {
        // console.log(newColumn.name);
        // });

        if (newColumn.name.indexOf('finished') > 0) {
          console.log('new column contains the word finished');
        }

        this.scene = scene;

        // this.$nextTick(() => {
        // this.istheme();
        // });

        this.saveuserdata();

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

      this.alldata.cardnum = this.alldata.cardnum + 1;

      if (column.children == null || undefined) {
        column.children = [];
      }

      column.children.push(({

        type: "draggable",
        id: `${this.alldata.scenenum}${this.alldata.colnum}${this.alldata.cardnum}`,
        props: {
          className: "card",
          labelstyle: { backgroundColor: 'transparent' }
        },
        data: 'Card title',
        carddescription: 'Write something...',
        showcheckbox: false,
        check: [],
        checkdone: [],
        percentage: 0,
        duedate: ''
        }));

      const card = column.children[column.children.length - 1];

      console.log(card);

      const ColID = document.getElementById(columnId);

      ColID.scrollTop = ColID.scrollHeight;

      // this.$nextTick(() => {
      //   this.istheme();
      // });
      if (this.cardSize == false) {
        this.cardSizeSmall();
      }
      this.saveuserdata();
    },
    
    addNewColumn: function() {

      const scene = Object.assign({}, this.scene);
      this.alldata.colnum = this.alldata.colnum + 1;

      if (scene.children == null || undefined) {
        scene.children = [];
      };

      scene.children.push(({

        id: this.alldata.scenenum + `column` + this.alldata.colnum,
        type: "container",
        name: "Title",
        props: {
          orientation: "vertical",
          className: "card-container"
        },
        children: []
      }));

      // this.$nextTick(() => {
      //   this.istheme();
      // });
      this.saveuserdata();

      setTimeout(function(){
        const board = document.getElementById("fullboard");
        board.scrollLeft = 5000;
      }, 100);
    }
    
  },


    created() {

      console.log('App mounted!');

      const db = firebase.database();

      var nouserloggedin = true;

      firebase.auth().onAuthStateChanged(async (data) => {
            if (firebase.auth().currentUser) {
                console.log("user is logged in");
                nouserloggedin = false;

                const profile = firebase.auth().currentUser; 

                const fullname = profile.displayName;

                const usricon = profile.photoURL;

                firebase.auth().setPersistence(firebase.auth.Auth.Persistence.LOCAL);

                document.getElementById('gbutton').style.display = "none";

                document.getElementById('usernameandpic').innerHTML = fullname;

                document.getElementById('user-icon').style.display = "block";

                document.getElementById('user-icon').src = usricon;

                var usrloggedin = true;

                if (usrloggedin) {
                  const pli = document.getElementById('p-l-i').style.display = "none";
                  this.loaduserdata();
                  usrloggedin = false;
                }

            } else {

              console.log('no user is logged in, inside currentuser async listener');
              console.log('display ask for user to log in to continue');

              const pli = document.getElementById('p-l-i').style.display = "block";



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

            }
          });
    },

    mounted(){

      // const a = this.scene;
      // const b = [a.children];
      // const c = [b.children];

      // for (var i = 0; i < c.length; i++) {
      //   if (c[i].checkdone == null || undefined) {
      //     c[i].checkdone = [];
      //   }
      // };

      console.log('in mounted, data loaded  ', this.alldata.tasks);

    },

    
    
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
.el-icon-check{
  border: 1px solid;
}
.dialogimported{
  border-radius: 10px;
  background-color: #343D46;
  background: #343D46;

}
.el-dialog{
  border-radius: 10px;
  background-color: #343D46;
  background: #343D46;
}

.dialogimported__body{
  padding-top: 0;
  padding-bottom: 0;
  color: #d1d1d2;
}

.dialogimported__header{
  padding:0;
  padding-bottom: 10px;
}

.dialogimported__headerbtn{
  display: none;
}
.el-icon-check{
  border: 1px solid;
}
.g-signin-button {
  display: inline-block;
  padding: 4px 8px;
  border-radius: 3px;
  background-color: #3c82f7;
  color: #fff;
  box-shadow: 0 3px 0 #0f69ff;
}
.g-signin-button:hover {
  cursor: pointer;
}
</style>

<style src="../style.css"/>