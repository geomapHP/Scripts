>## Descrição do projeto

Seguindo as idéias do livro:

> ### __Cap.1 - Estruturação do projeto e configurações básicas.__

- __Preparação__:

Upgrade dos módulos Python compartilhados (__pip__, __setuptools__ e __wheel__):
  
    pip install --upgrade pip setuptools wheel

- __1° passo__: Criar o ambiente virtual, habilitá-lo e instalar o Django
  
A partir do diretório raiz (___myproject_website___\) crie o ambiente virtual com o comando
no shell:

    C:\myproject_website>py -m venv env

Será instalado o módulo Python ___env___\ (diretório)

Para habilitá-lo, execute o comando a partir do diretório ___myproject_website___\

    C:\myproject_website>.\env\scripts\activate

O ambiente virtual será ligado

    (env)C:\myproject_website>

Instale o Django:

    (env)C:\myproject_website>pip install Django

- __2° passo__: Criar o projeto e a estrutura de diretórios na pasta
  ___myproject_website___\\:


Crie essa estrutura de diretórios (__abaixo__)

__OBS__: `algumas pastas são módulos Python e devem
conter o arquivo __init__.py`

      myproject_website\
       |--commands\
       |--db_backups\
       |--env\
       |  |--pasta contendo ambiente virtual 
       |--mockups\
       |--src\
          |--django-myproject\
             |--externals\
             |    |--apps\
             |    |   |--README.md
             |    |--libs\
             |        |--README.md
             |--locale\
             |--media\
             |--myproject\
             |   |--apps\
             |   |   |--core\
             |   |   |   |--__init__.py
             |   |   |   |--versioning.py
             |   |   |--__init__.py
             |   |--settings\
             |   |   |--__init__.py  
             |   |   |--_base.py
             |   |   |--dev.py          
             |   |   |--production.py  
             |   |   |--samples_secrets.json
             |   |   |--secrets.json
             |   |   |--dev.py   
             |   |--site_static\
             |   |   |--site\  
             |   |     |--css
             |   |     |   |--style.css
             |   |     |--img
             |   |     |   |--favicon-16x16.png
             |   |     |   |--favicon-32x32.png
             |   |     |   |--favicon.ico
             |   |     |--js
             |   |     |   |--main.js
             |   |     |--scss
             |   |         |--style.css
             |   |--templates\
             |   |   |--base.html
             |   |   |--index.html
             |   |--__init__.py
             |   |--urls.py
             |   |--wsgi.py
             |--requirements\
             |   |--_base.txt
             |   |--dev.txt
             |   |--production.txt
             |   |--staging.txt
             |   |--test.txt
             |--static\
             |--LICENSE
             |--manage.py
             |--README.md

Na pasta ___src___\ crie o projeto com o comando abaixo:

    (env)C:\myproject_website\src>django-admin.py startproject myproject

Crie um arquivo __README.md__: (_este próprio arquivo_)

Modifique o nome do arquivo de ___myproject___\ para ___django-myproject___\,
conforme esquema acima.


- __3° passo__: Lidar com as dependências do projeto usando arquivos "_.txt_" para 
instalação pelo pip:

Cria o diretório ___requirements___\ na pasta ___django-myproject___\ para especificar os
pacotes que deverão ser instalados para cada ambiente.

  Criar arquivo ___requirements\ _ base.txt___:

    Django~=3.0.3
    psycopg2-binary==2.9.1
    pytz==2019.2
    sqlparse==0.3.0

  Criar arquivo ___requirements\dev.txt___:

    -r _base.txt
    coverage
    django-debug-toolbar
    selenium

  Criar arquivo ___requirements\production.txt___:

    -r _base.txt
    coverage


Executar o comando para instalar os pacotes

    (env)C:\...\src\django-myproject>pip install -r requirements\dev.txt   

- __4° passo__: Configurações distintas para diversos ambientes na pasta "___settings___\\" 
  
A pasta "___settings___\" deve ter um arquivo __" __ init __ .py"__ (vazio) que vai tornar a pasta um módulo Python.

| __Ambiente__        | ___Nome do arquivo___ |  _obs_                 |
|---------------------|-----------------------|-------------------------|
| __Arquivo base__    |  ____base.py___       |_Arquivo orginal ___settings.py___ copiado_ *|
| __Desenvolvimento__ |  ___dev.py___         |                         |
| __Testes__          |  ___test.py___        |                         |
| __Staging__         |  ___staging.py___     |                         |
| __Produção__        |  ___production.py___  |___Padrão default **___  |

