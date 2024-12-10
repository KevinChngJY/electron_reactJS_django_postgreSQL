# Package 3 tiers Application into Single Desktop Application

Packaging a 3-tier application (ReactJS for frontend, Django for backend, and PostgreSQL for the database) into a single desktop application using Electron is often driven by the following reasons:

**1. Offline Accessibility**
** Users can run the application entirely on their local machine without requiring a constant internet connection. This is beneficial in environments where connectivity is limited or unreliable.
** A self-contained Electron app can bundle everything required (frontend, backend, and database), making it fully functional offline.

**2. Ease of Distribution**
** Desktop applications can be distributed as standalone executables or installers, simplifying deployment. Users don't need to install or configure separate components like a web server or database.
** Electron apps are cross-platform, allowing developers to target Windows, macOS, and Linux from a single codebase.

**3. Unified User Experience**
** Electron provides a native desktop-like experience while retaining the flexibility of web technologies (ReactJS for the frontend).
** Users interact with a familiar desktop environment without having to open a browser or manage multiple windows.

**4. Simplified Setup**
** End-users don't need to install a web server (like Apache or Nginx) or set up the database separately. 
** The Electron package can encapsulate the entire stack, reducing setup complexity.

**5. Centralized Updates**
** Electron apps can implement auto-updating mechanisms, allowing developers to push updates to the entire stack (frontend, backend, and database logic) seamlessly.

**6. Security**
** Running the application locally can reduce some security risks associated with hosting data in the cloud (though local systems come with their own security considerations).
** Sensitive data never leaves the local machine unless explicitly shared.

**7. Cost-Effectiveness**
** Hosting a cloud-based 3-tier application incurs ongoing costs (server hosting, maintenance, etc.). A local Electron-packaged app avoids these, making it a cost-effective solution for small-scale or single-user deployments.

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
1) Create a new Django project:
````
django-admin startproject my_django_server
cd my_django_server
````
2) Configure PostgreSQL in Django:
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
3) Create a Django app:
````
python manage.py startapp myapp
````
4) Define a simple API endpoint in myapp/views.py:
````
from django.http import JsonResponse
from .models import YourModel

def items(request):
    items = YourModel.objects.values()
    return JsonResponse(list(items), safe=False)
````
5) Configure the API endpoint in my_django_server/urls.py
````
from django.urls import path
from myapp import views

urlpatterns = [
    path('api/items/', views.items),
]
````
