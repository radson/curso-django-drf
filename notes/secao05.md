# Seção 05 - Django URLs, Views e Templates

## Aula 19. Usando o VS Code ao nosso favor para produtividade com o Django

Criar um arquivo ```.vscode/launch.json``` que irá proporcionar a execução do servidor do Django integrado ao VSCode.

```Json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Python: Django",
            "type": "python",
            "request": "launch",
            "program": "${workspaceFolder}/projeto1/manage.py",
            "args": [
                "runserver"
            ],
            "django": true,
            "justMyCode": true
        }
    ]
}
```

## Aula 20. introdução às URLs do Django e Django Views

No arquivo ```urls.py``` do projeto ficam as rotas para todas as páginas a serem acessadas no projeto.

A função ```path```, é que recebe o nome da URL e o destino (um método na view). 

Executando um 'Hello World'. Criando uma URL ```sobre/``` que retorna um HttpResponse que a string 'Olá mundo'.

```Python
from django.contrib import admin
from django.http import HttpResponse
from django.urls import path

def my_view(request):
    return HttpResponse("ola mundo")

urlpatterns = [
    path('admin/', admin.site.urls),
    path('sobre/', my_view),
]
```

Executando o servidor http do Django, acessando a url ```http://127.0.0.1:8000/sobre```, será exibida a string configurada.
Este exemplo implementou um método como view dentro do urls.py apenas para simplicificar o exemplo, mas esta não é maneira formal de implementação, que será abordada futuramente.

## Aula 21. Uma visão geral sobre o protocolo HTTP, Request e Response, Status e Method

Material complementar:
* [http status code](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Status)
* [metodos http](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Methods)
* [request e response](https://www.webnots.com/what-is-http/)

## Aula 22. Criando, conhecendo e entendendo apps do Django

### Objetivo

O Django permite que uma solução seja organizada em apps. Para este projeto será criado um site de receitas culinárias e serão mapeados os apps necessários.

### Etapas

Iniciar um app com nome ```recipes``` usando ```manager.py```

```Shell
python manage.py startapp recipes
```
Após executar este comando, um diretório referente ao app é criado com a seguinte estrutura de arquivos do Django.

```Shell
recipes
├── admin.py
├── apps.py
├── __init__.py
├── migrations
│   └── __init__.py
├── models.py
├── tests.py
└── views.py
```

## Aula 23. Movendo o código para o app recipes

### Objetivo

Iniciar a criação das views necessárias para a app *recipes*

### Etapas

Dentro de ```recipes/views.py``` adicionar algumas views básicas que retornar uma string

```Python
from django.http import HttpResponse

def home(request):
    return HttpResponse('HOME')

def contato(request):
    return HttpResponse("Contato")

def sobre(request):
    return HttpResponse("Sobre")
```

Criar um arquivo de rotas para a app *recipes* em ```recipes/urls.py``` com o seguinte conteúdo.

```Python
from django.urls import path

from .views import contato, home, sobre

urlpatterns = [
    path('', home),
    path('contato/', contato),
    path('sobre/', sobre),
]
```

No arquivo de rotas do projeto incluir um mapeamento para as rotas do app usando o ```include```.

```Python
from django.urls import include, path

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('recipes.urls')),
]
```

## Aula 24. Templates e renderização de HTML no Django

### Objetivo

Para correta exibição do HTML é necessário utilizar os recursos de *templates* do Django. 

### Etapas

No diretório do app *recipes* criar o diretório que irá conter os templates. O uso de um subdiretório com o nome do app é para criar um namespace e evitar conflito com outros arquivos de mesmo nome.

```Shell
recipes/
└── templates
    └── recipes
        └── home.html
```

No arquivo html incluir o conteúdo

```Html
<!DOCTYPE html>
<html lang="pt-BR">
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Recipes</title>
    </head>
    <body>
        <h1>Recipes</h1>
    </body>
</html>
```

Para que a app *recipes* esteja disponível no projeto, deve ser configurada no arquivo ```settings.py``` na lista de  ```INSTALLED_APPS```, incluir:

```Python
INSTALLED_APPS = [
    # outros apps padrão instalados
    'recipes',
]
```

No arquivo views.py alterar a view home para o template criado. O Django por padrão busca dentro do diretório templates de cada app. Neste exemplo é especificado o namespace *recipes* para identificar qual templates deve-se usar.

```Python
from django.shortcuts import render

def home(request):
    return render(request, 'recipes/home.html')
```

O Django permite configurar uma estrutura personalizade de templates, para esta aplicação será incluída uma estrutura a ser compartilhada entre diversos apps, o Django permite configurar no settings na lista ```TEMPLATES```

```Python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [
            BASE_DIR / 'base_templates',
        ],
        # suprimido texto de outras configs
    }
]
```

No diretório raiz do projeto, adicionar o diretório correspondente onde ficarão os templates conforme estrutura a seguir. Para este exemplo será usado o namespace *global*.

```Shell
projeto1/
├── base_templates
│   ├── global
│       └── base.html
└── recipes
│   ├── templates
│   │   └── recipes
│   │       └── home.html
```

## 25. A função render e mais sobre caminhos de templates

### Objetivos

Explorando o método ```render``` e os parâmetros que recebe.

### Etapas

Conforme documentação [render](https://docs.djangoproject.com/pt-br/3.2/topics/http/shortcuts/#render)

## 26. O que vamos criar neste projeto? Conheça o nosso site!

### Objetivos

Explicando o projeto, uma prévia

### Etapas

Serão tratados os recursos:

* Forms e validacoes;
* Criação de usuários e autenticação
* reCaptcha