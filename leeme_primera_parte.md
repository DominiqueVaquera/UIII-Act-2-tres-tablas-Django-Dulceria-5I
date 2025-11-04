Primera Parte: Proyecto Dulcería en Django
A continuación, te proporciono los procedimientos detallados para crear y configurar el proyecto "Dulcería" en Django, paso por paso. Vamos a recorrer el proceso desde la creación de las carpetas hasta la implementación de las vistas y configuraciones necesarias para el funcionamiento del proyecto.
#
# ==========================================
# 📁 Estructura Final del Proyecto
# ==========================================
# Estructura Final del Proyecto

```plaintext
Curso_Django/
└── Dulceria/
    ├── .venv/                     # Entorno virtual
    ├── backend_dulceria/           # Configuración del proyecto
    │   ├── __init__.py
    │   ├── settings.py
    │   ├── urls.py
    │   └── wsgi.py
    ├── app_dulceria/
    │   ├── __init__.py
    │   ├── models.py
    │   ├── admin.py
    │   ├── forms.py
    │   ├── views.py
    │   ├── urls.py
    │   ├── templates/
    │   │   ├── base.html
    │   │   ├── listar_productos.html
    │   │   ├── detalle_producto.html
    │   │   ├── formulario_producto.html
    │   │   └── confirmar_borrar.html
    │   └── static/
    │       └── css/
    │           └── styles.css
    ├── media/                     # Imágenes subidas (creado automáticamente)
    ├── manage.py
    └── requirements.txt

```

Paso 1: Cambio en los Modelos
    
1. Procedimiento para crear la carpeta del Proyecto:
Crea una carpeta donde será almacenado el proyecto. Puedes hacerlo manualmente o usando la terminal de VS Code.
mkdir UIII_Dulceria_0726

2. Procedimiento para abrir VS Code sobre la carpeta:
Abre la terminal en la carpeta donde creaste el proyecto y luego ejecuta:
code .

Esto abrirá VS Code en la carpeta UIII_Dulceria_0726.
3. Procedimiento para abrir la terminal en VS Code:
Para abrir la terminal dentro de VS Code, puedes usar el atajo de teclado:
En Windows/Linux: Ctrl + ~


En Mac: Cmd + ~


O bien, desde el menú en la parte superior:
 Terminal -> Nueva Terminal.
4. Procedimiento para crear la carpeta entorno virtual “.venv” desde la terminal de VS Code:
Crea un entorno virtual con el siguiente comando:
python -m venv .venv

Esto creará una carpeta llamada .venv dentro de tu proyecto.
5. Procedimiento para activar el entorno virtual:
Dependiendo de tu sistema operativo, usa uno de los siguientes comandos:
Windows:

 .\.venv\Scripts\activate


Linux/Mac:

 source .venv/bin/activate


Al activar el entorno virtual, deberías ver algo como (.venv) en la terminal.
6. Procedimiento para activar el intérprete de Python:
Una vez activado el entorno virtual, asegúrate de que VS Code esté usando el intérprete de Python correcto. Para hacerlo, ve a:
Cmd+Shift+P (Mac) o Ctrl+Shift+P (Windows/Linux).


Escribe y selecciona Python: Select Interpreter.


Elige el intérprete dentro del entorno virtual que acabas de crear (.venv).


7. Procedimiento para instalar Django:
Ahora, instala Django en tu entorno virtual usando pip:
pip install django

8. Procedimiento para crear el proyecto backend_Dulceria:
Con Django instalado, crea el proyecto con el siguiente comando:
django-admin startproject backend_Dulceria .

Asegúrate de usar el punto (.) al final para evitar que Django cree una subcarpeta.
9. Procedimiento para ejecutar el servidor en el puerto 0726:
Para ejecutar el servidor de desarrollo de Django en el puerto 0726, usa:
python manage.py runserver 0726

10. Procedimiento para copiar y pegar el link en el navegador:
Abre tu navegador y navega a:
http://127.0.0.1:0726

Ahí verás la página de inicio de Django si todo está funcionando correctamente.
11. Procedimiento para crear la aplicación app_Dulceria:
Ahora crea la aplicación app_Dulceria dentro de tu proyecto Django:
python manage.py startapp app_Dulceria

