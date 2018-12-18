<template>
    <div id="detail">
        hello
        <div v-if="true">
            <div>{{bookdata.title}}</div>
        </div>
    </div>
</template>

<script>
export default {
    name:"details",
    props:{
        bookdata:Object
    },
    created(){
       
       console.log(this.bookdata);
       
    }
}
</script>

<style>

</style>
