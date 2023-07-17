<script setup>
    import {ref,inject} from 'vue'

    const id = inject('rid');
    const apiKey = import.meta.env.VITE_ApiKey;
    const widget = ref('');
    const showbtn = ref(false);
    const btnlabel = ref('Get Recipe Nutrition')
    
    async function GetWidget(){
        btnlabel.value = 'Please wait...'
        const res = await fetch(`https://api.spoonacular.com/recipes/${id.value}/nutritionWidget?apiKey=${apiKey}&defaultCss=true`);
        const data = await res.text();
        widget.value = data;
        showbtn.value = true;
    }
</script>

<template>
    <div :style="{textAlign: 'center',marginTop:'0.5rem',marginBottom:'0.4rem'}">
        <el-button v-if="!showbtn" type="primary" @click="GetWidget">{{ btnlabel }}</el-button>
    </div>
    <div :style="{backgroundColor:'#001C30',color:'white',borderRadius:'12px',padding:'0.4rem'}" v-html="widget"></div>
</template>