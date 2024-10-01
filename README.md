Rest API Basics LOGO
DJANGO RESTFUL API
This is a basic, and rapid fire, guide on how to build a REST API with Django & Python. For much deeper depth and understanding, check out Django RestFramework.

Software/Packages(Prerequisites)
Django
Python
Django Rest Framework
drf-spectacular
Create a folder to house your project, go to your terminal and write the following comands.

mkdir startupsouth
cd startupsouth

# open the directory with your code editor
Project Setup
#installing all our packages at ones using pip

pip install django djangorestframework drf-spectacular

Create a Django Project
Start by creating a new Django project using the django-admin command.

django-admin startproject myapis .
Create a Django Application
Now, create a Django app within the project where you will define your API.

python manage.py startapp api   #for windows user
python3 manage.py startapp api  #for linux/mac user
After creating the app, add it to the INSTALLED_APPS list in your settings.py file, along with rest_framework:

# myapis/settings.py

INSTALLED_APPS = [
    ...
    'rest_framework',
    'drf-spectacular',  # Add drf-yasg for OpenAPI docs
    'api',
]
Create a simple model
Define a simple Product model in api/models.py:

# api/models.py

from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=100)
    description = models.TextField()
    price = models.DecimalField(max_digits=10, decimal_places=2)

    def __str__(self):
        return self.name
Run migrations to create the Product table in the database:

python manage.py makemigrations
python manage.py migrate
Create Serializers
In Django REST Framework (DRF), a serializer is used to convert complex data types, such as Django model instances or querysets, into Python data types that can be easily rendered into JSON, XML, or other content types. It also allows for the reverse process: deserialization, where parsed data (e.g., JSON) is converted back into complex types, such as Django models, for validation and saving.

# api/serializers.py

from rest_framework import serializers
from .models import Product

class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = ['id', 'name', 'description', 'price']
Create API Views
Create views to handle listing, creating, retrieving, updating, and deleting products:

Using generic views
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

Add API URLs
Define API routes in api/urls.py:

# api/urls.py

from django.urls import path
from .views import ProductListCreate, ProductDetail

urlpatterns = [
    path('products/', ProductListCreate.as_view(), name='product-list'),
    path('products/<int:pk>/', ProductDetail.as_view(), name='product-detail'),
]
Set Up Swagger and Redoc URLs for OpenAPI
Configure Swagger and Redoc URLs in your main myapi/urls.py file:

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

Test the API and Swagger UI
Run the development server:

python manage.py runserver
You can now access:
Swagger UI at http://localhost:8000/swagger/
Redoc UI at http://localhost:8000/redoc/
OpenAPI JSON schema at http://localhost:8000/openapi/
Testing the API Endpoints
Create a Product (POST request):

URL: http://localhost:8000/api/products/
Example body:
{
 "name": "Laptop",
 "description": "High-performance laptop",
 "price": "999.99"
}
Get Product List (GET request):

URL: http://localhost:8000/api/products/
Returns a list of all products.
Retrieve a Product (GET request):

URL: http://localhost:8000/api/products/1/ (replace 1 with the product ID).
Update a Product (PUT or PATCH request):

URL: http://localhost:8000/api/products/1/
Example body for update:
{
 "name": "Updated Laptop",
 "description": "Updated description",
 "price": "899.99"
}
Delete a Product (DELETE request):

URL: http://localhost:8000/api/products/1/
You can now interact with your API and explore your documentation via the Swagger and Redoc interfaces!
THANKS FOR ATTENDING THIS SESSION
