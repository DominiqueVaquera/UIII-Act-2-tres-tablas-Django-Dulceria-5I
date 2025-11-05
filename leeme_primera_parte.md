Primera Parte: Proyecto Dulcería en Django
A continuación, te proporciono los procedimientos detallados para crear y configurar el proyecto "Dulcería" en Django, paso por paso. Vamos a recorrer el proceso desde la creación de las carpetas hasta la implementación de las vistas y configuraciones necesarias para el funcionamiento del proyecto.

# ==========================================
# 📁 Estructura Final del Proyecto
# ==========================================

```plaintext
UIII_Dulceria_0726/
 ├── .venv/
 ├── backend_Dulceria/
 │   │   ├── __init__.py
 │   │   ├── settings.py
 │   │   ├── urls.py
 │   │   └── wsgi.py
 ├── app_Dulceria/
 │   ├── templates/
 │   │   ├── base.html
 │   │   ├── navbar.html
 │   │   ├── footer.html
 │   │   ├── inicio.html
 │   │   └── dulce/
 |   |       ├── agregar_dulce.html 
 │   |       ├── ver_dulces.html 
 │   |       ├── actualizar_dulce.html 
 │   |       └── borrar_dulce.html 
 │   ├── models.py
 │   ├── views.py
 │   ├── urls.py
 │   ├── tests.py
 │   ├── admin.py
 │   └── __init__.py
 ├── manage.py
 └── .venv/

```
# ==========================================
# 🧁 Proyecto Django: Dulcería
# ==========================================


Lenguaje: Python
Framework: Django
Editor: Visual Studio Code
Puerto: 0726

# 📁 1. Crear carpeta del proyecto
mkdir UIII_Dulceria_0726
cd UIII_Dulceria_0726

# 💻 2. Abrir VS Code sobre la carpeta code .

# 🧭 3. Abrir terminal en VS Code

Desde la barra superior:

Ver → Terminal

# 🧩 4. Crear entorno virtual “.venv”
python -m venv .venv

# ⚙️ 5. Activar el entorno virtual
En Windows:
.venv\Scripts\activate

En macOS/Linux:
source .venv/bin/activate

# 🐍 6. Activar intérprete de Python

En VS Code:
Presiona Ctrl + Shift + P → Escribe “Python: Select Interpreter” → Selecciona el que diga .venv.

# 📦 7. Instalar Django
pip install django

# 🚀 8. Crear proyecto backend_Dulceria sin duplicar carpeta
django-admin startproject backend_Dulceria .


(El punto evita que se cree una carpeta duplicada).

# 🌐 9. Ejecutar servidor en el puerto 0726
python manage.py runserver 0726

# 🔗 10. Copiar y pegar el link en el navegador

Abre en tu navegador:

http://127.0.0.1:0726/

# 🧱 11. Crear aplicación app_Dulceria

python manage.py startapp app_Dulceria

# ==========================================
# 🧬 12. Código del archivo models.py (app_Dulceria/models.py)
# ==========================================

from django.db import models

# ==========================================
# MODELO: DULCES
# ==========================================
```plaintext
class Dulce(models.Model):
    nombre = models.CharField(max_length=150, help_text="Nombre del dulce")
    descripcion = models.TextField(blank=True, null=True, help_text="Descripción detallada del dulce")
    precio = models.DecimalField(max_digits=10, decimal_places=2, help_text="Precio del dulce")
    stock = models.PositiveIntegerField(help_text="Cantidad de unidades en stock")
    categoria = models.CharField(max_length=50, help_text="Categoría del dulce (ej. Chocolates, Gomas, Caramelos)")
    fecha_disponibilidad = models.DateField(help_text="Fecha a partir de la cual el dulce está disponible")

    def __str__(self):
        return self.nombre
```

# ==========================================
# MODELO: CLIENTES
# ==========================================
```plaintext
class Cliente(models.Model):
    nombre = models.CharField(max_length=100, unique=True)
    apellido = models.CharField(max_length=100, blank=True, null=True) 
    email = models.EmailField(max_length=254, unique=True) 
    telefono = models.CharField(max_length=20, blank=True, null=True)
    direccion = models.CharField(max_length=255, blank=True, null=True)
    fecha_registro = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f"{self.nombre} {self.apellido}"
```

