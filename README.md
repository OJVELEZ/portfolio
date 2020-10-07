# portfolio
My Portfolio

-1-Instalar Python
0-Instalar Django
	pip install django==3.0.3
	
1-django-admin startproject portfolio
	Crea dos carpeta con el mismo nombre
	Renombrar la carpeta principal
	portfolio -> portfolio-project
	
2-Correr el servidor 
	python manage.py runserver
	en linux o mac sería python3
	
	Evaluar si correo en http://localhost:8000
	administración en http://localhost:8000/admin
	
3- Creación del app	
	app = modulo/funcionalidad
	
	django-admin startapp jobs
	
4-Editar archivo settings.py para agregar el app 'jobs'
	Linea 40 en INSTALLED_APPS
		'jobs',
		
5-URL patterns - URL conf
	Redirigiar a la vista y template adecuados
	Contiene una lista, si el  path no esta en esa lista django retorna 404
	
	Editar el archivo urls.py
		import jobs.views
		Agregar a la lista de paths
			path('nick', jobs.views.nick, name = 'nick'),
		
	Editar views.py 		
		def nick(request):
			return render(request, 'jobs/nick.html')
	
	En la app jobs crear dos nuevos directorios			
		templates/jobs
	
	Un nuevo archivo nick.html dentro de templates/jobs	
		Hello World!
		
	http://localhost:8000/nick

	Luego renombrar todo nick->home
		path('', jobs.views.home, name = 'home'),
		Cambiar views.py y renombrar el html


6-Creación del modelo

	El modelo es una clase que permite operaciones con la BD
	
	Editar models.py
	
	from django.db import models

	class Job(models.Model):
		image = models.ImageField(upload_to='images/')
		summary = models.CharField(max_length=200)

	Libreria para getión de imágenes: pip install pillow
	
7- Bases de datos
	
	En el archivo settings linea 77 hay un segmento DATABASES 
		'NAME': os.path.join(BASE_DIR, 'db.sqlite3')
		
	Descargar postgresql.org

	contraseña: 12345678
	puerto: 5432
	
	crear un bd desde pgadmin nombre: portfoliodb
	
	editar archivo settings.py
	
		DATABASES = {
			'default': {
				'ENGINE': 'django.db.backends.postgresql',
				'NAME': 'portfoliodb',
				'USER': 'postgres',
				'PASSWORD': '12345678',
				'HOST': 'localhost',
				'PORT': '5432'
			}
		}
	
	pip install psycopg2	
	
		
	
8-Migración
	Cambios al modelo deben ser aplicados a la base de datos
	
	
	makemigrations: genera los archivos y la enumeración
		python manage.py makemigrations	
		en la carpeta migrations genera archivo 0001_initial.py que incluye la tabla (Job) y campos
			migrations.CreateModel(
				name='Job',
				fields=[
					('id', models.AutoField(auto_created=True, primary_key=True, serialize=False, verbose_name='ID')),
					('image', models.ImageField(upload_to='images/')),
					('summary', models.CharField(max_length=200)),
				],
			),		
		

	showmigration: muestra las migraciones ejecutadas y las pendientes []=pendiente [X]=ejecutadas
		python manage.py showmigrations

	migrate: aplica los cambios en la BD
		python manage.py migrate	
		
	
	Dar un refresh en la BD desde pgadmin y ver cambios

9-Configuración del admin
	En urls.py encontramos: path('admin/', admin.site.urls)
	http://localhost:8000/admin/
	
	crear superusuario: python manage.py createsuperuser
	id:ovelez
	pass: 12345678
	
	Ahora permite Gestión de usuarios y grupos y los almacena en la BD
	
	Ahora se debe editar admin.py para permitir administrar la tabla Job
		Se debe registrar el modelo que creamos Job
		
		from .models import Job
		
		admin.site.register(Job)
		
	F5 en la página y ya permite administrar la tabla Job	
	
	Crear registro de nuevos Jobs
		Se almacenan en la BD pero la imagen queda como una carpeta al nivel del servidor django no la BD
		La BD solo almacena summary y el path de la imagen
		
		
10-Editar la vista views.py
	Agregar la clase que representa el modelo Job
		from models import Job

		def home(request):
			jobs = Job.objects
			return render(request, 'jobs/home.html', {'jobs': jobs})
			
	Editar home.html			
	<h1>All my jobs</h1>
	{% for job in jobs.all%}
		{{ job.summary}}
	{% endfor %}	
	
