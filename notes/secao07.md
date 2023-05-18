# Seção 7: Django Templates: herança, blocos, if, for e mais

## 42. Uma nova URL para uma nova View - sobre URLs no Django

### Objetivos

* Construir a página de uma receita

### Etapas

Uma boa prática para quando aumenta o número de views para importar, pode-se mudar a forma de importar usando o arquivo views e referenciado cada view pelo namespace. Como no exemplo a seguir.

```Python
from . import views

urlpatterns = [
    path('', views.home),
]
```

Para criar uma nova view que permita exibir uma única receita, será necessário definir no `url` do projeto recipes recebendo um parametro (um ID) que identifique uma receita. O Django possui um recurso que permite converter os parametros da URL em tipos de dados [URL Converter](https://docs.djangoproject.com/pt-br/3.2/topics/http/urls/#path-converters).

```Python
urlpatterns = [
    path('recipes/<int:id>/', views.recipe),
]
```

No arquivo `views` deve-se mapear o parâmetro na respectiva view.

```Python
def recipe(request, id):
    return render(request, 'recipes/pages/home.html')
```

## 43. Separando os templates de home e recipe

### Objetivos

* Criar um novo template para a recipe

### Etapas

Criar um novo template com base no `recipes/pages/home.html` no mesmo diretório com nome `recipe-view.html`. Alterar a classe do container para `main-content-detail`. Remover outros cards de recipe e deixar apenas um.

```Django
{% include 'recipes/partials/head.html' %}

    <body>
        {% include 'recipes/partials/header.html' %}

        {% include 'recipes/partials/search.html' %}

        <main class="main-content-container">   
            <div class="main-content main-content-detail container">
                {% include 'recipes/partials/recipe.html' %}

            </div>
        </main>

        {% include 'recipes/partials/footer.html' %}
        
    </body>
</html>
```

Alterar o `style.css` para centralizar o card e aumentar a largura. Para isso adicionar o estilo para a class `.recipe-list-item` que for filha de `.main-content-detail`.

```Css
.main-content-detail .recipe-list-item {
    max-width: 84rem;
    margin: 0 auto;
}

.main-content-detail .recipe-list-item:hover {
    transform: scale(1);
    box-shadow: -5px 5px 15px rgba(0, 0, 0, 0.2);
}
```

Em ```views.py``` alterar o nome do template para o novo criado `recipe-view.html`.

```Python
def recipe(request, id):
    return render(request, 'recipes/pages/recipe-view.html')
```

## 44. Herança em templates e template blocks

### Objetivos

* Reutilização de código nos templates, criando uma base que será reutilizada nos demais templates.

### Etapas

Usando o conceito de [herança de templates](https://docs.djangoproject.com/pt-br/3.2/ref/templates/language/#template-inheritance) do Django, copiar o conteúdo do template `recipes/pages/home.html` para o arquivo `base_templates/global/base.html`, onde será definido um bloco `content` que poderá ser expandido pelos templates que o referenciarem, conforme a seguir:

```Django
{% include 'recipes/partials/head.html' %}

    <body>
        {% include 'recipes/partials/header.html' %}

        {% include 'recipes/partials/search.html' %}

        <main class="main-content-container">
            {% block content %}
            
            {% endblock content %}
        </main>

        {% include 'recipes/partials/footer.html' %}

    </body>
</html>
```

O arquivo `recipes/pages/home.html` terá apenas o conteúdo que não é comum ao `base.html`.

```Django
{% extends 'global/base.html' %}

{% block content %}
    <div class="main-content main-content-list container">
        {% include 'recipes/partials/recipe.html' %}
        {% include 'recipes/partials/recipe.html' %}
        {% include 'recipes/partials/recipe.html' %}
        {% include 'recipes/partials/recipe.html' %}
        {% include 'recipes/partials/recipe.html' %}
        {% include 'recipes/partials/recipe.html' %}
        {% include 'recipes/partials/recipe.html' %}
        {% include 'recipes/partials/recipe.html' %}
        {% include 'recipes/partials/recipe.html' %}
    </div>
{% endblock content %}
```

O arquivo `recipes/pages/recipe-view.html` seguirá o mesmo modelo.

```Django
{% extends 'global/base.html' %}

{% block content %}
    <div class="main-content main-content-detail container">
        {% include 'recipes/partials/recipe.html' %}

    </div>
{% endblock content %}
```

## 45. Criando o block para o título da página

### Objetivos

* Aplicando o conceito de herança para o título da página.

### Etapas

Para atingir o objetivo, será necessário um ajuste no template `recipe/partials/head.html` removendo as tags `html`, `head` e `title` que ficarão em no template `base.html`. O conteúdo do `head.html` ficará assim:

```Html
{% load static %}

<meta charset="UTF-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/fontawesome.min.css"
    integrity="sha512-SgaqKKxJDQ/tAUAAXzvxZz33rmn7leYDYfBP+YoMRSENhf3zJyx3SBASt/OfeQwBHA1nxMis7mM3EV/oYT6Fdw=="
    crossorigin="anonymous" referrerpolicy="no-referrer" />
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/brands.min.css"
    integrity="sha512-9YHSK59/rjvhtDcY/b+4rdnl0V4LPDWdkKceBl8ZLF5TB6745ml1AfluEU6dFWqwDw9lPvnauxFgpKvJqp7jiQ=="
    crossorigin="anonymous" referrerpolicy="no-referrer" />
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/solid.min.css"
    integrity="sha512-yDUXOUWwbHH4ggxueDnC5vJv4tmfySpVdIcN1LksGZi8W8EVZv4uKGrQc0pVf66zS7LDhFJM7Zdeow1sw1/8Jw=="
    crossorigin="anonymous" referrerpolicy="no-referrer" />
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Roboto+Slab:wght@900&display=swap" rel="stylesheet">

<link rel="stylesheet" href={% static 'recipes/css/styles.css' %}>
```

As tags removidas serão posicionadas no início do template `base.html` antes da tag `body`:

```Html
<!DOCTYPE html>
<html lang="pt-BR">
    <head>
        {% include 'recipes/partials/head.html' %}
        <title>{% block title %}{% endblock title %} Recipes</title>
    </head>

    <body>
    <!-- Código sem alteração foi omitido -->
```

Usando o conceito de herança, o template `home.html` pode inserir qualquer texto dentro do bloco, que será contatenado com o título definido no bloco em `base.html`

```Django
{% extends 'global/base.html' %}

{% block title %}Home |{% endblock title %}

{% block content %}

<!-- Código sem alteração foi omitido -->
```

O arquivo `recipes/pages/recipe-view.html` seguirá o mesmo modelo (usando uma string temporária até ser implementado uma forma dinâmica de buscar o título da receita.)

```Django
{% extends 'global/base.html' %}

{% block title %}ALTERAR |{% endblock title %}

{% block content %}
```