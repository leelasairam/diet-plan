<script setup>
    import {ref} from 'vue'
    import RecipeWidget from './RecipeWidget.vue';
    const props = defineProps({
        plandata: Array
    })
    const dialogVisible = ref(true);

    const emit = defineEmits(['response'])

    function Close(){
        emit('response', false)
    }
</script>

<template>
    <el-dialog v-model="dialogVisible" title="Diet Info" width="80%" :before-close="Close">
        <div>
            <template v-for="i in props.plandata" :key="i.number">
                <p><strong>Step : {{ i.number }}</strong> : {{ i.step }}</p>
                <div class="grid">
                    <div v-for="j in i.ingredients" :key="j.id">
                        <div>
                            <el-avatar :size="50" :src="`https://spoonacular.com/cdn/ingredients_100x100/${j.image}`" />
                            <p>{{ j.name }}</p>
                        </div>
                    </div>
                </div>
                <div class="grid">
                    <div v-for="k in i.equipment" :key="k.id">
                        <div>
                            <el-avatar :size="50" :src="`https://spoonacular.com/cdn/equipment_100x100/${k.image}`" />
                            <p>{{ k.name }}</p>
                        </div>
                    </div>
                </div>
            </template>
        </div>
        <br/>
        <div>
            <RecipeWidget />
        </div>
    </el-dialog>


</template>

<style scoped>
.grid { 
  display: grid; 
  grid-template-columns: repeat(auto-fit, minmax(100px, 0.1fr)); 
  grid-gap: 10px; 
}


</style>