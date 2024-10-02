![Rest API Basics LOGO](https://www.codingforentrepreneurs.com/_next/image?url=https%3A%2F%2Fimagedelivery.net%2F-inYktE1L-xCpqjE0wGFvg%2Fb717b957-f0d2-42ee-b858-699691a54700%2FcourseDetail&w=1200&q=75)


# DJANGO RESTFUL API


This is a basic, and rapid fire, guide on how to build a REST API with Django & Python. For much deeper depth and understanding, check out [Django RestFramework](https://www.django-rest-framework.org/).

### Software/Packages(Prerequisites)
- Django
- Python
- Django Rest Framework
- drf-spectacular

Create a folder to house your project, go to your terminal and write the following comands.


```r3
mkdir startupsouth9
cd startupsouth9

# open the directory with your code editor
```

### Project Setup
```python
#installing all our packages at ones using pip

pip install django djangorestframework drf-spectacular

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
    'drf-spectacular',  # Add drf-yasg for OpenAPI docs
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

#### Using ViewSet

```python
from rest_framework import viewsets, status
from rest_framework.response import Response
from .models import Article
from .serializers import ArticleSerializer

class ArticleViewSet(viewsets.ModelViewSet):
    """
    A ViewSet for viewing and editing articles.
    """
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer

    def create(self, request, *args, **kwargs):
        serializer = self.get_serializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def list(self, request, *args, **kwargs):
        queryset = self.get_queryset()
        serializer = self.get_serializer(queryset, many=True)
        return Response(serializer.data, status=status.HTTP_200_OK)
```

### Using ApiView

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from django.shortcuts import get_object_or_404
from .models import Article
from .serializers import ArticleSerializer

class ArticleList(APIView):
    """
    Handles GET and POST requests for listing articles and creating new ones.
    """

    def get(self, request):
        articles = Article.objects.all()
        serializer = ArticleSerializer(articles, many=True)
        return Response(serializer.data)

    def post(self, request):
        serializer = ArticleSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

class ArticleDetail(APIView):
    """
    Handles GET, PUT, DELETE requests for a single article instance.
    """

    def get(self, request, pk):
        article = get_object_or_404(Article, pk=pk)
        serializer = ArticleSerializer(article)
        return Response(serializer.data)

    def put(self, request, pk):
        article = get_object_or_404(Article, pk=pk)
        serializer = ArticleSerializer(article, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def delete(self, request, pk):
        article = get_object_or_404(Article, pk=pk)
        article.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)

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
### Include App urls in the Project

Configure Swagger and Redoc URLs in your main ```myapi/urls.py``` file:

```python
# myapi/urls.py

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('api.urls')),  # Include the API URLs
]

```

### Test the API Endpoints
Run the development server:
```
python manage.py runserver
```

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
  



## Configuration for OpenAPI Documentation with Django RestFramework:
Update your ```settings.py``` to include drf_spectacular in the INSTALLED_APPS and configure the settings for drf-spectacular.

```python
# Add DRF configuration to use Spectacular as the default schema generator
REST_FRAMEWORK = {
    'DEFAULT_SCHEMA_CLASS': 'drf_spectacular.openapi.AutoSchema',
}

SPECTACULAR_SETTINGS = {
    'TITLE': 'My API Documentation',
    'DESCRIPTION': 'API for managing products',
    'VERSION': '1.0.0',
    'SERVE_INCLUDE_SCHEMA': False,
}
```

In your main ```myapi/urls.py``` file, configure the schema view and the documentation URLs using drf-spectacular.

```python
# myapi/urls.py

from django.contrib import admin
from django.urls import path, include
from drf_spectacular.views import SpectacularAPIView, SpectacularRedocView, SpectacularSwaggerView


urlpatterns = [

    # OpenAPI schema
    path('api/schema/', SpectacularAPIView.as_view(), name='schema'),

    # Optional Swagger UI:
    path('api/schema/swagger-ui/', SpectacularSwaggerView.as_view(url_name='schema'), name='swagger-ui'),

    # Optional Redoc UI:
    path('api/schema/redoc/', SpectacularRedocView.as_view(url_name='schema'), name='redoc'),
]

```

### Test the API and Access Documentation
Run the development server:
```python
python manage.py runserver
```
- Swagger UI: Go to http://localhost:8000/api/schema/swagger-ui/ to access the Swagger documentation.
- Redoc UI: Go to http://localhost:8000/api/schema/redoc/ to access the Redoc documentation.
- OpenAPI JSON Schema: Visit http://localhost:8000/api/schema/ to see the raw OpenAPI schema in JSON format.

#
 <h1 style="text-align:center";>THANKS FOR ATTENDING THIS SESSION</h1>