_*_ Após copiar o arquivo "___settings.py___" original e colar em ___settings\ _ base.py___, apague o arquivo original.  

_**_ O padrão default é estabelecido em ___manage.py___ e ___wsgi.py___, conforme quadro abaixo pela acresção de ".production" no final da ___string___.

        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'myproject.settings.production')

Modificações em ___settings\ _ base.py___ (arquivo copiado de __django-myproject\myproject\setting.py__):

    Antes:
    BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))

    Depois:
    BASE_DIR = os.path.dirname(
        os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
    )

Criar arquivo ___settings\production.py___: 

    from ._base import *

Criar arquivo ___settings\dev.py___: 

    from ._base import *
    EMAIL_BACKEND = "django.core.mail.backends.console.EmailBackend"


- __5° passo__: Definir __paths__ relativos nas configurações:

Modificações em ___settings\ _ base.py___):

    TEMPLATE = [{
    # ...
       'DIRS': [
           os.path.join(BASE_DIR, 'myproject', 'templates'),
       ],
    }]
    # ...
    LOCALE_PATHS = [
        os.path.join(BASE_DIR, 'locale'),
    ]
    # ...
    STATICFILES_DIRS = [
        os.path.join(BASE_DIR, 'myproject', 'site_static'),
    ]
    STATIC_URL = '/static/'
    STATIC_ROOT = os.path.join(BASE_DIR, 'static')
    MEDIA_URL = '/media/'
    MEDIA_ROOT = os.path.join(BASE_DIR, 'media')

- __6° passo__: Configurações sigilosas.

`OBS: Essa etapa causou diversos erros por causa do banco de dados, NÃO IMPLEMENTAR`

###### A partir de variáveis de ambiente:

Modificar o arquivo __settings\ _ base.py__ acrescentando a função ___get_secret()___:

    from django.core.exceptions import ImproperlyConfigured
    
    def get_secret(setting):
    """Get the secret variable or return explicit exception."""
    try:
        return secrets[setting]
    except KeyError:
        error_msg = f"Set the {setting} secret variable"
        raise ImproperlyConfigured(error_msg)

Outras mudanças (__SECRET_KEY__ e __DATABASES__)

    SECRET_KEY = get_secret("DJANGO_SECRET_KEY")
    
    DATABASES = {
        "default": {
            "ENGINE": "django.db.backends.postgresql_psycopg2",
            "NAME": get_secret("DATABASE_NAME"),
            "USER": get_secret("DATABASE_USER"),
            "PASSWORD": get_secret("DATABASE_PASSWORD"),
            "HOST": "db",
            "PORT": "5432",
        }
    }

###### A partir de arquivos-texto (com extensão YAML, INI, CSV ou JSON):

Modificar o arquivo __settings\ _ base.py__ acrescentando:

    import json

    with open(os.path.join(os.path.dirname(__file__), 'secrets.json'), 'r') as f:
        secrets = json.loads(f.read())
    
Criar arquivo ___settings\secrets.json___: 

    {
        "DJANGO_SECRET_KEY": "change-this-to-50-characters-long-random-string",
        "DATABASE_NAME": "myproject",
        "DATABASE_USER": "myproject",
        "DATABASE_PASSWORD": "senha qualquer",
    }

- __7° passo__: Incluir dependências externas ao projeto

No diretório ___externals___\ crie as pastas ___externals\apps___\ e ___externals\libs___\,
ambos com arquivos __README.md__.

Modificar o arquivo __settings\ _ base.py__

    import sys

    EXTERNAL_BASE = os.path.join(BASE_DIR, "externals")
    EXTERNAL_LIBS_PATH = os.path.join(EXTERNAL_BASE, "libs")
    EXTERNAL_APPS_PATH = os.path.join(EXTERNAL_BASE, "apps")
    sys.path = ["", EXTERNAL_LIBS_PATH, EXTERNAL_APPS_PATH] + sys.path

- __8° passo__: Configurar o STATIC_URL dinamicamente para os usuários do Git:

Crie um aplicação em ___django-myproject\myproject\apps___\ de nome __core__

    (env)C:\django-myproject\myproject\apps>    














