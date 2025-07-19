# 󰏢 MongoDB



_MongoDB & ODM (MongoEngine in Python)_

- compare with SQL

- **Database** = SQL Schema or Database
- **Collection** = SQL Table  
- **Document** = SQL Row
- **Field** = SQL Column

---

## Field 정의 등

기본적으로 `Document` 객체를 상속받아 field를 정의한다.

### Basic Field Types

```python
from mongoengine import Document, StringField, IntField, FloatField, BooleanField, DateTimeField, ListField, DictField

class User(Document):
    # Basic fields
    username = StringField(max_length=50, required=True, unique=True)
    age = IntField(min_value=0, max_value=120)
    height = FloatField()
    is_active = BooleanField(default=True)
    created_at = DateTimeField(auto_now_add=True)
    updated_at = DateTimeField(auto_now=True)
    
    # Collections
    tags = ListField(StringField(max_length=30))
    metadata = DictField()
```

### Field Constraints and Validation

```python
class Product(Document):
    name = StringField(
        required=True,           # Must be provided
        unique=True,            # Must be unique across collection
        min_length=3,           # Minimum length
        max_length=100,         # Maximum length
        regex=r'^[A-Za-z\s]+$'  # Must match regex
    )
    
    price = FloatField(
        min_value=0.01,         # Minimum value
        required=True
    )
    
    category = StringField(
        choices=['electronics', 'books', 'clothing']  # Limited choices
    )
    
    # Custom validation
    def clean(self):
        if self.price > 1000 and self.category == 'books':
            raise ValidationError('Books cannot cost more than $1000')
        if self.price > 2000 and self.category == 'clothing':
            raise ValidationError('Clothing cannot cost more than $2000')
```

- MongoEngine uses **method name conventions** to identify validation methods - it specifically looks for methods named `clean`.

- Other Special Method Names
  MongoEngine recognizes several special method names:
  ```python
  class Product(Document):
  name = StringField()

  def clean(self):  # Document-level validation
      pass

  def clean_<field_name>(self):  # Field-specific validation (Django-style)
      pass  # Note: This is less common in MongoEngine

  def save(self, *args, **kwargs):  # Override save behavior
      # Custom save logic
      super().save(*args, **kwargs)

  def delete(self, *args, **kwargs):  # Override delete behavior
      # Custom delete logic
      super().delete(*args, **kwargs)
  ```



## ETC

### Document Meta Options

```python
class BlogPost(Document):
    title = StringField(required=True)
    content = StringField()
    tags = ListField(StringField())
    published = BooleanField(default=False)
    
    meta = {
        'collection': 'blog_posts',          # Custom collection name
        'indexes': [
            'title',                         # Simple index
            ('title', 'published'),          # Compound index
            {'fields': ['title'], 'unique': True}  # Unique index
        ],
        'ordering': ['-created_at'],         # Default ordering
        'max_documents': 10000,              # Max documents in collection
        'max_size': 2000000                  # Max collection size in bytes
    }
```

**Purpose of Each Meta Option**

  - **`collection`**: Specifies custom collection name (`'blog_posts'`) instead of default (`blogpost`)
  - **`indexes`**: Creates database indexes for query performance:
    - Simple index on `title` field
    - Compound index on `title` + `published` fields
    - Unique index ensuring no duplicate titles
  - **`ordering`**: Sets default sort order (`-created_at` = newest first)
  - **`max_documents`**: Limits collection to 10,000 documents maximum
  - **`max_size`**: Caps collection size at ~2MB


### Relationships Between Documents

```python
from mongoengine import ReferenceField, EmbeddedDocument, EmbeddedDocumentField, ListField

# One-to-One and One-to-Many with ReferenceField
class Author(Document):
    name = StringField(required=True)
    email = StringField()

class Book(Document):
    title = StringField(required=True)
    author = ReferenceField(Author, required=True)  # Reference to Author document
    isbn = StringField()

# Embedded Documents (nested within parent)
class Address(EmbeddedDocument):
    street = StringField()
    city = StringField()
    country = StringField(default='US')

class User(Document):
    name = StringField()
    address = EmbeddedDocumentField(Address)  # Embedded single address
    addresses = ListField(EmbeddedDocumentField(Address))  # Multiple addresses
```

