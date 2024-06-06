---
title: "Python"
description: "Quick-starts and guidelines for Python development."
lead: "Quick-starts and guidelines for Pythondevelopment"
date: 2024-06-06
lastmod: 2024-06-06
draft: false
images: []
menu:
  docs:
    parent: "programming"
weight: 10
toc: true
---

## Python Virtual Environment

1. Installation:
   ```sh
   pip install virtualenv
   ```

2. Creation :
   ```sh
   python -m venv PATH
   ```

   For example:
   ```sh
   python -m venv venv
   ```

3. Activate it!

   * Linux or mac:
     ```sh
     source venv/bin/activate
     ```
   * Windows (cdm or Powershell):
     ```sh
     venv\Scripts\activate.bat
     venv\Scripts\activate.ps1
     ```

## Flask Web API example

Create a file called **main.py** with the following sample code:

```py
#
# DB
#
import sqlite3

def connect_to_db():
    conn = sqlite3.connect('database.db')
    return conn

def create_db_table():
    try:
        conn = connect_to_db()
        conn.execute('''
            CREATE TABLE users (
                id INTEGER PRIMARY KEY NOT NULL,
                name TEXT NOT NULL
            );
        ''')

        conn.commit()
        print('User table created successfully')
    except Exception as error:
        print(error)
        print('User table creation failed - Maybe table')
    finally:
        conn.close()

#
# Repo
#
def insert_user(user):
    inserted_user = {}
    try:
        conn = connect_to_db()
        cur = conn.cursor()
        cur.execute('''
            INSERT INTO users (name)
            VALUES (?)
            ''',
            # If there's a single argmuent,
            # do not forget to add a comma
            # to form a tuple
            (user['name'],)
        )
        conn.commit()
        inserted_user = get_user_by_id(cur.lastrowid)
    except Exception as error:
        print(error)
        conn.rollback()

    finally:
        conn.close()

    return inserted_user

def update_user(user):
    updated_user = {}
    try:
        conn = connect_to_db()
        cur = conn.cursor()
        cur.execute('''
            UPDATE users
            SET name = ?
            WHERE id = ?
            ''',
            (user['name'], user['id'])
        )
        conn.commit()
        # return the user
        updated_user = get_user_by_id(user['id'])

    except Exception as error:
        print(error)
        conn.rollback()
        updated_user = {}
    finally:
        conn.close()

    return updated_user

def delete_user(id):
    message = {}
    try:
        conn = connect_to_db()
        conn.execute('''
            DELETE from users
            WHERE id = ?
            ''',
            (id,)
        )
        conn.commit()
        message['status'] = 'User deleted successfully'
    except Exception as error:
        print(error)
        conn.rollback()
        message['status'] = 'Cannot delete user'
    finally:
        conn.close()

    return message

def get_users():
    users = []
    try:
        conn = connect_to_db()
        conn.row_factory = sqlite3.Row
        cur = conn.cursor()
        cur.execute('''
            SELECT
                id,
                name
            FROM users
            '''
        )
        rows = cur.fetchall()

        # convert row objects to dictionary
        for row in rows:
            user = {}
            user['id'] = row['id']
            user['name'] = row['name']
            users.append(user)

    except Exception as error:
        print(error)
        users = []

    return users


def get_user_by_id(user_id):
    user = {}
    try:
        conn = connect_to_db()
        conn.row_factory = sqlite3.Row
        cur = conn.cursor()
        cur.execute('''
            SELECT
                id,
                name
            FROM users
            WHERE id = ?
            ''',
            (user_id,)
        )
        row = cur.fetchone()

        # convert row object to dictionary
        user['id'] = row['id']
        user['name'] = row['name']
    except Exception as error:
        print(error)
        user = {}

    return user

#
# Flask routes
#
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route('/api/users', methods=['GET'])
def api_get_users():
    return jsonify(get_users())

@app.route('/api/users/<id>', methods=['GET'])
def api_get_user(id):
    return jsonify(get_user_by_id(id))

@app.route('/api/users',  methods = ['POST'])
def api_add_user():
    user = request.get_json()
    return jsonify(insert_user(user))

@app.route('/api/users',  methods = ['PUT'])
def api_update_user():
    user = request.get_json()
    return jsonify(update_user(user))

@app.route('/api/users/<id>',  methods = ['DELETE'])
def api_delete_user(id):
    return jsonify(delete_user(id))

if __name__ == '__main__':
    create_db_table()
```

Create a venv and install dependencies:

```sh
pip install Flask
pip install db-sqlite3
pip3 freeze > requirements.txt
```

Run migration to create database:
```sh
python main.py
```

Run project!
```sh
flask --app main run
```

Finally run some of the following requests from VSCodium/VSCode with [rest client extension](https://marketplace.visualstudio.com/items?itemName=humao.rest-client) installed (you can create a file with **.http** extension).

```http
###
#
# Find all
GET http://127.0.0.1:5000/api/users HTTP/1.1

###
#
# Find by id
GET http://127.0.0.1:5000/api/users/1 HTTP/1.1

###
#
# Insert

POST http://127.0.0.1:5000/api/users HTTP/1.1
content-type: application/json

{
    "name": "sample"
}

###
#
# Update
PUT http://127.0.0.1:5000/api/users HTTP/1.1
content-type: application/json

{
    "id": 1,
    "name": "sample updated"
}

###
#
# Update
DELETE http://127.0.0.1:5000/api/users/2 HTTP/1.1
content-type: application/json
```
