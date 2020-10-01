<template>
	<div class="topnav">
     <div class="boardmenu">
       <el-dropdown>
		  <span class="el-dropdown-link">
		  	<p><input class="boardname" >
		    <i class="el-icon-arrow-down el-icon--right"></i>
			</input>
			</p>
		  </span>
		  <el-dropdown-menu slot="dropdown">
		    <el-dropdown-item>+ New Board</el-dropdown-item>
		  </el-dropdown-menu>
		</el-dropdown>
      </div> 
      <div class="logo">&#9782; tasker</div>
      <div class="usermenu">
        <div class="usericon"></div>
       
      <!--         <input type="text" class="usernameinput" v-model="username"> -->
        
     </div>
    </div>
</template>

<script>
import Vue from 'vue';
import Element from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';
import NewScene from './cardscene.vue';
import store from './cardscene.vue';

Vue.use(Element);
	
export default {

  name: "Topnavigation",


  data: function() {

  	console.log(store);

    return {


    }

},
}

</script>

<!-- STYLE -->

<style>

  .el-dropdown-link {
    cursor: pointer;
    color: #c6e2ff;
  }
  .el-icon-arrow-down {
    font-size: 14px;
  }

</style>

<style src="../style.css"/>