# ==========================================
# MODELO: PEDIDOS
# ==========================================
```plaintext
class Pedido(models.Model):
    cliente_id = models.ForeignKey(Cliente, on_delete=models.CASCADE, related_name='pedidos')
    direccion_envio = models.CharField(max_length=255)
    TIPO_PEDIDO_CHOICES = [
        ('estandar', 'Estándar'),
        ('urgente', 'Urgente'),
        ('personalizado', 'Personalizado'),
    ]
    tipo_pedido = models.CharField(max_length=20, choices=TIPO_PEDIDO_CHOICES, default='estandar')
    fecha_pedido = models.DateTimeField(auto_now_add=True)
    ESTADO_CHOICES = [
        ('pendiente', 'Pendiente'),
        ('procesando', 'Procesando'),
        ('enviado', 'Enviado'),
        ('entregado', 'Entregado'),
        ('cancelado', 'Cancelado'),
    ]
    estado = models.CharField(max_length=20, choices=ESTADO_CHOICES, default='pendiente')

    METODO_PAGO_CHOICES = [
        ('tarjeta_credito', 'Tarjeta de Crédito'),
        ('paypal', 'PayPal'),
        ('transferencia', 'Transferencia Bancaria'),
        ('efectivo', 'Efectivo al Recibir'),
    ]
    metodo_pago = models.CharField(max_length=50, choices=METODO_PAGO_CHOICES)
    id_dulce = models.ManyToManyField(Dulce, related_name="pedidos")

    def __str__(self):
        return f"Pedido #{self.pk} de {self.cliente_id.nombre} - Estado: {self.estado}"
```
# ⚙️ 12.5 Realizar migraciones
python manage.py makemigrations
python manage.py migrate

# 🍬 13. Trabajaremos primero con el modelo Dulce
# 👁️‍🗨️ 14. Código de views.py (CRUD de Dulces)
```plaintext
from django.shortcuts import render, redirect, get_object_or_404
from .models import Dulce, Cliente, Pedido

def inicio_dulceria(request):
    return render(request, 'app_Dulceria/inicio.html')

# VISTAS PARA DULCES
def agregar_dulce(request):
    if request.method == 'POST':
        nombre = request.POST.get('nombre')
        descripcion = request.POST.get('descripcion')
        precio = request.POST.get('precio')
        stock = request.POST.get('stock')
        categoria = request.POST.get('categoria')
        fecha_disponibilidad = request.POST.get('fecha_disponibilidad')
        
        Dulce.objects.create(
            nombre=nombre,
            descripcion=descripcion,
            precio=precio,
            stock=stock,
            categoria=categoria,
            fecha_disponibilidad=fecha_disponibilidad
        )
        return redirect('ver_dulces')
    
    return render(request, 'app_Dulceria/dulce/agregar_dulce.html')

def ver_dulces(request):
    dulces = Dulce.objects.all()
    return render(request, 'app_Dulceria/dulce/ver_dulces.html', {'dulces': dulces})

def actualizar_dulce(request, id):
    dulce = get_object_or_404(Dulce, id=id)
    return render(request, 'app_Dulceria/dulce/actualizar_dulce.html', {'dulce': dulce})

def realizar_actualizacion_dulce(request, id):
    if request.method == 'POST':
        dulce = get_object_or_404(Dulce, id=id)
        dulce.nombre = request.POST.get('nombre')
        dulce.descripcion = request.POST.get('descripcion')
        dulce.precio = request.POST.get('precio')
        dulce.stock = request.POST.get('stock')
        dulce.categoria = request.POST.get('categoria')
        dulce.fecha_disponibilidad = request.POST.get('fecha_disponibilidad')
        dulce.save()
        return redirect('ver_dulces')
    
    return redirect('ver_dulces')

def borrar_dulce(request, id):
    dulce = get_object_or_404(Dulce, id=id)
    return render(request, 'app_Dulceria/dulce/borrar_dulce.html', {'dulce': dulce})

def realizar_borrado_dulce(request, id):
    if request.method == 'POST':
        dulce = get_object_or_404(Dulce, id=id)
        dulce.delete()
        return redirect('ver_dulces')
    
    return redirect('ver_dulces')
```
# 🧾 15. Crear carpeta templates
```plaintext
app_Dulceria/
 └── templates/
```
# 🧱 16. Archivos dentro de templates
```plaintext
templates/
 ├── base.html
 ├── header.html
 ├── navbar.html
 ├── footer.html
 └── inicio.html
```
# 🎨 17. base.html (Bootstrap)
```plaintext
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema de Administración Dulcería</title>
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body {
            background-color: #f8f9fa;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        .navbar {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        }
        .sidebar {
            background-color: #2c3e50;
            min-height: 100vh;
        }
        .sidebar .nav-link {
            color: #ecf0f1;
        }
        .sidebar .nav-link:hover {
            color: #3498db;
        }
        .main-content {
            background-color: #ffffff;
            border-radius: 10px;
            box-shadow: 0 0 20px rgba(0,0,0,0.1);
            padding: 30px;
            margin: 20px 0;
        }
        .btn-primary {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            border: none;
        }
        .table-hover tbody tr:hover {
            background-color: rgba(102, 126, 234, 0.1);
        }
    </style>
</head>
<body>
    {% include 'app_Dulceria/header.html' %}
    {% include 'app_Dulceria/navbar.html' %}
    
    <div class="container-fluid">
        <div class="row">
            <main class="col-md-12">
                <div class="main-content">
                    {% block content %}
                    {% endblock %}
                </div>
            </main>
        </div>
    </div>
    
    {% include 'app_Dulceria/footer.html' %}

    <!-- Bootstrap JS -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```
