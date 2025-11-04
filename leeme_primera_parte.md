Primera Parte: Proyecto Dulcería en Django
A continuación, te proporciono los procedimientos detallados para crear y configurar el proyecto "Dulcería" en Django, paso por paso. Vamos a recorrer el proceso desde la creación de las carpetas hasta la implementación de las vistas y configuraciones necesarias para el funcionamiento del proyecto.

# ==========================================
# 📁 Estructura Final del Proyecto
# ==========================================

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

Procedimiento para crear el Proyecto y su Entorno:

Procedimiento para crear carpeta del Proyecto:

Crea la carpeta donde se almacenará el proyecto con el nombre UIII_Dulceria_0726:

mkdir UIII_Dulceria_0726
cd UIII_Dulceria_0726


Procedimiento para abrir VS Code sobre la carpeta:

Para abrir VS Code directamente sobre la carpeta creada, usa:

code .


Procedimiento para abrir terminal en VS Code:

Abre la terminal en VS Code usando el atajo de teclado:

Windows/Linux: Ctrl + ~

Mac: Cmd + ~

Procedimiento para crear carpeta entorno virtual .venv desde la terminal de VS Code:

En la terminal, ejecuta el siguiente comando para crear un entorno virtual:

python -m venv .venv


Procedimiento para activar el entorno virtual:

Para activar el entorno virtual, dependiendo de tu sistema operativo, ejecuta uno de los siguientes comandos:

Windows:

.venv\Scripts\activate


Linux/Mac:

source .venv/bin/activate


Procedimiento para activar el intérprete de Python:

Asegúrate de que VS Code esté usando el intérprete de Python del entorno virtual, el cual puedes configurar desde la paleta de comandos (Ctrl + Shift + P o Cmd + Shift + P en Mac) y luego buscar "Python: Select Interpreter" y seleccionar .venv.

Procedimiento para instalar Django:

Con el entorno virtual activado, instala Django ejecutando:

pip install django


Procedimiento para crear proyecto backend_Dulceria sin duplicar carpeta:

Desde la terminal, crea el proyecto Django:

django-admin startproject backend_Dulceria


Procedimiento para ejecutar servidor en el puerto 0726:

Corre el servidor Django en el puerto 0726:

python manage.py runserver 0726


Procedimiento para copiar y pegar el link en el navegador:

Después de ejecutar el servidor, abre tu navegador y ve a:

http://127.0.0.1:0726

Modelo models.py:

Aquí el modelo models.py:

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

Procedimientos adicionales:

Procedimiento para realizar las migraciones (makemigrations y migrate):

Ejecuta las migraciones para aplicar los modelos:

python manage.py makemigrations
python manage.py migrate


Primero trabajamos con el modelo DULCES:

Aquí empezarás a trabajar con el modelo de los "Dulces", añadiendo las vistas y la lógica de CRUD.

En views.py de app_dulceria crear las funciones con sus códigos correspondientes:

Define las funciones correspondientes como inicio_dulceria, agregar_dulce, actualizar_dulce, realizar_actualizacion_dulce, y borrar_dulce en views.py.

Crear la carpeta templates dentro de app_Dulceria:

Crea la estructura para las plantillas:

mkdir app_Dulceria/templates


En la carpeta templates, crear los archivos HTML (base.html, header.html, navbar.html, footer.html, inicio.html).

En el archivo base.html, agregar Bootstrap para CSS y JS.

En el archivo navbar.html, incluir las opciones de navegación con iconos.

En el archivo footer.html, incluir derechos de autor, fecha del sistema, y “Creado por Dominique Vaquera, Cbtis 128”. Mantenerlo fijo al final de la página.

En el archivo inicio.html, mostrar información del sistema más una imagen tomada desde la red sobre cinepolis.

Crear la subcarpeta dulce dentro de app_Dulceria/templates:

mkdir app_Dulceria/templates/dulce


Crear los archivos HTML correspondientes en app_Dulceria/templates/dulce (agregar_dulce.html, ver_dulces.html, actualizar_dulce.html, borrar_dulce.html).

No utilizar forms.py en este caso.

Procedimiento para crear el archivo urls.py en app_Dulceria para las operaciones CRUD.

Agregar app_Dulceria en settings.py de backend_Dulceria.

Configurar urls.py en backend_Dulceria para enlazar con app_Dulceria.

