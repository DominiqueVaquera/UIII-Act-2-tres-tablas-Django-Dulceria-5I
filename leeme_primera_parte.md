Primera Parte: Proyecto Dulcería en Django
A continuación, te proporciono los procedimientos detallados para crear y configurar el proyecto "Dulcería" en Django, paso por paso. Vamos a recorrer el proceso desde la creación de las carpetas hasta la implementación de las vistas y configuraciones necesarias para el funcionamiento del proyecto.

# ==========================================
# 📁 Estructura Final del Proyecto
# ==========================================

```plaintext
UIII_Dulceria_0726/
 ├── .venv/
 ├── backend_Dulceria/
 ├── app_Dulceria/
 │   ├── templates/
 │   │   ├── dulce/
 │   │   ├── base.html
 │   │   ├── navbar.html
 │   │   ├── footer.html
 │   │   └── inicio.html
 │   ├── models.py
 │   ├── views.py
 │   ├── urls.py
 │   └── admin.py
 ├── manage.py

```
#🧁 Proyecto Django: Dulcería

Lenguaje: Python
Framework: Django
Editor: Visual Studio Code
Puerto: 0726

#📁 1. Crear carpeta del proyecto
mkdir UIII_Dulceria_0726
cd UIII_Dulceria_0726

#💻 2. Abrir VS Code sobre la carpeta
code .

#🧭 3. Abrir terminal en VS Code

Desde la barra superior:

Ver → Terminal

#🧩 4. Crear entorno virtual “.venv”
python -m venv .venv

#⚙️ 5. Activar el entorno virtual
En Windows:
.venv\Scripts\activate

En macOS/Linux:
source .venv/bin/activate

#🐍 6. Activar intérprete de Python

En VS Code:
Presiona Ctrl + Shift + P → Escribe “Python: Select Interpreter” → Selecciona el que diga .venv.

#📦 7. Instalar Django
pip install django

#🚀 8. Crear proyecto backend_Dulceria sin duplicar carpeta
django-admin startproject backend_Dulceria .


(El punto evita que se cree una carpeta duplicada).

#🌐 9. Ejecutar servidor en el puerto 0726
python manage.py runserver 0726

#🔗 10. Copiar y pegar el link en el navegador

Abre en tu navegador:

http://127.0.0.1:0726/

#🧱 11. Crear aplicación app_Dulceria
python manage.py startapp app_Dulceria

#🧬 12. Código del archivo models.py (app_Dulceria/models.py)
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
#⚙️ 12.5 Realizar migraciones
python manage.py makemigrations
python manage.py migrate