11- Agregar diseño con bootstrap

	Copiar el código de: https://getbootstrap.com/docs/4.5/examples/album/#
		Copiarlo en home.html
		
	Luego ir a https://getbootstrap.com/
		Click en get started
		Copiar:
		<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css" integrity="sha384-JcKb8q3iqJ61gNV9KGb8thSsNjpSL0n8PARn9HuZOnIxN0hoP+VmmDGMN5t9UJ0Z" crossorigin="anonymous">
	
	Editar home.html referencias de CSS	
		Reemplazar
			<link href="/docs/4.5/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-JcKb8q3iqJ61gNV9KGb8thSsNjpSL0n8PARn9HuZOnIxN0hoP+VmmDGMN5t9UJ0Z" crossorigin="anonymous">
		por
			<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css" integrity="sha384-JcKb8q3iqJ61gNV9KGb8thSsNjpSL0n8PARn9HuZOnIxN0hoP+VmmDGMN5t9UJ0Z" crossorigin="anonymous">
	

		Referencias javascript
		reemplazar
			<script src="https://code.jquery.com/jquery-3.5.1.slim.min.js" integrity="sha384-DfXdz2htPH0lsSSs5nCTpuj/zy4C+OGpamoFVy38MVBnE+IbbVYUew+OrCXaRkfj" crossorigin="anonymous"></script>
			<script>window.jQuery || document.write('<script src="/docs/4.5/assets/js/vendor/jquery.slim.min.js"><\/script>')</script><script src="/docs/4.5/dist/js/bootstrap.bundle.min.js" integrity="sha384-LtrjvnR4Twt/qOuYxE721u19sVFLVSA4hf/rRt6PrZTmiPltdZcI7q7PXQBYTKyf" crossorigin="anonymous"></script></body>
		por
			<script src="https://code.jquery.com/jquery-3.5.1.slim.min.js" integrity="sha384-DfXdz2htPH0lsSSs5nCTpuj/zy4C+OGpamoFVy38MVBnE+IbbVYUew+OrCXaRkfj" crossorigin="anonymous"></script>
			<script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.1/dist/umd/popper.min.js" integrity="sha384-9/reFTGAW83EW2RDu2S0VKaIzap3H66lZH81PoYlFhbGU+6BZp6G7niu735Sk7lN" crossorigin="anonymous"></script>
			<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/js/bootstrap.min.js" integrity="sha384-B4gt1jrGC7Jh4AgTPSdUtOBvfO8shuf57BaghqFfPlYxofvL8/KUEfYiJOMMV+rV" crossorigin="anonymous"></script>
		
		Editar home.html
			Cambiar Title
			Remover header
			Dejar solo un div de foto
			
12- Agregar información estatica
	
	Foto de perfil
	
	Dentro de la carpeta de la app Job 
		crear un carpeta static
	
	settings.py ir a
		STATIC_URL = '/static/'
		
		agregar
			STATIC_ROOT = os.path.join(BASE_DIR, 'static')
		
		
	editar urls.py
		from django.conf import settings
		from django.conf.urls.static import static

		urlpatterns = [
			path('admin/', admin.site.urls),
			path('', jobs.views.home, name = 'home'),
		] + static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
		
	python manage.py collectstatic		
		copia todo los archivos estaticos incluso los de admin
		crear una carpeta en portfolio-project\static
			la carpeta central del proyecto
			cada app tendrá su propia static
			
	Editar home.html
		Agregar código para que incluya los archivos staticos
			<!doctype html>
			<html lang="en">
			{% load static %}
		Despues del botón
			<img src="{ static 'Photo.png'}"></img>
		
		