Registrar los modelos en admin.py y volver a realizar las migraciones.

Utilizar colores suaves y atractivos, código web sencillo.

No validar entrada de datos.

Crear la estructura completa de carpetas y archivos al inicio.

Ejecutar servidor en el puerto 0726 para probar el proyecto.

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

ChatGPT Plus

¡Claro! A continuación te dejo una versión más detallada de cada uno de los pasos que necesitas seguir para completar tu proyecto Dulcería en Django, organizados de manera clara y estructurada. Esto incluye todos los procedimientos necesarios, detalles sobre cada archivo, y la lógica que debes implementar.

1. Crear la carpeta del Proyecto:
Procedimiento:

Crea una nueva carpeta en tu máquina donde se almacenará todo el proyecto. Puedes hacerlo manualmente o mediante la terminal (línea de comandos).

Comando en terminal (Linux/Mac/Windows):

mkdir UIII_Dulceria_0726
cd UIII_Dulceria_0726


Este paso crea una carpeta llamada UIII_Dulceria_0726 y entra dentro de ella.

2. Abrir VS Code sobre la carpeta creada:
Procedimiento:

Abre la carpeta que acabas de crear en Visual Studio Code. Puedes hacerlo desde la terminal o directamente desde la interfaz de VS Code.

Comando en terminal:

code .


Esto abrirá VS Code en la carpeta actual.

3. Abrir terminal en VS Code:
Procedimiento:

Una vez que tengas VS Code abierto, abre la terminal integrada dentro del editor para trabajar con los comandos.

Atajo de teclado:

Windows/Linux: Ctrl + ~

Mac: Cmd + ~

4. Crear el entorno virtual (.venv) desde la terminal de VS Code:
Procedimiento:

Desde la terminal integrada en VS Code, crea un entorno virtual. Esto es esencial para gestionar las dependencias de Python y mantener tu proyecto aislado.

Comando para crear entorno virtual:

python -m venv .venv


Esto creará una carpeta llamada .venv en la que se almacenarán las dependencias del proyecto.

5. Activar el entorno virtual:
Procedimiento:

Una vez creado el entorno virtual, deberás activarlo. Dependiendo de tu sistema operativo, utiliza uno de los siguientes comandos:

Comando para activar el entorno virtual:

Windows:

.venv\Scripts\activate


Linux/Mac:

source .venv/bin/activate


Si todo ha salido bien, verás que el nombre del entorno virtual (.venv) aparece en tu terminal.

6. Activar el intérprete de Python en VS Code:
Procedimiento:

Para asegurarte de que VS Code está usando el entorno virtual como el intérprete de Python, ve a la paleta de comandos de VS Code (presionando Ctrl + Shift + P o Cmd + Shift + P en Mac) y busca "Python: Select Interpreter".

Selecciona el intérprete que se encuentra dentro de tu carpeta .venv.

7. Instalar Django:
Procedimiento:

Con el entorno virtual activado, ahora puedes instalar Django, que es el framework que utilizarás para desarrollar el proyecto.

Comando para instalar Django:

pip install django


Este comando instalará la última versión de Django dentro de tu entorno virtual.

8. Crear el proyecto backend_Dulceria:
Procedimiento:

Con Django ya instalado, crea el proyecto principal de tu backend. Este es el contenedor principal del proyecto en el que se manejarán las configuraciones y las aplicaciones.

Comando para crear el proyecto:

django-admin startproject backend_Dulceria


Este comando creará una nueva carpeta llamada backend_Dulceria, dentro de la cual estará toda la configuración del proyecto Django.

9. Ejecutar el servidor en el puerto 0726:
Procedimiento:

Desde la terminal, navega hasta la carpeta del proyecto (si aún no estás dentro de ella) y ejecuta el servidor de desarrollo de Django.

Comando para ejecutar el servidor:

python manage.py runserver 0726


Esto ejecutará el servidor de desarrollo de Django en el puerto 0726. Ahora puedes abrir tu navegador y acceder a la URL http://127.0.0.1:0726 para ver la página de bienvenida de Django.

10. Copiar y pegar el link en el navegador:
Procedimiento:

Abre un navegador web y navega a la siguiente URL para ver el servidor en funcionamiento:

http://127.0.0.1:0726

11. Crear el modelo Dulce en models.py:
Procedimiento:

Dirígete a la carpeta backend_Dulceria y dentro de ella abre el archivo models.py que está en el directorio app_Dulceria.

