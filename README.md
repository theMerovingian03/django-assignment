# Django Assignment

#### Topic Django Signals:

### Question 1

By default are django signals executed synchronously or asynchronously?

### Answer 1

By default, django signals are executed synchronously. Following code snippet demonstrates this behavior:
suppose we have a model in our `models.py` file:

```
from django.db import models
from django.db.models.signals import post_save
from django.dispatch import receiver

class Book(models.Model):
  title = models.CharField(max_length=100)

# Defining signal handler

@receiver(post_save, sender=Book)
def signal_book_created(sender, instance, **kwargs):
  print(f"Book creates: {instance.title}")
```

in our `views.py` file:

```
from django.http import HttpResponse
from .models import Book
def create_book(request):
  Book.objects.create(title="South")
  return HttpResponse("Book created")
```

Here, the handler signal_book_receiver runs after the instance is saved. The message in handler is printed before the HttpResponse is returned.

### Question 2

Do django signals run in the same thread as the caller?

### Answer 2

Yes, Django signals run in the same thread as the caller. Following code snippet demonstrates this behavior:
Suppose we have the same Book model we print the thread of the signal handler:

```
from django.db import models
from django.db.models.signals import post_save
from django.dispatch import receiver
import threading

class Book(models.Model):
  title = models.CharField(max_length=100)

# Defining signal handler

@receiver(post_save, sender=Book)
def signal_book_created(sender, instance, **kwargs):
  current_thread = threading.current_thread()
  print(f"Signal handler running in threaf: {current_thread.name}")
```

In our `views.py` we print the thread of our view(caller):

```
from django.http import HttpResponse
from .models import Book
import threading

def create_book(request):
  current_thread = threading.current_thread()
  print(f"Request received in thread: {current_thread.name}")
  Book.objects.create(title="South")
  return HttpResponse("Book created")
```

Here the same output will be printed for thread_name in both the signal handler and the view.

### Question 3:

Yes, Django signals run in the same database transaction as the caller by default. Let us consider our `Book` model:

```
from django.db import models, transaction
from django.db.models.signals import post_save
from django.dispatch import receiver

class Book(models.Model):
  title = models.CharField(max_length=100)

# Defining signal handler

@receiver(post_save, sender=Book)
def signal_book_created(sender, instance, **kwargs):
    atomic_block = transaction.get_connection().in_atomic_block
    print(f"Signal handler Transaction atomic block? {atomic_block}")
```

Now, in our `views.py` file, we will try to create the Book instace within the transaction

```
from django.http import HttpResponse
from .models import Book
from django.db import transaction

def create_book(request):
    with transaction.atomic():
        Book.objects.create(name="South")
        return HttpResponse()
```

Here, output will be printed as:

```
Signal handler Transaction atomic block? True
```

Hence, we know that the signals run in the same database transaction as the caller.

<hr>
<br>
#### Topic: Custom Classes in Python

Following code shows the implementation of the `Rectangle` class:

```
class Rectangle:
    def __init__(self, length: int, width: int):
        self.length = length
        self.width = width

    def __iter__(self):
        yield {'length':self.length}
        yield {'width': self.width}
```

Creating an object:

```
rectangle = Rectangle(5,10)

for item in rectangle:
    print(item)
```

Following output will be shown:

```
{'length': 5}
{'width': 10}
```