13-Descargar localmente los archivos que estan presentes en la página de bootstrap que copiamos
	Actualmente los carga de internet
	
	ir a https://getbootstrap.com/docs/4.5/getting-started/download/
		descargar "Compiled CSS and JS"
		
	ir a https://jquery.com/download/
		"Download the compressed, production jQuery 3.5.1"
		
	ir a https://popper.js.org/
		descargar popper.min.js
		
	Copiar todo en portfolio-project\jobs\static NO copiar en 	portfolio-project\static directamente	
	
	Ejecutar
		python manage.py collectstatic		
		
	Editar home.html
		Cambiar https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css
		por 
			 <link rel="stylesheet" href="{% static 'css/bootstrap.min.css' %}">
			 
		cambiar
			<script src="https://code.jquery.com/jquery-3.5.1.slim.min.js" integrity="sha384-DfXdz2htPH0lsSSs5nCTpuj/zy4C+OGpamoFVy38MVBnE+IbbVYUew+OrCXaRkfj" crossorigin="anonymous"></script>
			<script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.1/dist/umd/popper.min.js" integrity="sha384-9/reFTGAW83EW2RDu2S0VKaIzap3H66lZH81PoYlFhbGU+6BZp6G7niu735Sk7lN" crossorigin="anonymous"></script>
			<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/js/bootstrap.min.js" integrity="sha384-B4gt1jrGC7Jh4AgTPSdUtOBvfO8shuf57BaghqFfPlYxofvL8/KUEfYiJOMMV+rV" crossorigin="anonymous"></script>	
		por
			<script src="{% static 'jquery-3.5.1.min.js' %}"></script>
			<script src="{% static 'popper.min.js' %}"></script>
			<script src="{% static 'js/bootstrap.min.js' %}"></script>		
			
		      <div class="row">


		Dejar dinamico los trabajos

			{% for job in jobs.all %}
			<div class="col-md-4">
			  <div class="card mb-4 shadow-sm">
				<svg class="bd-placeholder-img card-img-top" width="100%" height="225" xmlns="http://www.w3.org/2000/svg" preserveAspectRatio="xMidYMid slice" focusable="false" role="img" aria-label="Placeholder: Thumbnail"><title>Placeholder</title><rect width="100%" height="100%" fill="#55595c"/><text x="50%" y="50%" fill="#eceeef" dy=".3em">Thumbnail</text></svg>
				<img class="card-img-top" src="">

				<div class="card-body">
				  <p class="card-text">{{ job.summary }}</p>
				  <div class="d-flex justify-content-between align-items-center">
				  </div>
				</div>
			  </div>
			</div>
			{% endfor %}
			</div>
			
		Editar settings.py agregar al final
			MEDIA_URL = '/media/'
			MEDIA_ROOT = BASE_DIR
			
		Editar urls.py
			urlpatterns = [
				path('admin/', admin.site.urls),
				path('', jobs.views.home, name = 'home'),
			] 
			urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT) 
			urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT) 		
			
		Editar home.html
		      <div class="row">
			{% for job in jobs.all %}
			<div class="col-md-4">
			  <div class="card mb-4 shadow-sm">
				<img class="card-img-top" src="{{ job.image.url}}">
				<div class="card-body">
				  <p class="card-text">{{ job.summary }}</p>
				  <div class="d-flex justify-content-between align-items-center">
				  </div>
				</div>
			  </div>
			</div>
			{% endfor %}
		  </div>
	
	  
			
14- Mostrar información preliminar de los Jobs en admin
	Abrir models.py, agregar el metodo __str__
		
		class Job(models.Model):
			image = models.ImageField(upload_to='images/')
			summary = models.CharField(max_length=200)

			def __str__(self):
				return self.summary	
			 
15- Editar urls.py adicionar un nuevo path para poder adicionar páginas de detalle para los trabajos
	
	urlpatterns = [
    path('admin/', admin.site.urls),
    path('', jobs.views.home, name = 'home'),
    path('jobs/<int:job_id>',jobs.views.detail, name='detail'),
	] 
	urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT) 
	urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT) 

16- Adicionar las páginas de detalle de los Jobs
		prueba preliminar, agregar en views.py
			def detail(request, job_id):
				print(job_id)
				return render(request, 'jobs/home.html')
				
		crear en la app template/jobs
			detail.html
			agregar 
				{{ job.summary }}
			copiar todo lo de home en detail.html y editar lo relevante es:
				<main role="main">
				<section class="jumbotron text-center">
				<div class="container">
				  <h1>Job detail</h1>
				  <p class="lead text-muted">{{ job.summary }}</p>
				  <img src="{{ job.image.url }}", height="200"></img>
				  <p>
					<a href="{% url 'home'%}" class="btn btn-primary my-2">back</a>
				  </p>

				</div>
				</section>
				</main>
			
			
		editar en views
			from django.shortcuts import render, get_object_or_404
			
			def detail(request, job_id):
				job_detail = get_object_or_404(Job, pk=job_id)
				return render(request, 'jobs/detail.html',{'job':job_detail})
		
		
		se usan {{ }} sin porcentaje para las variables que vienen como parametro en el html, para el resto se usa {% %} 

	
17-Agregar los links de cada Job al home	
	    {% for job in jobs.all %}
        <div class="col-md-4">
          <a href="{% url 'detail' job.id%}">
          <div class="card mb-4 shadow-sm">
            <img class="card-img-top" src="{{ job.image.url}}">
            <div class="card-body">
              <p class="card-text">{{ job.summary }}</p>
              <div class="d-flex justify-content-between align-items-center">
              </div>
            </div>
          </div>
          </a>  
        </div>
        {% endfor %}

18-Desplegarlo en producción
	Digital Ocean
	AWS
	Heroku
	Python Anywhere

	
	
	
	
		
		
	
	
	
	
	
	

	

	
	
	
	
	
	
	
	
	
	
	
	
	

	
	
	
	
	



		
	
	
	
	





	
	
	




