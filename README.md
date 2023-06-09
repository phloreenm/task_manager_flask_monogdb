![CI logo](https://codeinstitute.s3.amazonaws.com/fullstack/ci_logo_small.png)

# Steps into creating a basic Flask app, conect it to the database, create templates.
## **Create BASIC Flask project:**
- create database on MongoDB:
    - create Database -> DB name -> collection name (first collection in this db)
- instal Flask: in terminal type ``` pip3 install Flask ```
- create python file: ``` touch app.py ```
- create env.py file for sensitive data: ```touch env.py ```
- add 'env.py' and '__pycache__/' dir to .gitignore file list
- add confidential data to env.py:
    ``` import os```
    ``` []space[] ```
    ``` os.environ.setdefault("IP", "0.0.0.0")```
    ``` os.environ.setdefault("PORT", "5000")```
    ``` os.environ.setdefault("SECRET_KEY", "generated_password at https://randomkeygen.com/")```
    ``` os.environ.setdefault("MONGO_URI", "")```
    ``` os.environ.setdefault("MONGO_DBNAME", "database_name")```
- in app.py file:
    - create the imports-
    - check if env.py
    - create an instance of the flask app
        - create the default route:  ``` @app.route("/")
                                         def hello():
                                            return "Hello World ... again!"
                                    ```
    - add: ``` if __name__ == "__main__":   ```
             ```   app.run(host=os.environ.get("IP"),```
                 ```       port=int(os.environ.get("PORT")),```
                ```        debug=True) // True for development, False for production```
- type in terminal ``` python3/python app.py ``` -> this will run without any DB
- to kill all instances of a running app: ``` pkill -9 python3 ```

## **Deploy app to Heroku:**
- setup files that Heroku needs:
    ``` pip3 freeze --local > requirements.txt ```
    ``` echo web: python app.py > Procfile ```
- go to Heroku, create new App, connect the GitHub repository of this project to Heroku
- go to app's Settings -> Config Vars -> add all variable from the env.py local files (the env.py is not included in the repository)
- go to Deploy tabe and enable Automatic Deployments, after you've push the latest changes in this repo.
- Select DEPLOY branch
- If deploying is fayling with error ``` Push failed: cannot parse Procfile. ```, save the Procfile in UTF8 char file.
- SUCCESS!

## **Connect Flask to MongoDB Atlas:**
- install 3rd party lib:
    ``` pip3 install flask-pymongo ```
    ``` pip3 install dnspython ``` - to use mongodb srv connection string
- update req file: ``` pip3 freeze --local > requirements.txt ```
- add new imports to app.py:
    ``` from flask_pymongo import PyMongo```
    ```from bson.objectid import ObjectId```
- configure app:
    ```app.config["MONGO_DBNAME"] = os.environ.get("MONGO_DBNAME")```
    ```app.config["MONGO_URI"] = os.environ.get("MONGO_URI")```
    ```app.secret_key = os.environ.get("SECRET_KEY")```
- from MongoDB webpage get the Connect string for the application ```mongodb+srv://<username>:<password>@<cluster_name>.<something>.mongodb.net/?retryWrites=true&w=majority``` and paste this in the env.py file and in the Settings->Config Vars in Heroku Dashboared of the app and update ```MONGO_URI```.
- assure Flask is communicaing with MongoDB: 
    ```mongo = PyMongo(app)```
- create a new route:
    ```@app.route("/get_tasks")```
    ```def get_tasks():```
    ``` tasks = mongo.db.<collection_name>.find()```
    ``` return render_template("tasks.html", tasks=tasks)```
- create templates folder ```mkdir templates```
- make tasks.html file: ``` touch templates/tasks.html``` and populate with boilerplate html5

## **Create template inheritance base file**
- create base.html file in templates/ folder: ```touch templates/base.html```
- use the boiler plate html:5 to generate html basic code in base.html
- in base.html in the content area use jinja blocks:
    ``` {% block content %}``` - the 'content' string must match the one in the tasks.html, otherwise it won't know what to match for injection.
    ``` {% endblock %}``` to indicate where to inject the content from the templates.
- in the tasks.html file delete content and add jinja blocks:
        ```{% extends "base.html" %}``` - this indicates it used the base.html as template in which it will inject the folowwing content
        ```{% block content %}``` - this is the injected content
           ``` {% for task in tasks %}``` - something to do: it uses the tasks variable, send as parameter from the router function
               ``` {{ task.task_name }}<br>```
                ```{{ task.category_name }}<br>```
               ``` {{ task.task_description }}<br>```
               ``` {{ task.is_urgent }}<br>```
                ```{{ task.due_date }}<br>```
               ``` {{ task.completed }}<br>```
            ```{% endfor %}``` - closing the for loop
        ```{% endblock %}``` - end of injected content

## **Materialize & Static Files Setup**
- Within the base.html template add:
    - Materialize CDN for compiled and minified CSS at the top in header element.
    - Materialize CDN for compiled and minified JS at the bottom of body element.
- Create static/ folder and inside create css/ and js/ folders. In css/ folder create style.css file and inside js/ folder create script.js file.
- In the base.html file add the static ginger url_for method for the css ``` href="{{ url_for('static', filename = 'css/style.css') }} ``` and js ``` src="{{ url_for('static', filename = 'js/script.js') }}" ``` files.
- In base.html file add blocks for our own customs style ```{% block styles %} custom css here {% endblock %}``` and scripts ``` {% block scripts %}customs scripts here{% endblock %}```, which are applied/injected from one of our child templates, as needed.


## Gitpod Reminders

To run a frontend (HTML, CSS, Javascript only) application in Gitpod, in the terminal, type:

`python3 -m http.server`

A blue button should appear to click: _Make Public_,

Another blue button should appear to click: _Open Browser_.

To run a backend Python file, type `python3 app.py`, if your Python file is named `app.py` of course.

A blue button should appear to click: _Make Public_,

Another blue button should appear to click: _Open Browser_.

In Gitpod you have superuser security privileges by default. Therefore you do not need to use the `sudo` (superuser do) command in the bash terminal in any of the lessons.

To log into the Heroku toolbelt CLI:

1. Log in to your Heroku account and go to *Account Settings* in the menu under your avatar.
2. Scroll down to the *API Key* and click *Reveal*
3. Copy the key
4. In Gitpod, from the terminal, run `heroku_config`
5. Paste in your API key when asked

You can now use the `heroku` CLI program - try running `heroku apps` to confirm it works. This API key is unique and private to you so do not share it. If you accidentally make it public then you can create a new one with _Regenerate API Key_.

------

