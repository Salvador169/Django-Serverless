# SPD Cloud
> a64583, a64586, a64587, a64590

# Primeiros passos para projeto em DJANGO
## Criar ambiente virtual e instalar o django
https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create-deploy-python-django.html
- Criar ambiente virtual 
```virtualenv eb-virt```
- Ativar ambiente virtual
``` eb-virt\Scripts\activate```
- Instalar django
```pip install django```
- Verificar se django ficou corretamete instalado
```pip freeze```

## Criar projeto no django
1. Verificar que o ambiente virtual está ativado
2. Criar projeto, neste caso demos o nome de *SPDCloud*
```django-admin startproject SPDCloud```
3. Entrar na pasta e correr o servidor local
```python manage.py runserver```
4. Terminar servidor ```Ctrl+C```


# CLOUD COMPUTING
## AWS
### Requisitos
1. Primeiro devemos criar conta na plataforma, seguindo os seguintes passos:
- https://docs.aws.amazon.com/cloud9/latest/user-guide/setup-student.html
Vá para o site de login do AWS Educate, em https://www.awseducate.com/signin/.

- Insira o endereço de e-mail e a senha que você usou para fazer login no Portal de aluno do AWS Educate e escolha Sign In (Fazer login).

- Selecione Conta da AWS no banner de navegação superior.

- Selecione Conta do AWS Educate Starter


### Configurar aplicação Django para *Elastic Beanstalk*
1. Verificar que o ambiente virtual está ativado
2. Guardar as aplicações que estão instaladas no ambiente virtual num ficheiro *requirements.txt* para ser instaladas mais tarde no servidor
```pip freeze > requirements.txt```
3. Criar diretoria onde serão guardados os Scripts para configuração do servidor
```mkdir .ebextensions```
4. Criar ficheiro na diretoria ```~/spd-grupo2a/.ebextensions/django.config``` com o seguinte informação: (**Atenção:** O *WSGIPath* deverá estar correto)
```
option_settings:
  aws:elasticbeanstalk:container:python:
    WSGIPath: spd-grupo2a.wsgi:application
```
5. Desativar o ambiente virtual
```deactivate```


### Deploy do site usando *EB CLI*
1. Criar o ambiente e fazer *deploy* da aplicação Django
```eb init ``
- Escolher regiao onde o servidor estará hospedado. No nosso caso escolhemos 16 *16) eu-west-2 : EU (London)*, por ser mais próximo de nós.
- Obter as credenciais de acesso. Aceder a *IAM* -> Users -> AddUser 
(aws-access-id)
- 
2. Criar um ambiente, neste caso criamos um ambiente de produção
```eb create spd-grupo2a-Production```
3. Verificar o estado do ambiente criado
```eb status``` ou ```eb open```
4. Caso tenhamos feito alguma atualização do código, devemos fazer **deploy**
```eb deploy```

```eb ssh``` conexão ao servidor

#### DEPLOYED URL

* **SITE**: http://spdgrupo2a-env.eba-uruptyfp.us-east-1.elasticbeanstalk.com/


>##  Azure
Usamos os seguintes tutoriais:
https://www.youtube.com/watch?v=0wWjGV3zva4&ab_channel=GLUGMVIT
### Requisitos
- Tenha uma conta Azure com uma subscrição ativa. Crie uma conta gratuita.
- Instale python 3.6 ou superior. ```py -3 --version```
- Instale o Azure CLI 2.18.0 ou superior, com o qual execute comandos em qualquer concha para provisões e configurar recursos Azure. ```az --version ``` e depois fazer login ```az login```

### Deploy do site usando Azure
1. Deploy para servidor, no nossa fizemos para spdcloud como app-name
```az webapp up --sku B1 --name <app-name>```
2. Abrir a app
```http://<app-name>.azurewebsites.net```

#### DEPLOYED URL
* **SITE**: https://spd-grupo2a.azurewebsites.net/

> ## Google Cloud Platform
Usamos o seguinte tutorial:
https://bennettgarner.medium.com/deploying-a-django-application-to-google-app-engine-f9c91a30bd35

### Requisitos
- conta na *Google Cloud Platform* 
- novo projeto na *Google Cloud dashboard*
- Billing ativad para conta GCP 
- instalar Google Cloud SDK 
- APIs ativadas para o projeto


### Criar aplicação
1. Correr comando ```gcloud app create --project=spd-grupo2a```
2. Escolher onde queremos o servidor, no nosso caso escolhemos o 11 (europe-west)

### Adicionar ficheiros e requisitos à aplicação
1. Na diretoria ```/app.yaml```
```yaml
# [START django_app]
runtime: python37
handlers:
# This configures Google App Engine to serve the files in the app's
# static directory.
- url: /static
  static_dir: static/
# This handler routes all requests not caught above to the main app. 
# It is required when static routes are defined, but can be omitted 
# (along with the entire handlers section) when there are no static 
# files defined.
- url: /.*
  script: auto
# [END django_app]
```

2. Na diretoria ```/main.py```
```py
from SPDCloud.wsgi import application
# App Engine by default looks for a main.py file at the root of the app
# directory with a WSGI-compatible object called app.
# This file imports the WSGI-compatible object of the Django app,
# application from mysite/wsgi.py and renames it app so it is
# discoverable by App Engine without additional configuration.
# Alternatively, you can add a custom entrypoint field in your app.yaml:
# entrypoint: gunicorn -b :$PORT mysite.wsgi
app = application 
```

3. Na diretoria principal do ambiente correr o comando
- ```pip freeze > requirements.txt```

4. Na diretoria ```/mysite/settings.py```
```py
# Static files (CSS, JavaScript, Images)
# https://docs.djangoproject.com/en/2.1/howto/static-files/
# Google App Engine: set static root for local static files
# https://cloud.google.com/appengine/docs/flexible/python/serving-static-files
STATIC_URL = '/static/'
STATIC_ROOT = 'static'
```

5. Na diretoria ```/mysite/wsgi.py``` (deverá já estar por defeito)
```py
"""
WSGI config for mysite project.
It exposes the WSGI callable as a module-level variable named ``application``.
For more information on this file, see
https://docs.djangoproject.com/en/2.1/howto/deployment/wsgi/
"""
import os
from django.core.wsgi import get_wsgi_application
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'mysite.settings')
application = get_wsgi_application()
```

### Fazer deploy para GCP
- Correr o comando ```gcloud app deploy```

#### DEPLOYED URL
* **SITE**: https://spd-grupo2a.ew.r.appspot.com/