# 🧭 18. navbar.html
```plaintext
<nav class="navbar navbar-expand-lg navbar-dark">
    <div class="container-fluid">
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNav">
            <ul class="navbar-nav me-auto">
                <li class="nav-item">
                    <a class="nav-link" href="{% url 'inicio_dulceria' %}">
                        <i class="bi bi-house"></i> Inicio
                    </a>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">
                        <i class="bi bi-candy"></i> Dulces
                    </a>
                    <ul class="dropdown-menu">
                        <li><a class="dropdown-item" href="{% url 'agregar_dulce' %}">Agregar Dulce</a></li>
                        <li><a class="dropdown-item" href="{% url 'ver_dulces' %}">Ver Dulces</a></li>
                        <li><hr class="dropdown-divider"></li>
                        <li><a class="dropdown-item" href="#">Actualizar Dulce</a></li>
                        <li><a class="dropdown-item" href="#">Borrar Dulce</a></li>
                    </ul>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">
                        <i class="bi bi-people"></i> Clientes
                    </a>
                    <ul class="dropdown-menu">
                        <li><a class="dropdown-item" href="#">Agregar Clientes</a></li>
                        <li><a class="dropdown-item" href="#">Ver Clientes</a></li>
                        <li><hr class="dropdown-divider"></li>
                        <li><a class="dropdown-item" href="#">Actualizar Clientes</a></li>
                        <li><a class="dropdown-item" href="#">Borrar Clientes</a></li>
                    </ul>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">
                        <i class="bi bi-cart"></i> Pedidos
                    </a>
                    <ul class="dropdown-menu">
                        <li><a class="dropdown-item" href="#">Agregar Pedidos</a></li>
                        <li><a class="dropdown-item" href="#">Ver Pedidos</a></li>
                        <li><hr class="dropdown-divider"></li>
                        <li><a class="dropdown-item" href="#">Actualizar Pedidos</a></li>
                        <li><a class="dropdown-item" href="#">Borrar Pedidos</a></li>
                    </ul>
                </li>
            </ul>
        </div>
    </div>
</nav>
```
# 🦶 19. footer.html
```plaintext
<footer class="bg-dark text-white text-center py-3 fixed-bottom">
    © <span id="year"></span> | Creado por Dominique Vaquera, CBTIS 128
    <script>document.getElementById("year").innerHTML = new Date().getFullYear();</script>
</footer>
```
# 🏠 20. inicio.html
```plaintext
{% extends 'app_Dulceria/base.html' %}

{% block content %}
<div class="text-center">
    <h1 class="display-4 text-primary mb-4">Bienvenido al Sistema de Administración Dulcería</h1>
    <p class="lead mb-5">Sistema diseñado para la gestión eficiente de productos, clientes y pedidos de dulcería.</p>
    
    <div class="row">
        <div class="col-md-8 mx-auto">
            <img src="https://cdn.pixabay.com/photo/2017/08/06/22/16/candy-2599045_1280.jpg" 
                 alt="Dulcería" 
                 class="img-fluid rounded shadow-lg mb-4"
                 style="max-height: 400px; object-fit: cover;">
        </div>
    </div>
    
    <div class="row mt-5">
        <div class="col-md-4">
            <div class="card border-0 shadow-sm">
                <div class="card-body">
                    <h5 class="card-title text-primary">
                        <i class="bi bi-candy fs-1"></i>
                    </h5>
                    <h6 class="card-subtitle mb-2">Gestión de Dulces</h6>
                    <p class="card-text">Administra el inventario de productos dulces.</p>
                </div>
            </div>
        </div>
        <div class="col-md-4">
            <div class="card border-0 shadow-sm">
                <div class="card-body">
                    <h5 class="card-title text-primary">
                        <i class="bi bi-people fs-1"></i>
                    </h5>
                    <h6 class="card-subtitle mb-2">Gestión de Clientes</h6>
                    <p class="card-text">Administra la información de los clientes.</p>
                </div>
            </div>
        </div>
        <div class="col-md-4">
            <div class="card border-0 shadow-sm">
                <div class="card-body">
                    <h5 class="card-title text-primary">
                        <i class="bi bi-cart fs-1"></i>
                    </h5>
                    <h6 class="card-subtitle mb-2">Gestión de Pedidos</h6>
                    <p class="card-text">Controla los pedidos y entregas.</p>
                </div>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```
