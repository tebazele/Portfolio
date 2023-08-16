# Setting up a Sqlite3 Database within Flask - Two ways

Python was the first language I learned through Codecademy. It seemed approachable as well as incredibly powerful, so after my coding bootcamp at CodeWorks, I decided to come back to it and see if I could write a Python server as the backend to one of my Vue projects using Flask.

## Setting up a virtual environment

Up at the top of the stack in the web development world, it feels like you can't write a line of code without installing a bunch of packages. I'm pretty familiar at this point with Node and Node Package Manager and how Node Version Manager can help manage all the different versions from Node updating constantly.

A Python virtual environment is kind of similar--the libraries of all the dependencies are constantly updating, so you kind of want to lock in whatever version you're using locally and tie it to the project. That's the job of the venv, to create a space to `pip install flask` so your project doesn't break as soon as Flask gets an update.

Once I'd created the venv and pip installed flask, I was ready to dive into the project.

## Setting up a sqlite database

Flask comes with sqlite3, so that seemed like the easiest thing to start playing around with. It's pretty straightforward in a lot of ways. Sql commands are very much plain English in a lot of ways, which is nice, and so are the python commands used to access the database: connect, execute, commit, close.

The first thing I did was get a VS code extension to view .sqlite files. I figured having access to my data within my project would be useful (it has been).

I only wanted to create the sqlite database file once when I started my project, and there are a couple of ways to accomplish this. I read over a few tutorials and decided to write a function called `connect()` that I would only call in app.py (the file that initializes the flask project) if there was no file called _db.sqlite_.

```python
from flask import Flask
import os
import db

app = Flask(__name__)

if not os.path.isfile('db.sqlite'):
  db.connect()
```

The db.py file contained the connect function:

```python
import sqlite3

def connect():
  conn = sqlite3.connect('db.sqlite')
  cur = conn.cursor()

  cur.execute(
    "CREATE TABLE IF NOT EXISTS users (id INTEGER PRIMARY KEY AUTO_INCREMENT, name TEXT, email TEXT)"
  )
  conn.commit()
  conn.close()
```

## SQLAlchemy

After successfully creating a database with a users table, I found a tutorial that used yet another library to make things _event easier_, so of course, I had to try it out.

I imported SQLAlchemy from flask_sqlalchemy and created a db_creation.py file. SQLAlchemy wants to be used within a flask context, so I also imported Flask into this file as well.

```python
app = Flask(__name__)

app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///instance/db.sqlite'
db = SQLAlchemy(app)

app.app_context().push()
```

I'm not entirely sure what the last line there is doing, but it seems that when you init a flask app these days, Flask creates a folder called "instance" that holds stuff like databases that you only create once at the beginning of the project, and that last line helps the app find the database within the instance folder whenever it goes looking for it.

### In the next blog post, I'll talk about routes I wrote using Flask to access my database
