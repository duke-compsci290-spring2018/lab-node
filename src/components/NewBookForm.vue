<template>
<div class="panel panel-default">
    <div class="panel-heading">
        <h3 class="panel-title">Add New Books</h3>
    </div>
    <div class="panel-body">
        <form id="form" class="form-inline" @submit.prevent="onSubmit">
            <div class="form-group">
                <label for="bookTitle">Title:</label>
                <input type="text" id="bookTitle" class="form-control" v-model="inputFields.title">
            </div>
            <div class="form-group">
                <label for="bookAuthor">Author:</label>
                <input type="text" id="bookAuthor" class="form-control" v-model="inputFields.author">
            </div>
            <input type="submit" class="btn btn-primary" value="Add Book">
        </form>
    </div>
</div>
</template>

<script>
// export anonymous object from this module so it can be accessed by others when imported
export default {
    name: 'App',
    // these values will be filled in by parent component, values must match tag's attribute names
    props: [
        'inputFields',
        'onSubmit'
    ]
}
</script>

<style lang="scss" scoped>
/* load global variable definitions so colors can be managed in a single place if needed */
@import "../assets/css/theme.scss";

.btn-primary {
    background-color: $submitColor;
}
</style>