# 📂 21. Subcarpeta dulce
app_Dulceria/templates/dulce/

# 📄 22. Archivos HTML CRUD Dulce

Ejemplo: ver_dulces.html
```plaintext
{% extends 'base.html' %}
{% block contenido %}
<h2 class="text-center mb-4">Lista de Dulces</h2>
<table class="table table-striped table-hover">
    <thead class="table-primary">
        <tr>
            <th>Nombre</th><th>Precio</th><th>Stock</th><th>Categoría</th><th>Acciones</th>
        </tr>
    </thead>
    <tbody>
    {% for d in dulces %}
        <tr>
            <td>{{ d.nombre }}</td><td>{{ d.precio }}</td><td>{{ d.stock }}</td><td>{{ d.categoria }}</td>
            <td>
                <a href="{% url 'actualizar_dulce' d.id %}" class="btn btn-warning btn-sm">Editar</a>
                <a href="{% url 'borrar_dulce' d.id %}" class="btn btn-danger btn-sm">Borrar</a>
            </td>
        </tr>
    {% endfor %}
    </tbody>
</table>
{% endblock %}
```

(Los demás archivos —agregar, actualizar, borrar— siguen formato simple con formularios HTML sin forms.py).

# 🌐 24. Crear urls.py en app_Dulceria
```plaintext
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_dulceria, name='inicio_dulceria'),
    path('agregar_dulce/', views.agregar_dulce, name='agregar_dulce'),
    path('ver_dulces/', views.ver_dulces, name='ver_dulces'),
    path('actualizar_dulce/<int:id>/', views.actualizar_dulce, name='actualizar_dulce'),
    path('realizar_actualizacion_dulce/<int:id>/', views.realizar_actualizacion_dulce, name='realizar_actualizacion_dulce'),
    path('borrar_dulce/<int:id>/', views.borrar_dulce, name='borrar_dulce'),
]
```
# ⚙️ 25. Agregar app_Dulceria en settings.py
```plaintext
INSTALLED_APPS = [
    ...
    'app_Dulceria',
]
```
# 🧭 26. Configurar urls.py de app_dulce

