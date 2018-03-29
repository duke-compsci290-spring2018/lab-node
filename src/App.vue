<template>
<div id="app" class="container">
    <div class="page-header">
        <h1>My Favorite Books</h1>
    </div>
    <!-- Use component's template instead of writing it directly, pass parameters as attributes -->
    <new-book-form
        :inputFields="newBookInput"
        :onSubmit="addBook">
    </new-book-form>
    <div class="panel panel-default">
        <div class="panel-heading">
            <h3 class="panel-title">Book List</h3>
        </div>
        <div class="panel-body">
            <table class="table">
                <thead>
                    <tr>
                        <th class="first"></th>
                        <th>Title</th>
                        <th>Author</th>
                        <th>Year</th>
                        <th></th>
                    </tr>
                </thead>
                <!-- Use component's template instead of writing it directly, pass parameters as attributes -->
                <book-info v-for="book in books"
                    :key="book.title"
                    :book="book"
                    :onRemove="removeBook">
                </book-info>
            </table>
        </div>
    </div>
</div>
</template>

<script>
// use Firebase to save data user entered
import Firebase from 'firebase';
// use local components, note .vue extension is assumed so not necessary to include
import NewBookForm from './components/NewBookForm'
import BookInfo from './components/BookInfo'

var config = {
    apiKey: "AIzaSyALmW2B2aJOtwCbikL6fI3RyfZejpKvgYI",
    authDomain: "book-list-70bcc.firebaseapp.com",
    databaseURL: "https://book-list-70bcc.firebaseio.com",
    projectId: "book-list-70bcc",
    storageBucket: "book-list-70bcc.appspot.com",
    messagingSenderId: "167870498358"
}
var db = Firebase.initializeApp(config).database()
var booksRef = db.ref('books')

// export anonymous object from this module so it can be accessed by others when imported
export default {
    name: 'app',
    // NOTE in a component, data must be a function that returns a NEW version of the values
    data () {
        return {
            newBookInput: {
                title: '',
                author: ''
            }
        }
    },
    // connect to single Firebase resource
    firebase: {
        books: booksRef
    },
    // components (HTML, CSS, and JS) used by this app
    components: {
        // imported components to use in HTML template
        NewBookForm,
        BookInfo
    },
    // functions you want to be called from HTML code
    methods: {
        // add book to the database after getting details from Google Books API
        addBook () {
            this.getBookDetails(this.newBookInput.title, this.newBookInput.author)
            this.resetInputValues()
        },
        // remove book from the database
        removeBook (book) {
            booksRef.child(book['.key']).remove()
        },
        // given title and author, get JSON data about the book to display
        getBookDetails (title, author) {
            // encode user input for inclusion within URL
            var encodedTitle = encodeURIComponent(title),
                encodedAuthor = encodeURIComponent(author),
                url = `https://www.googleapis.com/books/v1/volumes?q=intitle:${encodedTitle}+inauthor:${encodedAuthor}&maxResults=1`
            // get JSON data to fill in specific book data
            fetch(url).then(response => response.json())
                      .then(data => {
                            // only one result is returned to reduce data load, but it still is returned as an Array
                            var bookInfo = data.items[0]
                            // include data retrieved from JSON API in book fields
                            booksRef.push({
                                title: title,
                                author: author,
                                year: bookInfo.volumeInfo.publishedDate.split('-')[0],
                                description: bookInfo.volumeInfo.description,
                                url: this.makeURLSecure(bookInfo.volumeInfo.infoLink),
                                imgUrl: this.makeURLSecure(bookInfo.volumeInfo.imageLinks.thumbnail)
                            })
                       })
                      .catch(error => console.log(error))
        },
        // clear input form to prepare for the next entry
        resetInputValues () {
            this.newBookInput.title = ''
            this.newBookInput.author = ''
        },
        // some URLs in the book data still refer to http directly instead of https, so convert them
        makeURLSecure (url) {
            return url.replace(/^http:/, 'https:')
        }
    }
}
</script>

<style lang="scss" scoped>
/* load global variable definitions so colors can be managed in a single place if needed */
@import "./assets/css/theme.scss";

#app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    color: $formColor;
    margin-top: 20px;
}

.table {
    color: $tableColor;
}
</style>
