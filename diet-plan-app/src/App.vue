<script setup>
import {ref} from 'vue'
import PlanInfo from './components/PlanInfo.vue'

const dialogVisible = ref(true);
const url = ref('')
const inputs = ref({})
const diets= ['Gluten-Free','Ketogenic','Vegetarian','Lacto-Vegetarian','Ovo-Vegetarian','Vegan','Pescetarian','Paleo','Low-FODMAP','Whole30'];
const plans = ref({});
const apiKey = import.meta.env.VITE_ApiKey;
const plan = ref([]);
const ShowInfo = ref(false);
const load = ref(false)

async function GetDiets(){
  if(inputs.value.timeFrame){
    url.value = `https://api.spoonacular.com/mealplanner/generate?apiKey=${apiKey}`;
  
    for(let i in inputs.value){
      if(inputs.value[i]){
        url.value +=`&${i}=${inputs.value[i]}`
      }
    }

    const res = await fetch(url.value);
    const data = await res.json();
    if(inputs.value.timeFrame==='week'){
      plans.value = data.week;
    }
    else{
      plans.value = data;
    }
    dialogVisible.value = false;
  }
  else{
    alert("Please select 'Time Intervel'")
  }
}

async function GetInfo(id){
  load.value = true;
  const url = `https://api.spoonacular.com/recipes/${id}/analyzedInstructions?apiKey=${apiKey}`;
  const res = await fetch(url);
  const data = await res.json();
  plan.value = data[0].steps;
  ShowInfo.value = true;
  load.value = false;
}
</script>

<template>

<div class="header">
  <p><strong>Diet-Plan</strong></p>
</div>
<div class="header1">
  <el-button type="primary" @click="dialogVisible=true" id="header-action" size="small" v-if="dialogVisible==false">Open Diet Plan</el-button><br/>
</div>

<el-dialog v-model="dialogVisible" title="Provide Diet Input" width="70%">
  <div>
    <label for="timeframe">Time frame : </label><br/>
    <select v-model="inputs.timeFrame" name="timeframe">
      <option disabled value="">Please select one</option>
      <option value="day">Day</option>
      <option value="week">Week</option>
    </select> <br/><br/>

    <label for="diet">Diet : </label><br/>
    <select v-model="inputs.diet" name="diet">
      <option v-for="i in diets" :key="i" :value="i">{{ i }}</option>
    </select><br/><br/>

    <label for="target">Target calories : </label><br/>
    <input v-model="inputs.targetCalories" name="target" type="number" placeholder="Ex: 2000"/><br/><br/>

    <label for="exclude">Exclude (if any, seperate by ',') : </label><br/>
    <input v-model="inputs.exclude" name="exclude" type="text" placeholder="Ex: Strawberry"/><br/><br/>

    <el-button type="primary" plain @click="GetDiets">Search</el-button>
    <el-button type="danger" plain @click="dialogVisible=false">Close</el-button>
  </div>
</el-dialog>

<!--Content-->
<template v-if="inputs.timeFrame==='week'" v-for="(i,key) in plans">
  <h2>{{ key }}</h2>
  <div class="grid">
    <div v-for="j in i.meals" :key="j.id">
      <el-card>
        <img :src="`https://spoonacular.com/recipeImages/${j.id}-312x231.jpg`"/>
        <p><strong>{{ j.title }}</strong></p>
        <el-tag class="ml-2" type="info">{{ j.readyInMinutes }} mins</el-tag>
        <el-tag class="ml-2" type="warning">{{ j.servings }} servings</el-tag>
        <br/><el-button @click="GetInfo(j.id)" type="success" plain class="button c-btn">{{ load ? 'loading...' : 'View' }}</el-button>
      </el-card>
    </div>
  </div>
</template>

<template v-else>
  <div class="grid">
    <div v-for="x in plans.meals" :key="x.id">
      <el-card>
          <img :src="`https://spoonacular.com/recipeImages/${x.id}-312x231.jpg`"/>
          <p><strong>{{ x.title }}</strong></p>
          <el-tag class="ml-2" type="info">{{ x.readyInMinutes }} mins</el-tag>
          <el-tag class="ml-2" type="warning">{{ x.servings }} servings</el-tag>
          <br/><el-button @click="GetInfo(x.id)" type="success"  plain class="button c-btn">{{ load ? 'loading...' : 'View' }}</el-button>
      </el-card>
    </div>
  </div>
</template>

<template v-if="ShowInfo">
  <PlanInfo @response="(e) => ShowInfo=e" :plandata="plan" />
</template>
 
</template>

<style scoped>


input, select{
  width: 200px;
  height: 30px;
}

.header{
  background-color: rgb(45, 45, 46);
  color: white;
  padding: 0.2rem;
  text-align: center;
}

.header1{
  background-color: white;
  color: black;
  padding: 0.2rem;
  text-align: center;
  margin-bottom: 0.4rem;
  margin-top: 0.2rem;
}

.grid { 
  display: grid; 
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); 
  grid-gap: 20px; 
}

.c-btn{
  margin-top: 0.6rem;
}



</style>
