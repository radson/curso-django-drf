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

## 27. Adicionando font-awesome no template

### Objetivos

* Criar a estrutura inicial para o template sem uso dos recursos de arquivos estáticos.
* Remover as views de *sobre* e *contato*.
* Adicionar CDN da font-awesome

### Etapas

Em ```recipe/views.py``` excluir as view para sobre e contato. Em 

```Python
from django.http import HttpResponse
from django.shortcuts import render

def home(request):
    return render(request, 'recipes/home.html')
```

Em ```recipe/urls.py``` remover as rotas.

```Python
from .views import home

urlpatterns = [
    path('', home),
]
```

## 28. Criando a header, separando um "partial" para head e usando include no template

### Objetivos

Iniciar a criação da estrutura do site separando em arquivos parciais e usando a diretiva include do sistema de templates do Django.

### Etapas

No app ```recipes``` no diretório de templates, atualizar a estrutura para ficar como a seguir, reaproveitando o arquivo ```home.html``` anteriormente criado.

```
templates
└── recipes
    ├── pages
    │   └── home.html
    └── partials
        └── head.html
``` 

Em ```partials``` irão ficar os trechos de html que serão compartilhadas com outras páginas dessa aplicação. Nesta aula será criado o ```head.html``` que conterá o trecho ```<head>``` recortado do arquivo ```home.html```. 

Arquivo ```views.py``` atualizando o caminho do template.

```Python
def home(request):
    return render(request, 'recipes/pages/home.html')
``` 

Arquivo ```head.html```

```Html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/fontawesome.min.css" integrity="sha512-SgaqKKxJDQ/tAUAAXzvxZz33rmn7leYDYfBP+YoMRSENhf3zJyx3SBASt/OfeQwBHA1nxMis7mM3EV/oYT6Fdw==" crossorigin="anonymous" referrerpolicy="no-referrer" />
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/brands.min.css" integrity="sha512-9YHSK59/rjvhtDcY/b+4rdnl0V4LPDWdkKceBl8ZLF5TB6745ml1AfluEU6dFWqwDw9lPvnauxFgpKvJqp7jiQ==" crossorigin="anonymous" referrerpolicy="no-referrer" />
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/solid.min.css" integrity="sha512-yDUXOUWwbHH4ggxueDnC5vJv4tmfySpVdIcN1LksGZi8W8EVZv4uKGrQc0pVf66zS7LDhFJM7Zdeow1sw1/8Jw==" crossorigin="anonymous" referrerpolicy="no-referrer" />

    <title>Recipes</title>

    <style>
        * {
            padding: 0;
            margin: 0;
            box-sizing: border-box;
        }

        html {
            font-size: 62.5%;
        }

        body{
            font-size: 1.6rem;
        }
    </style>
</head>
```

No arquivo ```home.html```, será usada a diretiva ```include``` para que o Django insira o conteúdo do ```head.html``` no arquivo ```home.html```.

```Html
{% include 'recipes/partials/head.html' %}

<body>
    <header class="main-header-container">
        <div class="main-header container">
            <h1>
                <a href="/">
                    <i class="fa-solid fa-house"></i>  
                    <span>Recipes {{name}}</span>
                </a>
            </h1>
        </div>
    </header>
</body>
</html>
```

## 29. Ajustando o estilo CSS da Header

### Objetivos

Aplicar um layout e estilo para o Header

### Etapas

No arquivo ```head.html``` incluir referencias para a fonte *Roboto Slab* do Google, após as referencias do Font-awesome

```Html
 <!-- Omitido código sem alteração -->

<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Roboto+Slab:wght@900&display=swap" rel="stylesheet">
```

Ainda no arquivo ```head.html``` na seção  ```<style>```, incluir definições de algumas variáveis e novas classes de CSS

```Html
<style>
    :root {
        --color-primary: #269fe6;
        --color-primary-hover: #2086c2;
        --color-primary-dark: #13141f;
        --color-primary-dark-hover: #212336;
        --color-primary-light: #d4ecfa;
        --color-primary-light-hover: #bdd8e7;

        --color-white: #fff;
        --color-black: #000;

        --color-dark-text: #444;
        --color-info-light: #cce5ff;
        --color-debug-light: #cce5ff;
        --color-success-light: #d4edda;
        --color-alert-light: #fff3cd;
        --color-warning-light: #fff3cd;
        --color-error-light: #f8d7da;

        --color-info-dark: #4d86c4;
        --color-debug-dark: #4d86c4;
        --color-success-dark: #4a9c5d;
        --color-alert-dark: #927f40;
        --color-warning-dark: #927f40;
        --color-error-dark: #da525d;

        --color-gray-0: #f9f9f9;
        --color-gray-1: #e0e0e0;
        --color-gray-2: #c7c7c7;
        --color-gray-3: #aeaeae;
        --color-gray-4: #959595;
        --color-gray-5: #7d7d7d;
        --color-gray-6: #646464;
        --color-gray-7: #4b4b4b;
        --color-gray-8: #323232;
        --color-gray-9: #191919;

        --font-primary: sans-serif;
        --font-headings: 'Roboto Slab', serif;

        --spacing-gutter-medium: 3rem;
        --spacing-gutter-large: 4rem;
    }

    * {
        padding: 0;
        margin: 0;
        box-sizing: border-box;
    }

    html {
        font-size: 62.5%;
    }

    body{
        font-size: 1.6rem;
        font-family: var(--font-primary);
    }

    h1,h2,h3,h4,h5,h6 {
        font-family: var(--font-headings);
    }

    .container {
        max-width: 144rem;
        margin: 0 auto;
        padding: var(--spacing-gutter-medium);
    }

    .main-header-container { 
        background: var(--color-primary-dark);
    }

    .main-logo {
        font-size: 4rem;
        color: var(--color-white);
        text-decoration: none;
        display: flex;
        flex-flow:  row nowrap;
        align-items: center;
        justify-content: center;
        width: fit-content;
        margin: 0 auto;
    }

    .main-logo-icon {
        margin-right: 2rem;
    }
</style>
```

