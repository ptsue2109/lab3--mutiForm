<template>
   <div class="input-item">
     <!-- <label for="select-city">{{ label }}</label> -->
     <select
       id="select-city"
       name="select-city"
       :value="selectValue"
       @input="onSelect"
       @change="onChangeSelect"
     >
       <option v-for="item in list" :key="item.id" :value="item.name">
         <span>{{ item.name }}</span>
       </option>
     </select>
   </div>
 </template>
 
 <script>
 export default {
   name: "SelectCom",
   props: {
     list: Array,
     label: String,
     selectValue: String,
   },
   methods: {
     onSelect(e) {
       this.$emit("update:selectValue", e.target.value);
     },
     onChangeSelect() {
       this.$emit("onChangeSelect");
     },
   },
 };
 </script>
 
 <style scoped>
 .input-item {
   display: flex;
   flex-direction: column;
   width: 528px;
   text-align: start;
   margin-bottom: 10px;
 }
 .input-item select {
   padding: 10px;
   border: 1px solid #dbdbdb;
   outline: none;
   background: #fff;
   border-radius: 4px;
 }
 select::-webkit-scrollbar {
   display: none;
 }

 </style>
 