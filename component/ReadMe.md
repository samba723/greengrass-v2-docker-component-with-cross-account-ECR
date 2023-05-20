# Create Virtual environment for creating component
    $ python3 -m virtualenv docker-component

# Activate environment
    $ source ./docker-component/bin/activate

# Install Flask
    $ pip install flask

# Freeze python modules
    $ python3 -m pip freeze > requirements.txt

# Created app.py file with flask rest API code
    from flask import Flask
    from markupsafe import escape
    app = Flask(__name__)

    @app.route('/')
    def hello_world():
        return 'Hello, this is docket component'

    @app.route('/user/<username>')
    def show_user_profile(username):
        return f'User {escape(username)}'

    @app.route('/post/<int:post_id>')
    def show_post(post_id):
        return f'Post {post_id}'

    @app.route('/path/<path:subpath>')
    def show_subpath(subpath):
        return f'Subpath {escape(subpath)}'

# Created Dockerfile for creating docker image
    # syntax=docker/dockerfile:1

    FROM python:latest

    WORKDIR /app

    COPY requirements.txt requirements.txt
    RUN pip3 install -r requirements.txt

    COPY . .

    CMD ["python3", "-m" , "flask", "run", "--host=0.0.0.0"]

# Create docker image
    $ docker build --tag docker-component .