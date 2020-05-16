## GraphQL

### 개요
- 강력한 쿼리 언어
- REST에서 여러번 요청하는 것을 한번에 할 수 있음 (REST 보다 효율적이고 유연함)

### REST vs GraphQL
- REST는 여러가지 주소가 있으며 각각 항목에 대해서 요청해야 함
- GraphQL는 하나의 주소만 있으며 하나의 쿼리로 요청할수 있음
```
Author(id:2) {
    name
    books {
        name
        similar {
            name
        }
    }
}
```

### 구조 
- Client (Browser) : React App, Apollo
- Dummy Client : Graphiql
- Server (Node.js) : Express App, GraphQL Server
- Database : MongoDB(mLab)


### GraphiQL 쿼리
- 요청하면 data에 결과를 반환함
- 원하는 항목들만 쿼리 지원
    ```
    {
        books {
            name
            genre
            id
        }
    }
    ```
- 계층구조 지원
    ```
    {
        books {
            name
            genre
            author {
                name
                age
            }
        }
    }
    ```

### GraphQL 서버 만들기
- server 폴더 만들기
- Express 추가
    ```
    yarn add express
    ```
- app.js 만들기
    ```javascript
    const express = require('express');
    const app = express();

    app.listen(4000, () => {
        console.log('now listening for request on port 4000');
    });
    ```
- nodemon 추가 (변경시 자동 시작)
    ```
    npm install -g nodemon
    nodemon app
    ```
- GraphQL 패키지 설치
    ```
    yarn add graphql express-graphql
    ```
- GraphQL 서버
    - app.js에 기본 코드 추가
        ```javascript
        const express = require('express');
        const graphqlHTTP = require('express-graphql');

        const app = express();

        app.use('/graphql', graphqlHTTP({
            
        }));
        app.listen(4000, () => {
            console.log('now listening for request on port 4000');
        });
        ```
    - GraphQL 서버 구동시 Schema가 필요
        - Schema는 데이터 구조와 관계를 정의되어 있음. 
        - Schema 정의를 위해서는 다음 구현이 필요.
            - 데이터 타입 정의
            - 데이터 관계 정의
            - 루트(Root) 쿼리 정의
        - schema/schema.js 파일 생성
            ```javascript
            const graphql = require('graphql');
            const { GraphQLObjectType, GraphQLString, GraphQLSchema } = graphql;

            const BookType = new GraphQLObjectType({
                name: 'Book',
                fields: () => ({
                    id: { type: GraphQLString },
                    name: { type: GraphQLString },
                    genre: { type: GraphQLString }
                })
            });

            const RootQuery = new GraphQLObjectType({
                name: 'RootQueryType',
                fields: {
                    book: {
                        type: BookType,
                        args: { id: {type: GraphQLString } },
                        resolve(parent, args) {
                            // code to get data from db / other source
                        }
                    }
                }
            });

            module.exports = new GraphQLSchema({
                query: RootQuery
            });
            ```
        - app.js 수정
            ```javascript
            const express = require('express');
            const graphqlHTTP = require('express-graphql');
            const schema = require('./schema/schema');

            const app = express();

            app.use('/graphql', graphqlHTTP({
                schema: schema
            }));
            app.listen(4000, () => {
                console.log('now listening for request on port 4000');
            });
            ```
    - 데이터소스에서 데이터 가져오기 (resolve 구현)
        - 더머 데이터 만들기 (schema.js)
            ```javascript
            var books = [
                {name: 'Name of the Wind', genre: 'Fantasy', id:'1'},
                {name: 'The Final Empire', genre: 'Fantasy', id:'2'},
                {name: 'The Long Earth', genre: 'Sci-Fi', id:'3'},
            ];
            ```
        - 쿼리를 쉽게 하기 위해 lodash 패키지 추가
            ```
            yarn add lodash
            ```
        - 실제 데이터 찾기
            ```javascript
            const _ = require('lodash');

            const RootQuery = new GraphQLObjectType({
                name: 'RootQueryType',
                fields: {
                    book: {
                        type: BookType,
                        args: { id: {type: GraphQLString } },
                        resolve(parent, args) {
                            return _.find(books, { id: args.id });
                        }
                    }
                }
            });
            ```
    - 테스트를 위해 더미 클라이언트 만들기
        ```javascript
        const express = require('express');
        const graphqlHTTP = require('express-graphql');
        const schema = require('./schema/schema');

        const app = express();

        app.use('/graphql', graphqlHTTP({
            schema: schema,
            graphiql: true
        }));
        app.listen(4000, () => {
            console.log('now listening for request on port 4000');
        });
        ```
