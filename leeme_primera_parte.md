Primera Parte: Proyecto Dulcería en Django
A continuación, te proporciono los procedimientos detallados para crear y configurar el proyecto "Dulcería" en Django, paso por paso. Vamos a recorrer el proceso desde la creación de las carpetas hasta la implementación de las vistas y configuraciones necesarias para el funcionamiento del proyecto.

Estructura del Proyecto:
Curso_Django/
└── Dulceria/
    ├── .venv/                     # Entorno virtual
    ├── backend_dulceria/          # Configuración del proyecto
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
    │   │   ├── listar_dulces.html
    │   │   ├── detalle_dulce.html
    │   │   ├── formulario_dulce.html
    │   │   └── confirmar_borrar.html
    │   └── static/
    │       └── css/
    │           └── styles.css
    ├── media/                     # Imágenes subidas (creado automáticamente)
    ├── manage.py
    └── requirements.txt

    
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