Archivo: backend_Dulceria/urls.py
```plaintext
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_dulceria, name='inicio_dulceria'),
    
    # URLs para Dulces
    path('dulces/agregar/', views.agregar_dulce, name='agregar_dulce'),
    path('dulces/ver/', views.ver_dulces, name='ver_dulces'),
    path('dulces/actualizar/<int:id>/', views.actualizar_dulce, name='actualizar_dulce'),
    path('dulces/realizar_actualizacion/<int:id>/', views.realizar_actualizacion_dulce, name='realizar_actualizacion_dulce'),
    path('dulces/borrar/<int:id>/', views.borrar_dulce, name='borrar_dulce'),
    path('dulces/realizar_borrado/<int:id>/', views.realizar_borrado_dulce, name='realizar_borrado_dulce'),
]
```
# 🛠️ 27. Registrar modelos en admin.py
```plaintext
from django.contrib import admin
from .models import Dulce, Cliente, Pedido

@admin.register(Dulce)
class DulceAdmin(admin.ModelAdmin):
    list_display = ('nombre', 'precio', 'stock', 'categoria', 'fecha_disponibilidad')
    list_filter = ('categoria',)
    search_fields = ('nombre', 'descripcion')
```
# 28. agregar_dulce.html
```plaintext
{% extends 'app_Dulceria/base.html' %}

{% block content %}
<div class="container">
    <h2 class="text-primary mb-4">Agregar Nuevo Dulce</h2>
    
    <form method="POST" class="needs-validation" novalidate>
        {% csrf_token %}
        <div class="row">
            <div class="col-md-6 mb-3">
                <label for="nombre" class="form-label">Nombre del Dulce</label>
                <input type="text" class="form-control" id="nombre" name="nombre" required>
            </div>
            <div class="col-md-6 mb-3">
                <label for="precio" class="form-label">Precio</label>
                <input type="number" step="0.01" class="form-control" id="precio" name="precio" required>
            </div>
        </div>
        
        <div class="mb-3">
            <label for="descripcion" class="form-label">Descripción</label>
            <textarea class="form-control" id="descripcion" name="descripcion" rows="3"></textarea>
        </div>
        
        <div class="row">
            <div class="col-md-4 mb-3">
                <label for="stock" class="form-label">Stock</label>
                <input type="number" class="form-control" id="stock" name="stock" required>
            </div>
            <div class="col-md-4 mb-3">
                <label for="categoria" class="form-label">Categoría</label>
                <input type="text" class="form-control" id="categoria" name="categoria" required>
            </div>
            <div class="col-md-4 mb-3">
                <label for="fecha_disponibilidad" class="form-label">Fecha de Disponibilidad</label>
                <input type="date" class="form-control" id="fecha_disponibilidad" name="fecha_disponibilidad" required>
            </div>
        </div>
        
        <div class="d-grid gap-2 d-md-flex justify-content-md-end">
            <a href="{% url 'ver_dulces' %}" class="btn btn-secondary me-md-2">Cancelar</a>
            <button type="submit" class="btn btn-primary">Guardar Dulce</button>
        </div>
    </form>
</div>
{% endblock %}
```
# 29. ver_dulce.html
```plaintext
{% extends 'app_Dulceria/base.html' %}

{% block content %}
<div class="container">
    <div class="d-flex justify-content-between align-items-center mb-4">
        <h2 class="text-primary">Lista de Dulces</h2>
        <a href="{% url 'agregar_dulce' %}" class="btn btn-primary">
            <i class="bi bi-plus-circle"></i> Agregar Nuevo Dulce
        </a>
    </div>
    
    <div class="table-responsive">
        <table class="table table-striped table-hover">
            <thead class="table-dark">
                <tr>
                    <th>Nombre</th>
                    <th>Descripción</th>
                    <th>Precio</th>
                    <th>Stock</th>
                    <th>Categoría</th>
                    <th>Fecha Disponibilidad</th>
                    <th>Acciones</th>
                </tr>
            </thead>
            <tbody>
                {% for dulce in dulces %}
                <tr>
                    <td>{{ dulce.nombre }}</td>
                    <td>{{ dulce.descripcion|default:"-" }}</td>
                    <td>${{ dulce.precio }}</td>
                    <td>{{ dulce.stock }}</td>
                    <td>{{ dulce.categoria }}</td>
                    <td>{{ dulce.fecha_disponibilidad }}</td>
                    <td>
                        <div class="btn-group" role="group">
                            <a href="#" class="btn btn-sm btn-info">Ver</a>
                            <a href="{% url 'actualizar_dulce' dulce.id %}" class="btn btn-sm btn-warning">Editar</a>
                            <a href="{% url 'borrar_dulce' dulce.id %}" class="btn btn-sm btn-danger">Borrar</a>
                        </div>
                    </td>
                </tr>
                {% empty %}
                <tr>
                    <td colspan="7" class="text-center">No hay dulces registrados</td>
                </tr>
                {% endfor %}
            </tbody>
        </table>
    </div>
</div>
{% endblock %}
```
# 30. actualizar_dulce.html
```plaintext
{% extends 'app_Dulceria/base.html' %}

{% block content %}
<div class="container">
    <h2 class="text-primary mb-4">Actualizar Dulce</h2>
    
    <form method="POST" action="{% url 'realizar_actualizacion_dulce' dulce.id %}" class="needs-validation" novalidate>
        {% csrf_token %}
        <div class="row">
            <div class="col-md-6 mb-3">
                <label for="nombre" class="form-label">Nombre del Dulce</label>
                <input type="text" class="form-control" id="nombre" name="nombre" value="{{ dulce.nombre }}" required>
            </div>
            <div class="col-md-6 mb-3">
                <label for="precio" class="form-label">Precio</label>
                <input type="number" step="0.01" class="form-control" id="precio" name="precio" value="{{ dulce.precio }}" required>
            </div>
        </div>
        
        <div class="mb-3">
            <label for="descripcion" class="form-label">Descripción</label>
            <textarea class="form-control" id="descripcion" name="descripcion" rows="3">{{ dulce.descripcion }}</textarea>
        </div>
        
        <div class="row">
            <div class="col-md-4 mb-3">
                <label for="stock" class="form-label">Stock</label>
                <input type="number" class="form-control" id="stock" name="stock" value="{{ dulce.stock }}" required>
            </div>
            <div class="col-md-4 mb-3">
                <label for="categoria" class="form-label">Categoría</label>
                <input type="text" class="form-control" id="categoria" name="categoria" value="{{ dulce.categoria }}" required>
            </div>
            <div class="col-md-4 mb-3">
                <label for="fecha_disponibilidad" class="form-label">Fecha de Disponibilidad</label>
                <input type="date" class="form-control" id="fecha_disponibilidad" name="fecha_disponibilidad" value="{{ dulce.fecha_disponibilidad|date:'Y-m-d' }}" required>
            </div>
        </div>
        
        <div class="d-grid gap-2 d-md-flex justify-content-md-end">
            <a href="{% url 'ver_dulces' %}" class="btn btn-secondary me-md-2">Cancelar</a>
            <button type="submit" class="btn btn-primary">Actualizar Dulce</button>
        </div>
    </form>
</div>
{% endblock %}
```
# 31. borrar_dulce.html
```plaintext
{% extends 'app_Dulceria/base.html' %}

{% block content %}
<div class="container">
    <div class="row justify-content-center">
        <div class="col-md-6">
            <div class="card border-danger">
                <div class="card-header bg-danger text-white">
                    <h4 class="mb-0">Confirmar Eliminación</h4>
                </div>
                <div class="card-body">
                    <h5 class="card-title">¿Estás seguro de que deseas eliminar este dulce?</h5>
                    <p class="card-text">
                        <strong>Nombre:</strong> {{ dulce.nombre }}<br>
                        <strong>Precio:</strong> ${{ dulce.precio }}<br>
                        <strong>Categoría:</strong> {{ dulce.categoria }}
                    </p>
                    <p class="text-danger">
                        <strong>¡Esta acción no se puede deshacer!</strong>
                    </p>
                    
                    <form method="POST" action="{% url 'realizar_borrado_dulce' dulce.id %}">
                        {% csrf_token %}
                        <div class="d-grid gap-2 d-md-flex justify-content-md-end">
                            <a href="{% url 'ver_dulces' %}" class="btn btn-secondary me-md-2">Cancelar</a>
                            <button type="submit" class="btn btn-danger">Eliminar Dulce</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```