12. Modelo models.py (Dulces, Clientes, Pedidos):
Ya proporcionaste los modelos. Aquí están con comentarios y formateo adecuado:
from django.db import models

# ==========================================
# MODELO: Dulces
# ==========================================
class Dulce(models.Model):
    nombre = models.CharField(max_length=150, help_text="Nombre del dulce")
    descripcion = models.TextField(blank=True, null=True, help_text="Descripción detallada del dulce")
    precio = models.DecimalField(max_digits=10, decimal_places=2, help_text="Precio del dulce")
    stock = models.PositiveIntegerField(help_text="Cantidad de unidades en stock")
    categoria = models.CharField(max_length=50, help_text="Categoría del dulce (ej. Chocolates, Gomas, Caramelos)")
    fecha_disponibilidad = models.DateField(help_text="Fecha a partir de la cual el dulce está disponible")
    
    def __str__(self):
        return self.nombre

# ==========================================
# MODELO: CLIENTES
# ==========================================
class Cliente(models.Model):
    nombre = models.CharField(max_length=100, unique=True)
    apellido = models.CharField(max_length=100, blank=True, null=True) 
    email = models.EmailField(max_length=254, unique=True) 
    telefono = models.CharField(max_length=20, blank=True, null=True)
    direccion = models.CharField(max_length=255, blank=True, null=True)
    fecha_registro = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f"{self.nombre} {self.apellido}"

# ==========================================
# MODELO: PEDIDOS
# ==========================================
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
        return f"Pedido #{self.pk} de {self.cliente.nombre} - Estado: {self.estado}"

12.5. Procedimiento para realizar las migraciones:
Ejecuta los siguientes comandos para realizar las migraciones de tus modelos en la base de datos:
python manage.py makemigrations
python manage.py migrate


Continuación de los Procedimientos
En los siguientes pasos, te guiaré sobre cómo implementar las vistas y configuraciones adicionales como las plantillas HTML, URLs, la creación de formularios y otras configuraciones necesarias para tu aplicación.
Si deseas continuar con estos procedimientos, me avisas y los vamos detallando uno por uno.

Paso 1: Cambio en los Modelos

Adaptaremos los modelos a la idea de una dulcería, donde los productos serían dulces y podrían tener atributos como tipo, sabor, y foto.

app_dulceria/models.py

from django.db import models

class Dulce(models.Model):
    nombre = models.CharField(max_length=100, help_text="Nombre del dulce")
    sabor = models.CharField(max_length=100, help_text="Sabor del dulce")
    foto_dulce = models.ImageField(upload_to='img_dulces/', blank=True, null=True)

    def __str__(self):
        return self.nombre

    class Meta:
        verbose_name = "Dulce"
        verbose_name_plural = "Dulces"

Paso 2: Cambiar Vistas

Las vistas reflejarán lo que se muestra en una dulcería: la lista de dulces, los detalles de un dulce específico, la creación, edición y eliminación de dulces.

app_dulceria/views.py

from django.shortcuts import render, get_object_or_404, redirect
from .models import Dulce
from .forms import DulceForm

def listar_dulces(request):
    dulces = Dulce.objects.all()
    return render(request, 'listar_dulces.html', {'dulces': dulces})

def detalle_dulce(request, dulce_id):
    dulce = get_object_or_404(Dulce, id=dulce_id)
    return render(request, 'detalle_dulce.html', {'dulce': dulce})

def crear_dulce(request):
    if request.method == 'POST':
        form = DulceForm(request.POST, request.FILES)
        if form.is_valid():
            form.save()
            return redirect('app_dulceria:listar_dulces')
    else:
        form = DulceForm()
    return render(request, 'formulario_dulce.html', {'form': form, 'titulo': 'Crear Dulce'})

def editar_dulce(request, dulce_id):
    dulce = get_object_or_404(Dulce, id=dulce_id)
    if request.method == 'POST':
        form = DulceForm(request.POST, request.FILES, instance=dulce)
        if form.is_valid():
            form.save()
            return redirect('app_dulceria:detalle_dulce', dulce_id=dulce.id)
    else:
        form = DulceForm(instance=dulce)
    return render(request, 'formulario_dulce.html', {'form': form, 'titulo': 'Editar Dulce'})