No arquivo ```home.html``` ajustar o conteúdo da tag ```<a>``` dentro de ```<h1>``` para corresponder às novas definições de CSS.

```Html
<a class="main-logo" href="/">
    <i class="fa-solid fa-utensils main-logo-icon"></i>  
    <span class="main-logo-text">Recipes</span>
</a>
```

## 30. Criando a área de busca com form, input e button (search)

### Objetivos

Criar um input de busca abaixo do header

### Etapas

No arquivo ```home.html``` recortar o conteúdo dentro da tag ```<header>``` e colar em um novo aquivo com nome ```templates/partials/header.html```. 

```Html
<header class="main-header-container">
    <div class="main-header container">
        <h1>
            <a class="main-logo" href="/">
                <i class="fa-solid fa-utensils main-logo-icon"></i>  
                <span class="main-logo-text">Recipes</span>
            </a>
        </h1>
    </div>
</header>
```

No arquivo ```home.html``` dentro da tag ```<body>``` usar o ```include``` referenciando o arquivo acima criado. Além disso criar um container de busca com o conteúdo abaixo.

```Html
<body>
    {% include 'recipes/partials/header.html' %}

    <div class="search-container">
        <div class="container">
            <form action="" class="search-form">
                <input type="search" class="search-input" name="search">
                <button type="submit" class="search-button"><i class="fa-solid fa-search"></i></button>
            </form>
        </div>
    </div>
</body>
```

No arquivo ```head.html``` alterar o backgrou para a class ```body``` e criar novas classes para os containers recem criados para o campo de busca.

```Html
<style>
    /*Omitido código sem alteração*/

    body{
            font-size: 1.6rem;
            font-family: var(--font-primary);
            background: var(--color-gray-1);
        }

    .search-form {
        border: .2rem solid var(--color-primary-dark);
        max-width: 64rem;
        margin: 0 auto;
        border-radius: .4rem;
        position: relative;
        transition: all 300ms ease-in-out;
    }

    .search-input,
    .search-button {
        border: none;
        background: none;
        outline: none;
        padding: 1rem;
        transition: all 300ms ease-in-out;
    }

    .search-button {
        position: absolute;
        top: 0;
        right: 0;
    }

    .search-button:focus {
        outline: 1px solid var(--color-primary);
        background: rgba(0, 0, 0, .05);
    }

    .search-input{
        width: 100%;
        padding-right: 4rem;
    }

    .search-form:focus-within {
        border-color: var(--color-primary);
        background-color: var(--color-white);
    }

    .search-form:focus-within .search-button {
        border-color: var(--color-primary);
    }
</style>
```

## 32. Criando a grid de conteúdo

### Objetivos

Criar a grid que vai exibir os cards com as receitas

### Etapas

Mover o conteúdo do container search para uma partials em ```templates/partials/search.html```

```Html
<div class="search-container">
    <div class="container">
        <form action="" class="search-form">
            <input type="search" class="search-input" name="search">
            <button type="submit" class="search-button"><i class="fa-solid fa-search"></i></button>
        </form>
    </div>
</div>
```

Alterar o ```home.html``` para fazer include do ```search.html``` e incluir novos containers referentes ao grid das receitas.

```Html
<!-- Omitido código sem alteração  -->
{% include 'recipes/partials/search.html' %}

<main class="main-content-container">   
    <div class="main-content main-content-list container">

    </div>
</main>
```

No arquivo ```head.html``` incluir novos estilos para o grid.

```Html
<style>
    /*Omitido código sem alteração*/

    .main-content {
        padding-top: 0;
    }

    .main-content-list {
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(340px, 1fr));
        gap: var(--spacing-gutter-large);
    }
</style>
```

## 33. Criando o card de receitas - Parte 1

### Objetivos

Criar o card da receita

### Etapas

No arquivo ```home.html``` criar os containers relativo aos cards das receitas. 


```Html
<!-- Omitido código sem alteração  -->

<div class="main-content main-content-list container">
    <div class="recipe recipe-list-item">
        <div class="recipe-cover">
            <img src="https://via.placeholder.com/1280x720.png/269fe6" alt="Temporário">
        </div>
    </div>
</div>
```

No arquivo ```head.html``` incluir novos estilos para o card.

```Html
<style>
    /*Omitido código sem alteração*/
    .recipe {
        background: var(--color-white);
        box-shadow: -5px 5px 10px rgba(0, 0, 0, .2);
        transform: scale(1);
        transition: all 300ms ease-in-out;
    }

    .recipe img {
        max-width: 100%;
    }

    .recipe-list-item {
        max-width: 64rem;
    }

    .recipe-list-item:hover {
        transform: scale(1.02);
    }
</style>
```