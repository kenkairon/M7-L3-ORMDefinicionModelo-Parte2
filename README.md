# M7-L3-ORMDefinicionModelo-Parte2
Nuevo Proyecto Django: Sistema de Gestión de Cursos

---
## Tabla de Contenidos
- [Tecnologías](#Tecnologías)
- [Configuración Inicial](#configuración-Inicial)
- [Configuración Base de datos](#configuración-Base-de-datos)
- [Creación del Modelo](#creación-del-modelo)
- [Creación de Vistas](#creación-de-vistas)

---
# Tecnologías
- Django: Framework web en Python.
- PostgreSQL: Base de datos relacional avanzada 
--- 
# Configuración Inicial 
1. Entorno virtual 
    ```bash 
    python -m venv venv

2. Activar el entorno virtual
    ```bash 
    venv\Scripts\activate

3. Instalar Django
    ```bash 
    pip install django 

4. Actulizamos el pip 
    ```bash
    python.exe -m pip install --upgrade pip

5. Crear el proyecto de django
    ```bash 
    django-admin startproject proyecto_eductativo

6. Ingresamos al proyecto proyecto_eductativo
    ```bash 
    cd mi_proyecto

7. Creamos la aplicacion llamada gestion_cursos
    ```bash     
    python manage.py startapp gestion_cursos

8. Configuración de mi_proyecto/settings.py 
    ```bash 
    INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'gestion_cursos',
    ]

# Configuración Base de datos
9. Instalar python-decouple: Es una biblioteca que ayuda manejar las variables de entorno 
    ```bash
    pip install python-decouple

10. Creamos el archivo .env a la altura del proyecto al lado manage.py 
    ```bash
    DATABASE_NAME=nombre_base_de_datos
    DATABASE_USER=postgres
    DATABASE_PASSWORD=yourpassword
    DATABASE_HOST=localhost
    DATABASE_PORT=5432

11. Configuracion de la base de datos ingresando los parametros de conexión 
    ```bash
    from decouple import config

    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.postgresql',
            'NAME': config('DATABASE_NAME'),
            'USER': config('DATABASE_USER'),
            'PASSWORD': config('DATABASE_PASSWORD'),
            'HOST': config('DATABASE_HOST'),
            'PORT': config('DATABASE_PORT'),
        }
    }
12. Instalacion de psycopg2: es un adaptador de base de datos para Python que permite interactuar con bases de datos PostgreSQL
    ```bash
    pip install psycopg2 

13. Guardo las dependencias me voy un cd .. mas atras del proyecto principal con el objetivo que quede al lado del README.md
    ```bash
    cd ..
    cd ..
    pip freeze > requirements.txt

# Creación del Modelo 

14. en gestion_cursos/models.py
    ```bash
    from django.db import models

    class Curso(models.Model):
        nombre = models.CharField(max_length=100)
        descripcion = models.TextField()
        duracion_horas = models.PositiveIntegerField()
        fecha_inicio = models.DateField()
        fecha_fin = models.DateField()

        def __str__(self):
            return self.nombre


15. Ejecuta las migraciones para aplicar estos cambios a la base de datos:
    ```bash 
    python manage.py makemigrations
    python manage.py migrate

16. gestion/forms.py
    ```bash 
    from django import forms
    from .models import Curso

    class CursoForm(forms.ModelForm):
        class Meta:
            model = Curso
            fields = ['nombre', 'descripcion', 'duracion_horas', 'fecha_inicio', 'fecha_fin']

## Creación de Vistas
17. gestion_cursos/views.py 
    ```bash 
    from django.shortcuts import render, redirect
    from .models import Curso
    from .forms import CursoForm

    # Página de inicio
    def index(request):
        return render(request, 'gestion_cursos/index.html')

    # Lista de cursos
    def lista_cursos(request):
        cursos = Curso.objects.all()
        return render(request, 'gestion_cursos/lista_cursos.html', {'cursos': cursos})

    # Crear un nuevo curso
    def crear_curso(request):
        if request.method == 'POST':
            form = CursoForm(request.POST)
            if form.is_valid():
                form.save()
                return redirect('lista_cursos')
        else:
            form = CursoForm()
        return render(request, 'gestion_cursos/crear_curso.html', {'form': form})

18. creamos en templates/gestion_cursos/lista_cursos.html 
    ```bash 
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Lista de Cursos</title>
    </head>

    <body>
        <h1>Lista de Cursos</h1>
        <table border="1">
            <thead>
                <tr>
                    <th>Nombre</th>
                    <th>Descripción</th>
                    <th>Duración (horas)</th>
                    <th>Fecha Inicio</th>
                    <th>Fecha Fin</th>
                </tr>
            </thead>
            <tbody>
                {% for curso in cursos %}
                <tr>
                    <td>{{ curso.nombre }}</td>
                    <td>{{ curso.descripcion }}</td>
                    <td>{{ curso.duracion_horas }}</td>
                    <td>{{ curso.fecha_inicio }}</td>
                    <td>{{ curso.fecha_fin }}</td>
                </tr>
                {% endfor %}
            </tbody>
        </table>
        <a href="{% url 'crear_curso' %}">Registrar Nuevo Curso</a>
    </body>

    </html>
19. creamos en templates/gestion_cursos/index.html 
    ```bash 
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Página de Inicio</title>
    </head>

    <body>
        <h1>Bienvenido al Sistema de Gestión de Cursos</h1>
        <nav>
            <ul>
                <li><a href="{% url 'lista_cursos' %}">Lista de Cursos</a></li>
                <li><a href="{% url 'crear_curso' %}">Registrar Curso</a></li>
            </ul>
        </nav>
    </body>

    </html>

20. Ingresamos a las rutas http://127.0.0.1:8000/

    ```bash 
    python manage.py runserver