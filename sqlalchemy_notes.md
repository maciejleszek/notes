
## SQLAlchemy

1. SQLAlchemy provides an object-relational mapper (ORM) that allows developers to work with databases using Python objects rather than raw SQL queries.
It allows to write SQL queries in Python syntax

2. It has two components:
    - SQLAlchemy Core – which is similar to traditional SQL. It views your data in a schema-centric view.

    - SQLAlchemy ORM – which provides a high-level abstraction to write SQL queries in Python objects

### Define the schema

3. Defining a schema in SQLAlchemy ORM follows the same pattern as defining a native Python object. You create a class that inherits from a base class called declarative_base

### Establish relationships between tables

4. There are three main types of relationships that relate tables with each other: 

- 1-to-1
- 1-to-many
- many-to-many relationships

5. 
Example of backreferencing the author column in the Article class. This creates a new column called author in the articles table
```
Base = declarative_base()

class Article(Base):
    __tablename__ = 'articles'

    id = Column(Integer(), primary_key=True)
    slug = Column(String(100), nullable=False, unique=True)
    title = Column(String(100), nullable=False)
    created_on = Column(DateTime(), default=datetime.now)
    updated_on = Column(DateTime(), default=datetime.now, onupdate=datetime.now)
    content = Column(Text)
    author_id = Column(Integer(), ForeignKey('authors.id'))

class Author(Base):
    __tablename__ = 'authors'

    id = Column(Integer(), primary_key=True)
    firstname = Column(String(100))
    lastname = Column(String(100))
    email = Column(String(255), nullable=False)
    joined = Column(DateTime(), default=datetime.now)

    articles = relationship('Article', backref='author')
``` 

### Create the tables

6. What we have done so far is define two mapped tables, not create them. You need to create these objects to be able to query from the tables
Use the `create_all()` method, which requires an `engine` instance like the following:

`Base.metadata.create_all(engine)`

### Interacting with data

7. The SQLAlchemy ORM needs a SQLAlchemy session to interact with the database. What I mean by interaction is to be able to insert, get, update, and delete data from the database. It also includes ordering and grouping data

Create a new session with the following:

```
from sqlalchemy.orm import sessionmaker

Session = sessionmaker(bind=engine)
session = Session()
```

### Insert data

8. Inserting data is as simple as initializing an instance of a class. You create the instance of the table definition with your desired parameters. You also need to take into consideration the required fields and the data types of each column

```
ahmed = Author(
    firstname="Ahmed",
    lastname="Mohammed",
    email="ahmed_email@gmail.com"
)
```

ahmed is now an author defined as a Python object. Note that I used only the required fields. You can add more arguments from the author’s schema as you wish.

9. Create an article:

```
article1 = Article(
    slug="clean-python",
    title="How to Write Clean Python",
    content="Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.",
    author=ahmed
    )
session.add(article1)
session.commit()
```

After `article1` is defined, you need to add the created instance to the session using `session.add(article1)`. You can’t query `article1` just yet. You need to commit that insert change to the database using `session.commit()`

10. To flush a session: `session.flush()` it communicates a series of operations to the database (insert, update, delete). The database maintains them as pending operations in a transaction. The changes aren't persisted permanently to disk, or visible to other transactions until the database receives a COMMIT for the current transaction (which is what `session.commit()` does).

11. Instead of calling the `add()` method multiple times, you could add multiple new records like so:

`session.add_all([article1, article2, article3])`

### Update data

12. Updating data in SQLAlchemy is similar to inserting. If you know the object already, it’s as simple as changing an attribute value for a Python objec

13. slug - URL/encoding friendly:
- Title: "How to Learn Python"
- Slug: "how-to-learn-python"

14. Or if you don’t have the object already, you can query it using the `session.query()` and then filter the output, like the following:
```
>>> article_query = session.query(Article)
>>> clean_py_article = article_query.filter(Article.slug == "clean-python").first()
>>> clean_py_article.title = "Clean Python"
>>> clean_py_article.title
"Clean Python"
```

So the `article_query` is a query object that you applied a `filter()` method on to retrieve the article with the clean-python slug. Then you used the `first()` method to get the article object. And then you edited the title of that article.

15. You can also update queries in place using the `update()` method. Here is how:

```
>>> article_query = session.query(Article)
>>> clean_py_query = article_query.filter(Article.slug=="clean-python")
>>> clean_py_query.update({Article.title: "Cleaner Python"})
>>> clean_py_query.first().title 'Cleaner Python'
```

So now, the `clean_py_query` is a Query object. Applying the `update()` method on that object, as above, will update the article’s title in place.
To query it, use `first()` and then see if the title is changed with the attribute `title`

### Query data
You probably noticed that we used the `query()` method in the previous section. To start querying, use the `session` instance and pass the class definition of your database.

### SELECT

16. One type of query is to use  `SELECT *` from the table to retrieve all records. You can use the `all()` method:
```
session.query(Article).all()
```
This retrieves a list of all Article instances in the database.

17. If you want to retrieve each object record, you can iterate over each object with this use case:
```
articles = session.query(Article).all()
for article in articles:
    print(article.title) # or any other attribute
```

As you can see, `articles` is an iterable — which is memory intensive when you loop over, especially when the database is large.

Or you can alternatively omit the `all()` method and use an iterator of article objects:
```
articles_objs = session.query(Article)
for article in articles_objs:
    print(article.title)
```

18. You might not be interested in selecting all columns. Here is how you select specific columns in your query:
```
session.query(Article.slug, Article.title)[2]
```

### ORDER BY
19. If you want to sort data in the same way that the ORDER BY clause does in SQL, use the order_by() method on your Query object.
```
for article in session.query(Article).order_by(Article.title.desc()):
    print(article.title)
```