# 32. urls.py (backend_Dulceria)
```plaintext
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Dulceria.urls')),
]
```
Volver a migrar:

python manage.py makemigrations
python manage.py migrate

# 🌈 33. Estilo general

Usar colores suaves, modernos y Bootstrap 5 (ya incluido).

# 🧩 34. Estructura completa

```plaintext
UIII_Dulceria_0726/
 ├── .venv/
 ├── backend_Dulceria/
 │   │   ├── __init__.py
 │   │   ├── settings.py
 │   │   ├── urls.py
 │   │   └── wsgi.py
 ├── app_Dulceria/
 │   ├── templates/
 │   │   ├── base.html
 │   │   ├── navbar.html
 │   │   ├── footer.html
 │   │   ├── inicio.html
 │   │   └── dulce/
 |   |       ├── agregar_dulce.html 
 │   |       ├── ver_dulces.html 
 │   |       ├── actualizar_dulce.html 
 │   |       └── borrar_dulce.html 
 │   ├── models.py
 │   ├── views.py
 │   ├── urls.py
 │   ├── tests.py
 │   ├── admin.py
 │   └── __init__.py
 ├── manage.py
 └── .venv/
```
# ✅ 35. Proyecto totalmente funcional
# 🚀 36. Ejecutar servidor
# python manage.py runserver 0726


Abre en navegador:

http://127.0.0.1:0726/

