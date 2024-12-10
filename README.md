# Testing the 3 tiers application on the Window Desktop

The goal is to run the three tiers (ReactJS + Electron, Django, PostgreSQL) on the desktop for testing purposes. Once the infrastructure is ready, we plan to deploy them to a proper server.
Before the infra is ready, it has to consider the patching and maintenance for all the components.

The frontend application (ReactJS+Electron requires to open another software application in the desktop.
This documents would have 3 main topics :

(1) Steps to set up the desktop application <br>
(2) Steps to distribute to client and install the desktop applicaion in client's desktop <br>
(3) Maintenance Management before deploying to the servers <br>
(4) Strategies migrate to the servers (3 tiers)

## Steps to set up the desktop applications:

**Step 1: Prerequisites**
Before you begin, make sure you have:

1) Node.js and npm installed.
2) Basic understanding of Electron, React, and Django.
3) PostgreSQL installed locally (if needed).

**Step 2: Setting Up the Electron Project**
1) Create an Electron Project:
````
mkdir my-electron-app
cd my-electron-app
npm init -y
npm install electron --save-dev
````
**Step 3: Integrate React into the Electron Project**
1) Install React and React DOM:
````
npm install react@18.0.0 react-dom@18.0.0
````
2) Create the React entry point:
** Inside src/renderer, create an index.js:
````
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

ReactDOM.render(<App />, document.getElementById('root'));
````
3) Create a basic React app component App.js:
** Inside src/renderer, create App.js:
````
import React, { useEffect, useState } from 'react';
import axios from 'axios';

const App = () => {
    const [items, setItems] = useState([]);

    useEffect(() => {
        // Fetch data from Django API
        axios.get('http://localhost:8000/api/items/')
            .then(response => setItems(response.data))
            .catch(error => console.error(error));
    }, []);

    return (
        <div>
            <h1>Items from Django API</h1>
            <ul>
                {items.map(item => (
                    <li key={item.id}>{item.name}</li>
                ))}
            </ul>
        </div>
    );
};

export default App;
````
** Step 4 Setup Django Server **
1) Create a Virtual Environment:
````
python -m venv venv
venv\Scripts\activate
````
2) Install Django:
````
pip install django
````
3) Create a New Django Project:
````
django-admin startproject my_django_server
cd my_django_server
````
4) Configure PostgreSQL in Django:
Open settings.py and add the PostgreSQL configuration:
````
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'your_database_name',
        'USER': 'your_database_user',
        'PASSWORD': 'your_database_password',
        'HOST': 'localhost',
        'PORT': '5432',
    }
}
````
5) Create a Django app:
````
python manage.py startapp myapp
````
6) Define the Model in myapp/models.py:
````
from django.db import models

class Item(models.Model):
    name = models.CharField(max_length=200)
    description = models.TextField(blank=True)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.name
````
7) Register the Model in myapp/admin.py (Optional for admin interface):
````
from django.contrib import admin
from .models import Item

admin.site.register(Item)
````
8) Add the App to Installed Apps: Open my_django_server/settings.py and add "myapp" to INSTALLED_APPS:
````
INSTALLED_APPS = [
    # Other apps
    'myapp',
]
````
9) Run Migrations:
Generate migration files:
````
python manage.py makemigrations
````
Apply migrations:
````
python manage.py migrate
````
10) Seed Data (Optional): Create a fixture file myapp/fixtures/initial_data.json to populate the database:
````
[
    {
        "model": "myapp.item",
        "pk": 1,
        "fields": {
            "name": "Sample Item 1",
            "description": "This is a sample item.",
            "created_at": "2024-01-01T00:00:00Z"
        }
    }
]
````
Load the fixture:
````
python manage.py loaddata initial_data
````
11) Define a Simple API Endpoint in myapp/views.py
````
from django.http import JsonResponse
from .models import Item

def items(request):
    items = Item.objects.values()
    return JsonResponse(list(items), safe=False)
````
12) Configure the API Endpoint in my_django_server/urls.py:
````
from django.urls import path
from myapp import views

urlpatterns = [
    path('api/items/', views.items),
]
````
13) Run the Development Server: Start the Django server to test the setup:
````
python manage.py runserver
````
14) Verify the API Endpoint: Open your browser and navigate to http://localhost:8000/api/items/. It should return a JSON response with the seeded items.

## For Distribution