#🍬 13. Trabajaremos primero con el modelo Dulce
#👁️‍🗨️ 14. Código de views.py (CRUD de Dulces)
```plaintext
from django.shortcuts import render, redirect, get_object_or_404
from .models import Dulce

def inicio_dulceria(request):
    return render(request, 'inicio.html')

def agregar_dulce(request):
    if request.method == 'POST':
        nombre = request.POST['nombre']
        descripcion = request.POST['descripcion']
        precio = request.POST['precio']
        stock = request.POST['stock']
        categoria = request.POST['categoria']
        fecha_disponibilidad = request.POST['fecha_disponibilidad']
        Dulce.objects.create(
            nombre=nombre,
            descripcion=descripcion,
            precio=precio,
            stock=stock,
            categoria=categoria,
            fecha_disponibilidad=fecha_disponibilidad
        )
        return redirect('ver_dulces')
    return render(request, 'dulce/agregar_dulce.html')

def ver_dulces(request):
    dulces = Dulce.objects.all()
    return render(request, 'dulce/ver_dulces.html', {'dulces': dulces})

def actualizar_dulce(request, id):
    dulce = get_object_or_404(Dulce, id=id)
    return render(request, 'dulce/actualizar_dulce.html', {'dulce': dulce})

def realizar_actualizacion_dulce(request, id):
    dulce = get_object_or_404(Dulce, id=id)
    dulce.nombre = request.POST['nombre']
    dulce.descripcion = request.POST['descripcion']
    dulce.precio = request.POST['precio']
    dulce.stock = request.POST['stock']
    dulce.categoria = request.POST['categoria']
    dulce.fecha_disponibilidad = request.POST['fecha_disponibilidad']
    dulce.save()
    return redirect('ver_dulces')

def borrar_dulce(request, id):
    dulce = get_object_or_404(Dulce, id=id)
    dulce.delete()
    return redirect('ver_dulces')
```
#🧾 15. Crear carpeta templates
```plaintext
app_Dulceria/
 └── templates/
```
#🧱 16. Archivos dentro de templates
```plaintext
templates/
 ├── base.html
 ├── header.html
 ├── navbar.html
 ├── footer.html
 └── inicio.html
```
#🎨 17. base.html (Bootstrap)
```plaintext
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Dulcería</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
</head>
<body class="bg-light">
    {% include 'navbar.html' %}
    <div class="container mt-4">
        {% block contenido %}{% endblock %}
    </div>
    {% include 'footer.html' %}
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```
#🧭 18. navbar.html
```plaintext
<nav class="navbar navbar-expand-lg navbar-dark bg-primary">
  <div class="container-fluid">
    <a class="navbar-brand" href="#">🍭 Sistema de Administración Dulcería</a>
    <div class="collapse navbar-collapse">
      <ul class="navbar-nav">
        <li class="nav-item"><a class="nav-link" href="{% url 'inicio_dulceria' %}">🏠 Inicio</a></li>
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown">🍬 Dulces</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="{% url 'agregar_dulce' %}">Agregar Dulce</a></li>
            <li><a class="dropdown-item" href="{% url 'ver_dulces' %}">Ver Dulces</a></li>
          </ul>
        </li>
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#">👤 Clientes</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar Cliente</a></li>
          </ul>
        </li>
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#">📦 Pedidos</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar Pedido</a></li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
</nav>
```
#🦶 19. footer.html
```plaintext
<footer class="bg-dark text-white text-center py-3 fixed-bottom">
    © <span id="year"></span> | Creado por Dominique Vaquera, CBTIS 128
    <script>document.getElementById("year").innerHTML = new Date().getFullYear();</script>
</footer>
```
#🏠 20. inicio.html
```plaintext
{% extends 'base.html' %}
{% block contenido %}
<div class="text-center">
    <h1>Bienvenido al Sistema de Administración de Dulcería</h1>
    <p>Gestione sus productos, clientes y pedidos de forma sencilla.</p>
    <img src="https://upload.wikimedia.org/wikipedia/commons/2/2e/Cinepolis_logo.svg" width="300">
</div>
{% endblock %}
```
#📂 21. Subcarpeta dulce
app_Dulceria/templates/dulce/

#📄 22. Archivos HTML CRUD Dulce

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

#🌐 24. Crear urls.py en app_Dulceria
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
#⚙️ 25. Agregar app_Dulceria en settings.py
```plaintext
INSTALLED_APPS = [
    ...
    'app_Dulceria',
]
```
#🧭 26. Configurar urls.py de backend_Dulceria

Archivo: backend_Dulceria/urls.py
```plaintext
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Dulceria.urls')),
]
```
#🛠️ 27. Registrar modelos en admin.py
```plaintext
from django.contrib import admin
from .models import Dulce, Cliente, Pedido

admin.site.register(Dulce)
admin.site.register(Cliente)
admin.site.register(Pedido)
```

Volver a migrar:

python manage.py makemigrations
python manage.py migrate

#🌈 28. Estilo general

Usar colores suaves, modernos y Bootstrap 5 (ya incluido).

#🧩 29. Estructura completa
```plaintext
UIII_Dulceria_0726/
 ├── .venv/
 ├── backend_Dulceria/
 ├── app_Dulceria/
 │   ├── templates/
 │   │   ├── dulce/
 │   │   ├── base.html
 │   │   ├── navbar.html
 │   │   ├── footer.html
 │   │   └── inicio.html
 │   ├── models.py
 │   ├── views.py
 │   ├── urls.py
 │   └── admin.py
 ├── manage.py
```
#✅ 30. Proyecto totalmente funcional
#🚀 31. Ejecutar servidor
python manage.py runserver 0726


Abre en navegador:

http://127.0.0.1:0726/