def borrar_dulce(request, dulce_id):
    dulce = get_object_or_404(Dulce, id=dulce_id)
    if request.method == 'POST':
        dulce.delete()
        return redirect('app_dulceria:listar_dulces')
    return render(request, 'confirmar_borrar.html', {'dulce': dulce})

Paso 3: Formularios

El formulario será para crear y editar dulces.

app_dulceria/forms.py

from django import forms
from .models import Dulce

class DulceForm(forms.ModelForm):
    class Meta:
        model = Dulce
        fields = ['nombre', 'sabor', 'foto_dulce']

Paso 4: Actualizar Plantillas HTML

Las plantillas de la aplicación cambiarán los textos y las imágenes de "artistas" a "dulces".

app_dulceria/templates/base.html (cambiar los nombres y clases de los enlaces)

{% load static %}
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block titulo %}Dulcería{% endblock %}</title>
    <link rel="stylesheet" href="{% static 'css/styles.css' %}">
</head>
<body>
    <div class="wrapper">
        <header class="main-header">
            <h1><a href="{% url 'app_dulceria:listar_dulces' %}">🍬 Dulcería</a></h1>
            <nav class="main-nav">
                <a href="{% url 'app_dulceria:listar_dulces' %}">🍬 Dulces</a>
                <a href="{% url 'app_dulceria:crear_dulce' %}">➕ Nuevo Dulce</a>
            </nav>
        </header>

        <main class="main-content">
            {% block contenido %}{% endblock %}
        </main>

        <footer class="main-footer">
            <p>© 2025 Dulcería - ¡La mejor selección de dulces!</p>
        </footer>
    </div>
</body>
</html>


app_dulceria/templates/listar_dulces.html

{% extends 'base.html' %}
{% block titulo %}Dulces | Dulcería{% endblock %}

{% block contenido %}
<div class="page-header">
    <h2>Dulces Disponibles</h2>
    <a href="{% url 'app_dulceria:crear_dulce' %}" class="btn btn-primary">+ Agregar Dulce</a>
</div>

<ul class="dulce-grid">
    {% for dulce in dulces %}
    <li class="dulce-card">
        <a href="{% url 'app_dulceria:detalle_dulce' dulce.id %}">
            {% if dulce.foto_dulce %}
                <img src="{{ dulce.foto_dulce.url }}" alt="Foto de {{ dulce.nombre }}" class="dulce-img">
            {% else %}
                <div class="dulce-placeholder">🍬</div>
            {% endif %}
            <h3>{{ dulce.nombre }}</h3>
            <p class="dulce-sabor">{{ dulce.sabor }}</p>
        </a>
    </li>
    {% empty %}
    <li class="no-dulces">No hay dulces disponibles.</li>
    {% endfor %}
</ul>
{% endblock %}


app_dulceria/templates/detalle_dulce.html

{% extends 'base.html' %}
{% block titulo %}{{ dulce.nombre }} | Dulcería{% endblock %}

{% block contenido %}
<article class="dulce-detail">
    <div class="dulce-banner">
        {% if dulce.foto_dulce %}
            <img src="{{ dulce.foto_dulce.url }}" alt="Foto de {{ dulce.nombre }}" class="detail-photo">
        {% else %}
            <div class="detail-placeholder">🍬</div>
        {% endif %}
        <div class="dulce-info">
            <h1>{{ dulce.nombre }}</h1>
            <h3>Sabor: {{ dulce.sabor }}</h3>
        </div>
    </div>

    <div class="detail-actions">
        <a href="{% url 'app_dulceria:editar_dulce' dulce.id %}" class="btn btn-secondary">✏️ Editar</a>
        <a href="{% url 'app_dulceria:borrar_dulce' dulce.id %}" class="btn btn-danger">🗑️ Eliminar</a>
    </div>
</article>
{% endblock %}


app_dulceria/templates/formulario_dulce.html

{% extends 'base.html' %}
{% block titulo %}{{ titulo }} | Dulcería{% endblock %}

{% block contenido %}
<h2>{{ titulo }}</h2>
<form method="post" enctype="multipart/form-data" class="form-styled">
    {% csrf_token %}
    {{ form.as_p }}
    <div class="actions">
        <button type="submit" class="btn btn-primary">Guardar</button>
        <a href="{% url 'app_dulceria:listar_dulces'
