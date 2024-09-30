# startupsouth9_conf

![Rest API Basics LOGO](https://www.codingforentrepreneurs.com/_next/image?url=https%3A%2F%2Fimagedelivery.net%2F-inYktE1L-xCpqjE0wGFvg%2Fb717b957-f0d2-42ee-b858-699691a54700%2FcourseDetail&w=1200&q=75)


# DJANGO RESTFUL API


This is a basic, and rapid fire, guide on how to build a REST API with Django & Python. For much deeper depth and understanding, check out [Django RestFramework](https://www.django-rest-framework.org/).

### Software/Packages(Prerequisites)
- Django
- Python
- Django Rest Framework
- drf-yasg

Create a folder to house your project, go to your terminal and write the following comands.


```
mkdir startupsouth
cd startupsouth

# open the directory with your code editor
```

### Project Setup
```python
#installing all our packages at ones using pip

pip install django djangorestframework drf-yasg

```

### Create a Django Project

Start by creating a new Django project using the ```django-admin``` command.
```python
django-admin startproject myapis .
```

### Create a Django Application

Now, create a Django app within the project where you will define your API.

```python
python manage.py startapp api   #for windows user
python3 manage.py startapp api  #for linux/mac user
```

After creating the app, add it to the INSTALLED_APPS list in your settings.py file, along with rest_framework:

```python
# myapis/settings.py

INSTALLED_APPS = [
    ...
    'rest_framework',
    'drf-yasg',  # Add drf-yasg for OpenAPI docs
    'api',
]
```

### Create a simple model

Define a simple Product model in api/models.py:
```python
# api/models.py

from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=100)
    description = models.TextField()
    price = models.DecimalField(max_digits=10, decimal_places=2)

    def __str__(self):
        return self.name
```
Run migrations to create the Product table in the database:

```python
python manage.py makemigrations
python manage.py migrate
```

### Create Serializers

In Django REST Framework (DRF), a serializer is used to convert complex data types, such as Django model instances or querysets, into Python data types that can be easily rendered into JSON, XML, or other content types. It also allows for the reverse process: deserialization, where parsed data (e.g., JSON) is converted back into complex types, such as Django models, for validation and saving.

```python
# api/serializers.py

from rest_framework import serializers
from .models import Product

class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = ['id', 'name', 'description', 'price']
```

### Create API Views

Create views to handle listing, creating, retrieving, updating, and deleting products:
#### Using generic views
```python
# api/views.py

from rest_framework import generics
from .models import Product
from .serializers import ProductSerializer

# List all products or create a new one
class ProductListCreate(generics.ListCreateAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer

# Retrieve, update, or delete a specific product
class ProductDetail(generics.RetrieveUpdateDestroyAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer

```

### Add API URLs
Define API routes in ```api/urls.py```:
```python
# api/urls.py

from django.urls import path
from .views import ProductListCreate, ProductDetail

urlpatterns = [
    path('products/', ProductListCreate.as_view(), name='product-list'),
    path('products/<int:pk>/', ProductDetail.as_view(), name='product-detail'),
]
```
### Set Up Swagger and Redoc URLs for OpenAPI

Configure Swagger and Redoc URLs in your main ```myapi/urls.py``` file:

```python
# myapi/urls.py

from django.contrib import admin
from django.urls import path, include
from rest_framework import permissions
from drf_yasg.views import get_schema_view
from drf_yasg import openapi

schema_view = get_schema_view(
    openapi.Info(
        title="My API Documentation",
        default_version="v1",
        description="API for managing products",
        terms_of_service="https://www.google.com/policies/terms/",
        contact=openapi.Contact(email="contact@myapi.local"),
        license=openapi.License(name="BSD License"),
    ),
    public=True,
    permission_classes=(permissions.AllowAny,),
)

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('api.urls')),  # Include the API URLs

    # Swagger UI:
    path('swagger/', schema_view.with_ui('swagger', cache_timeout=0), name='schema-swagger-ui'),

    # Redoc UI:
    path('redoc/', schema_view.with_ui('redoc', cache_timeout=0), name='schema-redoc'),

    # OpenAPI schema in JSON format:
    path('openapi/', schema_view.without_ui(cache_timeout=0), name='schema-json'),
]

```

### Test the API and Swagger UI
Run the development server:
```
python manage.py runserver
```
### You can now access:
- Swagger UI at http://localhost:8000/swagger/
- Redoc UI at http://localhost:8000/redoc/
- OpenAPI JSON schema at http://localhost:8000/openapi/

# Testing the API Endpoints
1. Create a Product (POST request):
    - URL: http://localhost:8000/api/products/
    - Example body:
    ```json
    {
     "name": "Laptop",
     "description": "High-performance laptop",
     "price": "999.99"
    }
    ```

2. Get Product List (GET request):
    - URL: http://localhost:8000/api/products/
    - Returns a list of all products.

3. Retrieve a Product (GET request):
    - URL: http://localhost:8000/api/products/1/ (replace 1 with the product ID).

4. Update a Product (PUT or PATCH request):
    - URL: http://localhost:8000/api/products/1/
    - Example body for update:

    ```json
    {
     "name": "Updated Laptop",
     "description": "Updated description",
     "price": "899.99"
    }
    ```
5. Delete a Product (DELETE request):
    - URL: http://localhost:8000/api/products/1/

# 

#### You can now interact with your API and explore your documentation via the Swagger and Redoc interfaces!

#
 <h1 style="text-align:center";>THANKS FOR ATTENDING THIS SESSION</h1>