- GraphQL ID 타입
    - GraphQLID
        ```javascript        
        const { 
            GraphQLObjectType, 
            GraphQLString, 
            GraphQLSchema,
            GraphQLID
        } = graphql;

        const BookType = new GraphQLObjectType({
            name: 'Book',
            fields: () => ({
                id: { type: GraphQLID },
                name: { type: GraphQLString },
                genre: { type: GraphQLString }
            })
        });

        const RootQuery = new GraphQLObjectType({
            name: 'RootQueryType',
            fields: {
                book: {
                    type: BookType,
                    args: { id: {type: GraphQLID } },
                    resolve(parent, args) {
                        return _.find(books, { id: args.id });
                    }
                }
            }
        });
        ```
    - 여러 타입 추가
        ```javascript
        const graphql = require('graphql');
        const _ = require('lodash');

        const { 
            GraphQLObjectType, 
            GraphQLString, 
            GraphQLSchema,
            GraphQLID,
            GraphQLInt
        } = graphql;

        // dummy data
        var books = [
            {name: 'Name of the Wind', genre: 'Fantasy', id: '1'},
            {name: 'The Final Empire', genre: 'Fantasy', id: '2'},
            {name: 'The Long Earth', genre: 'Sci-Fi', id: '3'},
        ];
        var authors = [
            {name: 'Patrick Rothfuss', age: 44, id: '1'},
            {name: 'Brandon Sanderson', age: 42, id: '2'},
            {name: 'Terry Pratchett', age: 66, id: '3'},
        ];

        const BookType = new GraphQLObjectType({
            name: 'Book',
            fields: () => ({
                id: { type: GraphQLID },
                name: { type: GraphQLString },
                genre: { type: GraphQLString }
            })
        });

        const AuthorType = new GraphQLObjectType({
            name: 'Author',
            fields: () => ({
                id: { type: GraphQLID },
                name: { type: GraphQLString },
                age: { type: GraphQLInt }
            })
        });

        const RootQuery = new GraphQLObjectType({
            name: 'RootQueryType',
            fields: {
                book: {
                    type: BookType,
                    args: { id: {type: GraphQLID } },
                    resolve(parent, args) {
                        return _.find(books, { id: args.id });
                    }
                },
                author: {
                    type: AuthorType,
                    args: { id: {type: GraphQLID }},
                    resolve(parent, args) {
                        return _.find(authors, { id: args.id });
                    }
                }
            }
        });

        module.exports = new GraphQLSchema({
            query: RootQuery
        });

        ```
    - 타입 관계
        ```javascript
        const graphql = require('graphql');
        const _ = require('lodash');

        const { 
            GraphQLObjectType, 
            GraphQLString, 
            GraphQLSchema,
            GraphQLID,
            GraphQLInt
        } = graphql;

        // dummy data
        var books = [
            {name: 'Name of the Wind', genre: 'Fantasy', id: '1', authorId: '1'},
            {name: 'The Final Empire', genre: 'Fantasy', id: '2', authorId: '2'},
            {name: 'The Long Earth', genre: 'Sci-Fi', id: '3', authorId: '3'},
        ];
        var authors = [
            {name: 'Patrick Rothfuss', age: 44, id: '1'},
            {name: 'Brandon Sanderson', age: 42, id: '2'},
            {name: 'Terry Pratchett', age: 66, id: '3'},
        ];

        const BookType = new GraphQLObjectType({
            name: 'Book',
            fields: () => ({
                id: { type: GraphQLID },
                name: { type: GraphQLString },
                genre: { type: GraphQLString },
                author: { 
                    type: AuthorType,
                    resolve(parent, args) {
                        return _.find(authors, {id: parent.authorId});
                    }
                }
            })
        });

        const AuthorType = new GraphQLObjectType({
            name: 'Author',
            fields: () => ({
                id: { type: GraphQLID },
                name: { type: GraphQLString },
                age: { type: GraphQLInt }
            })
        });

        const RootQuery = new GraphQLObjectType({
            name: 'RootQueryType',
            fields: {
                book: {
                    type: BookType,
                    args: { id: {type: GraphQLID } },
                    resolve(parent, args) {
                        return _.find(books, { id: args.id });
                    }
                },
                author: {
                    type: AuthorType,
                    args: { id: {type: GraphQLID }},
                    resolve(parent, args) {
                        return _.find(authors, { id: args.id });
                    }
                }
            }
        });

        module.exports = new GraphQLSchema({
            query: RootQuery
        });

        ```
    - 리스트형 관련
        ```javascript
        const graphql = require('graphql');
        const _ = require('lodash');

        const { 
            GraphQLObjectType, 
            GraphQLString, 
            GraphQLSchema,
            GraphQLID,
            GraphQLInt,
            GraphQLList
        } = graphql;

        // dummy data
        var books = [
            {name: 'Name of the Wind', genre: 'Fantasy', id: '1', authorId: '1'},
            {name: 'The Final Empire', genre: 'Fantasy', id: '2', authorId: '2'},
            {name: 'The Long Earth', genre: 'Sci-Fi', id: '3', authorId: '3'},
            {name: 'The Hero of Ages', genre: 'Fantasy', id: '4', authorId: '2'},
            {name: 'The Colour of Magic', genre: 'Fantasy', id: '5', authorId: '3'},
            {name: 'The Light Fantastic', genre: 'Fantasy', id: '6', authorId: '3'},
        ];
        var authors = [
            {name: 'Patrick Rothfuss', age: 44, id: '1'},
            {name: 'Brandon Sanderson', age: 42, id: '2'},
            {name: 'Terry Pratchett', age: 66, id: '3'},
        ];

        const BookType = new GraphQLObjectType({
            name: 'Book',
            fields: () => ({
                id: { type: GraphQLID },
                name: { type: GraphQLString },
                genre: { type: GraphQLString },
                author: { 
                    type: AuthorType,
                    resolve(parent, args) {
                        return _.find(authors, {id: parent.authorId});
                    }
                }
            })
        });

        const AuthorType = new GraphQLObjectType({
            name: 'Author',
            fields: () => ({
                id: { type: GraphQLID },
                name: { type: GraphQLString },
                age: { type: GraphQLInt },
                books: {
                    type: new GraphQLList(BookType),
                    resolve(parent, args) {
                        return _.filter(books, { authorId: parent.id });
                    }
                }
            })
        });

        const RootQuery = new GraphQLObjectType({
            name: 'RootQueryType',
            fields: {
                book: {
                    type: BookType,
                    args: { id: {type: GraphQLID } },
                    resolve(parent, args) {
                        return _.find(books, { id: args.id });
                    }
                },
                author: {
                    type: AuthorType,
                    args: { id: {type: GraphQLID }},
                    resolve(parent, args) {
                        return _.find(authors, { id: args.id });
                    }
                }
            }
        });

        module.exports = new GraphQLSchema({
            query: RootQuery
        });

        ```
    - Root 쿼리
        ```javascript
        const RootQuery = new GraphQLObjectType({
            name: 'RootQueryType',
            fields: {
                book: {
                    type: BookType,
                    args: { id: {type: GraphQLID } },
                    resolve(parent, args) {
                        return _.find(books, { id: args.id });
                    }
                },
                author: {
                    type: AuthorType,
                    args: { id: {type: GraphQLID }},
                    resolve(parent, args) {
                        return _.find(authors, { id: args.id });
                    }
                },
                books: {
                    type: new GraphQLList(BookType),
                    resolve(parent, args) {
                        return books;
                    }
                },
                authors: {
                    type: new GraphQLList(AuthorType),
                    resolve(parent, args) {
                        return authors;
                    }
                }
            }
        });
        ```
    - MongoDB 사용하기
        - gql-ninja 데이터베이스 생성
        - mongoose 설치
            ```
            yarn add mongoose
            ```
        - app.js
            ```javascript
            const express = require('express');
            const graphqlHTTP = require('express-graphql');
            const schema = require('./schema/schema');
            const mongoose = require('mongoose');

            const app = express();

            mongoose.connect('mongodb://localhost:27017/gql-ninja');
            mongoose.connection.once('open', () => {
                console.log('connected to database');
            });

            app.use('/graphql', graphqlHTTP({
                schema: schema,
                graphiql: true
            }));
            app.listen(4000, () => {
                console.log('now listening for request on port 4000');
            });
            ```
        - models 폴더 생성, book.js와 auther.js 파일 생성
        - book.js
            ```javascript
            const mongoose = require('mongoose');
            const Schema = mongoose.Schema;

            const bookSchema = new Schema({
                name: String,
                genre: String,
                authorId: String
            });

            module.exports = mongoose.model('Book', bookSchema);
            ```
        - author.js
            ```javascript
            const mongoose = require('mongoose');
            const Schema = mongoose.Schema;

            const authorSchema = new Schema({
                name: String,
                age: Number
            });

            module.exports = mongoose.model('Author', authorSchema);
            ```
        - Mutations (Add, Remove, Delete)
            - GraphQL에서는 어떻게 데이터를 조작할 수 있는지 정의해야 함.
            ```javascript
            const Mutation = new GraphQLObjectType({
                name: 'Mutation',
                fields: {
                    addAuthor: {
                        type: AuthorType,
                        args: {
                            name: { type: GraphQLString },
                            age: { type: GraphQLInt },
                        },
                        resolve(parent, args) {
                            let author = new Author({
                                name: args.name,
                                age: args.age
                            });
                            return author.save();
                        }
                    },
                    addBook: {
                        type: BookType,
                        args: {
                            name: { type: GraphQLString },
                            genre: { type: GraphQLString },
                            authorId: { type: GraphQLID }
                        },
                        resolve(parent, args) {
                            let book = new Book({
                                name: args.name,
                                genre: args.genre,
                                authorId: args.authorId
                            });
                            return book.save();
                        }
                    },
                }
            });
            ```
        - Non Null
            ```javascript
            const Mutation = new GraphQLObjectType({
                name: 'Mutation',
                fields: {
                    addAuthor: {
                        type: AuthorType,
                        args: {
                            name: { type: new GraphQLNonNull(GraphQLString) },
                            age: { type: new GraphQLNonNull(GraphQLInt) },
                        },
                        resolve(parent, args) {
                            let author = new Author({
                                name: args.name,
                                age: args.age
                            });
                            return author.save();
                        }
                    },
                    addBook: {
                        type: BookType,
                        args: {
                            name: { type: new GraphQLNonNull(GraphQLString) },
                            genre: { type: new GraphQLNonNull(GraphQLString) },
                            authorId: { type: new GraphQLNonNull(GraphQLID) }
                        },
                        resolve(parent, args) {
                            let book = new Book({
                                name: args.name,
                                genre: args.genre,
                                authorId: args.authorId
                            });
                            return book.save();
                        }
                    },
                }
            });
            ```
### GraphQL 클라이언트 만들기