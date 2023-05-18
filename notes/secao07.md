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