Define el modelo Dulce para almacenar la información de los dulces disponibles en la tienda:

from django.db import models

class Dulce(models.Model):
    nombre = models.CharField(max_length=150, help_text="Nombre del dulce")
    descripcion = models.TextField(blank=True, null=True, help_text="Descripción detallada del dulce")
    precio = models.DecimalField(max_digits=10, decimal_places=2, help_text="Precio del dulce")
    stock = models.PositiveIntegerField(help_text="Cantidad de unidades en stock")
    categoria = models.CharField(max_length=50, help_text="Categoría del dulce (ej. Chocolates, Gomas, Caramelos)")
    fecha_disponibilidad = models.DateField(help_text="Fecha a partir de la cual el dulce está disponible")

    def __str__(self):
        return self.nombre


Este modelo tiene atributos para el nombre, descripción, precio, stock, categoría y fecha de disponibilidad del dulce.

12. Realizar las migraciones (makemigrations y migrate):
Procedimiento:

Para que los cambios en los modelos se apliquen a la base de datos, debes ejecutar las migraciones. Django genera scripts de migración que luego aplicas a la base de datos.

Comando para crear migraciones:

python manage.py makemigrations


Comando para aplicar las migraciones:

python manage.py migrate


Estos dos comandos aseguran que la estructura de la base de datos esté actualizada con los nuevos modelos definidos en models.py.

13. Trabajar con el modelo DULCES en las vistas (views.py):
Procedimiento:

Crea las vistas necesarias para gestionar los dulces en el sistema, tales como ver, agregar, editar y borrar dulces.

Función de ejemplo en views.py:

from django.shortcuts import render, get_object_or_404, redirect
from .models import Dulce
from django.http import HttpResponse

def inicio_dulceria(request):
    # Vista que muestra todos los dulces disponibles
    dulces = Dulce.objects.all()
    return render(request, 'inicio.html', {'dulces': dulces})

def agregar_dulce(request):
    if request.method == 'POST':
        # Lógica para agregar un dulce
        nombre = request.POST.get('nombre')
        precio = request.POST.get('precio')
        dulce = Dulce.objects.create(nombre=nombre, precio=precio)
        return redirect('inicio_dulceria')  # Redirige a la vista de inicio
    return render(request, 'agregar_dulce.html')

14. Crear la carpeta templates dentro de app_Dulceria:
Procedimiento:

En la carpeta app_Dulceria, crea una subcarpeta llamada templates donde colocarás todos los archivos HTML para las vistas.

Comando en terminal:

mkdir app_Dulceria/templates


Dentro de esta carpeta, crea archivos HTML como base.html, header.html, footer.html, etc., que compartirán el mismo diseño base.

15. Agregar Bootstrap en base.html:
Procedimiento:

En el archivo base.html, agrega el enlace a Bootstrap para utilizar su diseño responsivo y fácil de usar.

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dulcería</title>
    <!-- Incluir Bootstrap -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    {% include 'header.html' %}
    <div class="container">
        {% block content %}
        {% endblock %}
    </div>
    {% include 'footer.html' %}
</body>
</html>

16. Crear otros archivos HTML como navbar.html, footer.html, etc.:
Procedimiento:

navbar.html: Aquí incluirás la barra de navegación con las opciones del sistema y los submenús:

<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <a class="navbar-brand" href="#">Sistema de Administración Dulcería</a>
  <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-icon"></span>
  </button>
  <div class="collapse navbar-collapse" id="navbarNav">
    <ul class="navbar-nav">
      <li class="nav-item active">
        <a class="nav-link" href="#">Inicio</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#">Dulces</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#">Clientes</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#">Pedidos</a>
      </li>
    </ul>
  </div>
</nav>

17. Procedimiento para crear las URLs en urls.py:
Procedimiento:

Crea el archivo urls.py dentro de app_Dulceria para definir las rutas de las vistas CRUD. Por ejemplo:

from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_dulceria, name='inicio_dulceria'),
    path('agregar/', views.agregar_dulce, name='agregar_dulce'),
]

Conclusión:

Estos pasos cubren desde la creación de tu entorno de desarrollo hasta la implementación de los modelos y vistas CRUD básicos. ¡Espero que con estos detalles puedas avanzar fácilmente con tu proyecto! Si tienes alguna pregunta sobre algún paso, no dudes en preguntar.