### Inheritance

```python
class Vehicle(Document):
    brand = StringField(required=True)
    model = StringField(required=True)
    year = IntField()
    
    meta = {'allow_inheritance': True}

class Car(Vehicle):
    doors = IntField()
    fuel_type = StringField(choices=['gasoline', 'diesel', 'electric'])

class Motorcycle(Vehicle):
    engine_size = IntField()  # in cc
```


### Advanced Field Types

```python
from mongoengine import URLField, EmailField, FileField, ImageField, ObjectIdField, UUIDField
from bson import ObjectId
import uuid

class AdvancedUser(Document):
    email = EmailField(required=True)
    website = URLField()
    profile_pic = ImageField()
    resume = FileField()
    
    # Custom ID fields
    external_id = UUIDField(default=uuid.uuid4)
    legacy_id = ObjectIdField()
    
    # JSON-like nested data
    preferences = DictField()
    scores = ListField(IntField())
```

### Custom Field Methods

```python
class User(Document):
    first_name = StringField()
    last_name = StringField()
    email = EmailField()
    
    # Custom properties
    @property
    def full_name(self):
        return f"{self.first_name} {self.last_name}"
    
    # Custom methods
    def get_posts(self):
        return Post.objects(author=self)
    
    def __str__(self):
        return self.full_name or self.email
```


**Key Characteristics**

  - **No storage**: `full_name` isn't stored in the database - it's computed each time
  - **Read-only by default**: You can't assign to it (`user.full_name = "New Name"` would error)
  - **Method disguised as attribute**: Called without parentheses, unlike regular methods



## Basic Operations

```python
# Create
user = User(username='john', email='john@example.com')
user.save()

# Query
users = User.objects(username='john')
all_users = User.objects.all()

# Update
User.objects(username='john').update(email='newemail@example.com')

# Delete
User.objects(username='john').delete()
```



## MongoEngine Project Structure Guide

### Directory Structure

```
app/
├── models/
│   ├── __init__.py          # DB connection + imports
│   ├── base.py              # Base model class
│   ├── user.py              # Individual models
│   └── product.py
```

### Database Setup (`models/__init__.py`)

```python
import mongoengine

def init_db(app):
    mongoengine.connect(
        db=app.config['MONGODB_DB'],
        host=app.config['MONGODB_HOST'],
        port=app.config['MONGODB_PORT']
    )

# Import all models
from .user import User
from .product import Product

__all__ = ['User', 'Product']
```

### Base Model (`models/base.py`)

```python
from mongoengine import Document, DateTimeField
from datetime import datetime

class BaseDocument(Document):
    created_at = DateTimeField(default=datetime.utcnow)
    updated_at = DateTimeField(default=datetime.utcnow)
    
    meta = {'abstract': True}
    
    def save(self, *args, **kwargs):
        self.updated_at = datetime.utcnow()
        return super().save(*args, **kwargs)
```

### Individual Models (`models/user.py`)

```python
from mongoengine import StringField, EmailField, BooleanField
from .base import BaseDocument

class User(BaseDocument):
    username = StringField(max_length=50, required=True, unique=True)
    email = EmailField(required=True, unique=True)
    is_active = BooleanField(default=True)
    
    meta = {
        'collection': 'users',
        'indexes': ['username', 'email']
    }
```

### Flask Integration (`app/__init__.py`)

```python
from flask import Flask
from app.models import init_db

def create_app():
    app = Flask(__name__)
    app.config['MONGODB_DB'] = 'your_db_name'
    
    init_db(app)
    return app
```

### Using in Controllers

```python
from app.models import User

# Create
user = User(username='john', email='john@email.com')
user.save()

# Query
users = User.objects.all()
user = User.objects(username='john').first()

# Update
User.objects(username='john').update(email='new@email.com')

# Delete
User.objects(username='john').delete()
```
