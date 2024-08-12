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

## 46. Injetando dados na lista da view Home

### Objetivos

* Inserindo informações da receita para aparecer nos cards

### Etapas

Para gerar dados fictícios das receitas e os metadados dos cards, será utilizado o pacote [Faker](https://pypi.org/project/Faker/), que deve ser instalado no venv:

```Bash
pip install faker
```

No diretório raiz do projeto (no mesmo nível onde está `manage.py`), será criado o diretório `utils` com um script que usando o Faker irá produzir as informaçoes de acordo com o modelo apresentado.

```Bash
mkdir -p utils/recipes
touch utils/recipes/factory.py
```

O ```factory.py``` deverá ter o conteúdo a seguir:

```Python
from random import randint
from faker import Faker

def rand_ratio():
    return randint(840, 900), randint(473, 573)

fake = Faker('pt_BR')

def make_recipe():
    return {
        'title': fake.sentence(nb_words=6),
        'description': fake.sentence(nb_words=12),
        'preparation_time': fake.random_number(digits=2, fix_len=True),
        'preparation_time_unit': 'Minutos',
        'servings': fake.random_number(digits=2, fix_len=True),
        'servings_unit': 'Porção',
        'preparation_steps': fake.text(3000),
        'created_at': fake.date_time(),
        'author': {
            'first_name': fake.first_name(),
            'last_name': fake.last_name(),
        },
        'category': {
            'name': fake.word()
        },
        'cover': {
            'url': 'https://loremflickr.com/%s/%s/food,cook' % rand_ratio(),
        }
    }

if __name__ == '__main__':
    from pprint import pprint
    pprint(make_recipe())
```

Na views o `factory` será importado para fornecer os dados para os templates.

```Python
from utils.recipes.factory import make_recipe

def home(request):
    return render(request, 'recipes/pages/home.html', context={
        'recipes': [make_recipe() for _ in range(10)],
    })

def recipe(request, id):
    return render(request, 'recipes/pages/recipe-view.html', context={
        'recipe': make_recipe(),
    })
```

No template `home.html` o bloco content poderá iterar sobre as recipes que vieram no contexto

```Django
{% block content %}
    <div class="main-content main-content-list container">
        {% for recipe in recipes %}
            {% include 'recipes/partials/recipe.html' %}
        {% endfor %}
    </div>
{% endblock content %}
```

No template `partials/recipe.html` os dados estáticos devem ser substituídos pelos que serão fornecidos na variável de contexto, conforme o modelo definido no `factory.py`.

```Django
<div class="recipe recipe-list-item">
    <div class="recipe-cover">
        <img src="{{ recipe.cover.url }}" alt="Temporário">
    </div>
    <div class="recipe-title-container">
        <h2 class="recipe-title">{{ recipe.title }}</h2>
    </div>
    <div class="recipe-author">
        <span class="recipe-author-item">
            <i class="fa-solid fa-user"></i>
            {{ recipe.author.first_name }} {{ recipe.author.last_name }}
        </span>
        <span class="recipe-author-item">
            <i class="fa-solid fa-calendar-alt"></i>
            {{ recipe.created_at|date:"d/m/Y"}} às {{ recipe.created_at|date:"H:i"}}
        </span>
        <span class="recipe-author-item">
            <a href="/recipes/category/cafe-da-manha/">
                <i class="fa-solid fa-layer-group"></i>
                <span>{{ recipe.category.name }}</span>
            </a>
        </span>
    </div>

    <div class="recipe-content">
        <p>{{ recipe.description}}</p>
    </div>

    <div class="recipe-meta-container">
        <div class="recipe-meta recipe-preparation">
            <h3 class="recipe-meta-title"><i class="fa-solid fa-stopwatch"></i> Preparo</h3>
            <div class="recipe-meta-text">
                {{ recipe.preparation_time }} {{ recipe.preparation_time_unit }}
            </div>
        </div>

        <div class="recipe-meta recipe-servings">
            <h3 class="recipe-meta-title"><i class="fa-solid fa-pizza-slice"></i> Porções</h3>
            <div class="recipe-meta-text">
                {{ recipe.servings }} {{ recipe.servings_unit }}
            </div>
        </div>
       
    </div>
    <footer class="recipe-footer">
        <a href="" class="recipe-read-more button button-dark button-full-width">
            <i class="fa-solid fa-eye"></i>
            <span>ver mais...</span>
        </a>
    </footer>
</div>
```

## 47. Corrigindo a altura dos cards de recipes e adicionando a imagem

### Objetivos

* Corrigindo altura dos cards no CSS

### Etapas

Em `recipes/static/recipes/css/style.css` alterar o a classe `recipe-list-item`.

```Css
.recipe-list-item {
    max-width: 64rem;
    height: fit-content;
}
```

## 48. Trabalhando nas diferenças entre detail page e list page

### Objetivos

* Implementando um mecanismo que permita idenfiticar quando se está em cada uma das páginas.

### Etapas

No `views.py`, na view `recipe` adicionar uma variável no contexto `is_detail_page` com valor `True` que irá servir como flag de quando se está nesta página. Quando estiver na outra view, esta variável não irá existir mas não vai causar falha no funcionamento da página.

```Python
def recipe(request, id):
    return render(request, 'recipes/pages/recipe-view.html', context={
        'recipe': make_recipe(),
        'is_detail_page': True,
    })
```

No template `recipe.html`, a tag `footer` irá ficar dentro de uma estrutura de seleção `if` disponível na linguagem de templates do Django. Quando a variável `is_detail_page` estiver vazia ou não for `True` o botão para exibir mais informações irá aparecer, no caso da view `home`. Quando estiver na view `recipe` a variável irá permitir exibir o campo `preparation_steps`.

```Django
{% if is_detail_page is not True %}
    <footer class="recipe-footer">
        <a href="" class="recipe-read-more button button-dark button-full-width">
            <i class="fa-solid fa-eye"></i>
            <span>ver mais...</span>
        </a>
    </footer>
{% endif %}

{% if is_detail_page is True %}
    <div class="preparation-steps">
        {{ recipe.preparation_steps|linebreaksbr }}
    </div>
{% endif %}
```

Para melhor exibição do texto do `preparation_steps`, deve-se incluir a classe respectiva da div no arquivo de estilo.

```Css
.preparation-steps{
    padding: var(--spacing-gutter-medium);
}
```

## 49. Nome único nas URLs e app_name para Namespace

### Objetivos

* Implementando recurso de nomes para urls e uso de namespace.

### Etapas

No arquivo de rotas `recipes/url.py` adicionar a variável `app_name` que identifica o namespace e adicionar o parametro `name` com um nome único para cada url da lista.

```Python
app_name = 'recipes'

urlpatterns = [
    path('', views.home, name='home'),
    path('recipes/<int:id>/', views.recipe, name='recipe'),
]
```

No template `header.html` atualizar o link da classe `main-logo`, onde a tag url do Django recebe o namespace e o nome da view separados por `:`.

```Django
<a class="main-logo" href={% url 'recipes:home' %}>
```

No template `recipe.html` atualizar os links nas divs `recipe-cover`, `recipe-title-container` e no footer `recipe-footer`. Estes irão receber a URL e um parametro adicional que será a ID da receita.

```Django
<!-- omitido código sem alteração -->
<div class="recipe-cover">
    <a href={% url 'recipes:recipe' recipe.id %}>
        <img src="{{ recipe.cover.url }}" alt="Temporário">
    </a>
</div>
<div class="recipe-title-container">
    <h2 class="recipe-title">
        <a href={% url 'recipes:recipe' recipe.id %}>
            {{ recipe.title }}
        </a>
    </h2>
</div>

<!-- omitido código sem alteração -->

<footer class="recipe-footer">
    <a href={% url 'recipes:recipe' recipe.id %} class="recipe-read-more button button-dark button-full-width">
        <i class="fa-solid fa-eye"></i>
        <span>ver mais...</span>
    </a>
</footer>

<!-- omitido código sem alteração -->
```

Para que exista a ID nos links será necessário atualizar o `utils/recipes/factory.py` adicionando este campo no dicionário.

```Python
#omitido codigo sem alteração
def make_recipe():
    return {
        'id': fake.random_number(digits=2, fix_len=True),
#omitido codigo sem alteração
```

No arquivo de estilos, abaixo da class `recipe-title-container` adicionar uma formatação para quando houver um link.

```Css
/* omitido codigo sem alteracao */
.recipe-title-container a {
    text-decoration: none;
    color: var(--color-primary-dark);
}
/* omitido codigo sem alteracao */
```

## 50. Sobre Programação Orientada a Objetos (POO)

### Objetivos

* Introdução sobre POO e playlist de apoio.

### Etapas

A partir dessa parte do curso, muitos assuntos relacionados a POO (Programação Orientada a Objetos) serão usados. Isso porque o Django usa muito o conceito de classes. Models usam classes, Class Based Views, tem classe até no nome, forms também usam classes, além de várias outras partes que eu devo estar me esquecendo.

[Playlist sobre POO](https://youtube.com/playlist?list=PLbIBj8vQhvm34qAAEEH_PdL2tMG9rz-P7).

## 51. Primeiro Django Model e seus atributos

### Objetivos

* Explicando o models no Django.

### Etapas

No arquivo `recipe/models.py` são declaradas as classes que modelam as tabelas e colunas no banco de dados. O Django já fornece atritubos específicos para hedado de `models.Model` que são traduzidos para cada tipo de banco de dados. A modelam inicial para a classe `Recipe` fica como a seguir;

```Python
from django.db import models


class Recipe(models.Model):
    title = models.CharField(max_length=65)
    description = models.CharField(max_length=165)
    slug = models.SlugField()
    preparation_time = models.IntegerField()
    preparation_time_unit = models.CharField(max_length=65)
    servings = models.IntegerField()
    servings_unit = models.CharField(max_length=65)
    preparation_steps = models.TextField()
    preparation_steps_is_html = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    is_published = models.BooleanField(default=False)
    cover = models.ImageField(upload_to='recipes/covers/%Y/%m/%d/')
```

## 52. makemigrations e migrate - Aplicando as migrações

### Objetivos

* Continuando modelagem da aula anterior e incluindo recurso de relação entre classes (tabelas no banco de dados) e migrações.

### Etapas

Cada receita deve ter uma categoria, para representar essa relação, foi adicionada uma classe `Category` que será relacionada com a `Recipe` através do campo `ForeignKey`. 

Para o autor, utilizou-se a classe `User` que já existe no Django. 

```Python
#omitido codigo sem alteração
from django.contrib.auth.models import User

class Category(models.Model):
    name = models.CharField(max_length=65)


class Recipe(models.Model):
    #omitido codigo sem alteração
    category = models.ForeignKey(Category, on_delete=models.SET_NULL, null=True)
    author = models.ForeignKey(User, on_delete=models.SET_NULL, null=True)
```

Instalar o pacote Pillow requerido pelo campo ImageField do Django. Requisito antes de executar a migration.

```Bash
pip install pillow
```

Para realizar as sincronização com o banco de dados, é necessário gerar as migrations e em seguida aplicar no banco de dados. Usando os comandos a seguir:

```Bash
python manage.py makemigrations
python manage.py migrate
```
Após esta etapa, os models estarão no banco de dados como tabelas.


## 53. Recomendação de leitura - Django Model field reference

### Objetivos

* Recomendação de leitura - Django Model field.

### Etapas

[Django Model field reference](https://docs.djangoproject.com/pt-br/3.2/ref/models/fields/)
Existem vários campos e várias opções para cada um deles. Documentação oficial para entender melhor as possibilidades de tipos de dados para cada um dos campos desejados.

## 54. Registrando category na admin do Django

### Objetivos

* Usando a área administrativa do Django.

### Etapas

Para acessar a área administrativa, é necessário gerar usuário e senha:

```Bash
python manage.py createsuperuser
```

Em seguida, acessar área administrativa no endereço:

`http://localhost:8000/admin`

Por default estão registrados os models internos do Django: User e Group. Para adicionar os models da aplicação é necessário registrá-los no arquivo `recipes/admin.py`. Abaixo um exemplo básico que registra o model `Category` com as configurações default do Django.

```Python
from django.contrib import admin

from .models import Category


class CategoryAdmin(admin.ModelAdmin):
    ...

admin.site.register(Category, CategoryAdmin)
```

Para melhorar a exibição do model, ajusta a representacão como string, indicando qual campo deve aparecer (no caso o campo name), no arquivo `models.py`.

```Python
#omitido codigo sem alteração

class Category(models.Model):
    name = models.CharField(max_length=65)

    def __str__(self):
        return self.name

#omitido codigo sem alteração
```

## 55. Adicionando Recipes na admin do Django

### Objetivos

* Usando a área administrativa do Django.
* Registrando Recipes usando decorator

### Etapas

Registrar o model `Recipe` na área administrativa do Django, utilizando recurso de decorator.

```Python
#omitido codigo sem alteração
from .models import  Recipe

#omitido codigo sem alteração

@admin.register(Recipe)
class RecipeAdmin(admin.ModelAdmin):
    ...
```

No `settings.py`, definir as variáveis MEDIA_URL e MEDIA_ROOT para onde serão salvas as imagens inseridas no campo ImageField. 

```Python
#omitido codigo sem alteração
MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'media'
```

No arquivo `urls.py` do projeto,  definir a URL estática para que o Django saiba onde salvar e onde ler os arquivos de imagens. Atenção para boa prática no uso do pacote `settings` do Django quando for acessar conteúdo do arquivo `settings.py`. 

```Python
from django.conf import settings
from django.conf.urls.static import static

#omitido codigo sem alteração

urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
```

Definir o método `__str__` para o model `Recipe`, tambem chamado de método *Dunder* no Django.

```Python
#omitido codigo sem alteração
class Recipe(models.Model):
    
    #omitido codigo sem alteração

    def __str__(self):
        return self.title
```


## 56. Conhecendo o Django Shell e manipulando QuerySets com ele

### Objetivos

* Utilizando o recurso Django Shell para manipular models
* Shell interativo do Python para carregar os models

### Etapas

```Bash
python manage.py shell
```

No prompt do Python, é possível importar os models para manipula-los. Os models já possuem um manager (objects, no código a seguir) que transforma as interações em consultas SQL. Managers customizados podem ser criados, mas o Django já fornece uma solução bem completa.

```Python
from recipes.models import Recipe, Category
categories = Category.objects.all() # Retorna todos os objetos
print(categories)
categories.order_by('id') # Usa os recursos no manager para ordenar pelo ID
categories.order_by('-id') # Ordenação inversa pelo ID
categories.order_by('id', '-name') # Ordenação usando 2 campos
recipes = Recipe.objects.all()
print(recipes)
for recipe in recipes: print(recipe.id, recipe.title)
for recipe in recipes.order_by('-id'): print(recipe.id, recipe.title)
recipe = recipes.order_by('-id').first() # Retorna apenas o primeiro da QuerySet
recipe._meta.get_fields() #Para descobrir os campos de uma instância de objeto

new_category = Category() # QuerySets usa o conceito de Lazy QuerySet
new_category.name = 'Nova categoria' # Atribuindo valor
new_category.save() # Salvando no banco de dados
new_category.id # Verificando o ID do registro salvo

new_category = Category.objects.create(name='Nova categoria de comida') # Não usa Lazy QuerySet

other_category = Category.objets.get(id=1) # Busca objeto com ID=1
other_category.name = 'Outra categoria'
other_category.save() # Atualiza a instancia no banco de dados
other_category.delete()  # Deleta no banco de dados
```
Conceito de [Lazy QuerySet](https://docs.djangoproject.com/pt-br/3.2/topics/db/queries/#querysets-are-lazy)
[Django Shell](https://docs.djangoproject.com/pt-br/3.2/ref/django-admin/#shell)

## 57. Recomendação de leitura - QuerySet API reference

[QuerySet API reference](https://docs.djangoproject.com/pt-br/3.2/ref/models/querysets/)

## 58. Usando o model Recipe real (com QuerySet) na view home

### Objetivos

* Buscar registro de Recipes do banco de dados em vez de usar dados gerados.

### Etapas

Mudar view para buscar objetos no banco de dados. Buscando `Recipes` e `Category` usando o ORM e passando no contexto para o template.

```Python
#omitido codigo sem alteração
from recipes.models import Recipe

def home(request):
    recipes = Recipe.objects.all().order_by('-id')
    return render(request, 'recipes/pages/home.html', context={
        'recipes': recipes,
    })

def category(request, category_id):
    recipes = Recipe.objects.filter(
        category__id=category_id
    ).order_by('-id')
    return render(request, 'recipes/pages/home.html', context={
        'recipes': recipes,
    })

#omitido codigo sem alteração
```

No `urls.py` de `recipe`, adicionar rota para `category` quando passar o `id` como parametro.

```Python
#omitido codigo sem alteração
urlpatterns = [
    #omitido codigo sem alteração
    path('recipes/category/<int:category_id>/', views.category, name='category'),
]
```

Alterar template `recipe/recipe.html` para os seguintes casos:
* Quando o usuário não submeter uma imagem;
* Exibir o username quando os campos first_name e last_name estiverem vazios;
* Pegar a category dinamicamente do banco de dados.

```Django
<!-- omitido codigo sem alteração -->
<div class="recipe recipe-list-item">
    {% if recipe.cover %}
        <div class="recipe-cover">
            <a href={% url 'recipes:recipe' recipe.id %}>
                <img src="{{ recipe.cover.url }}" alt="Temporário">
            </a>
        </div>
    {% endif %}
    <div class="recipe-title-container">
        <h2 class="recipe-title">
            <a href={% url 'recipes:recipe' recipe.id %}>
                {{ recipe.title }}
            </a>
        </h2>
    </div>
    <div class="recipe-author">
        <span class="recipe-author-item">
            <i class="fa-solid fa-user"></i>
            {% if recipe.author.first_name %}
                {{ recipe.author.first_name }} {{ recipe.author.last_name }}
            {% else %}
                {{ recipe.author.username }}
            {% endif %}
        </span>
        <span class="recipe-author-item">
            <i class="fa-solid fa-calendar-alt"></i>
            {{ recipe.created_at|date:"d/m/Y"}} às {{ recipe.created_at|date:"H:i"}}
        </span>
        <span class="recipe-author-item">
            <a href="{% url 'recipes:category' recipe.category.id %}">
                <i class="fa-solid fa-layer-group"></i>
                <span>{{ recipe.category.name }}</span>
            </a>
        </span>
    </div>
    <!-- omitido codigo sem alteração -->
```

Alterar o campo `cover` da classe `Recipe` em `models.py` para permitir que seja vazio.

```Python
class Recipe(models.Model):
    # omitido codigo sem alteração
    cover = models.ImageField(upload_to='recipes/covers/%Y/%m/%d/', blank=True, default='')
    # omitido codigo sem alteração
```

Sincronizar o banco de dados.

```Bash
python manage.py makemigrations
python manage.py migrate
```

## 59. Ocultando receitas que não estão publicadas (filter para is_published)

### Objetivos

* Usar o campo `is_published` no template.

### Etapas

Adicionar um novo template para exibir as categorias de receitas criando o arquivo em `recipes/templates/recipes/pages/category.html` com o conteúdo.

```Django
{% extends 'global/base.html' %}

{% block title %}Category |{% endblock title %}

{% block content %}
    <div class="main-content main-content-list container">
        {% for recipe in recipes %}
            {% include 'recipes/partials/recipe.html' %}
        {% endfor %}
    </div>
{% endblock content %}
```

Em `views.py` alterar a view `home` e `category` passando o filtro `is_published=True` para retornar apenas as receitas que correspondam a esse critério. Adicionalmente, alterar o template da view `category` para o criado anteriormente.

```Python
# omitido codigo sem alteração
def home(request):
    recipes = Recipe.objects.filter(is_published=True).order_by('-id')
    
def category(request, category_id):
    recipes = Recipe.objects.filter(
        category__id=category_id,
        is_published=True
    ).order_by('-id')
    return render(request, 'recipes/pages/category.html', context={
        'recipes': recipes,
    })
# omitido codigo sem alteração
```

## 60. Corrigindo category com blank True e default None

### Objetivos

* Corrigir para permitir editar uma receita fique sem categoria.

### Etapas

Ao permitir que uma categoria seja excluída, a modelagem atual deixa o campo como Null, porém o Recipe não pode ser editado para ser atribuída uma outra categoria. Para isso será necessário alterar o model para permitir que o campo fique sem preencher.

Em `models.py` adicionar `blank=True` e `default=None` no campo `category` da classe `Recipe`:

```Python
class Recipe(models.Model):
    # omitido codigo sem alteração
    category = models.ForeignKey(
        Category, on_delete=models.SET_NULL, null=True, blank=True, default=None
    )
    # omitido codigo sem alteração
```

Sincronizar banco de dados:

```Bash
python manage.py makemigrations
python manage.py migrate
```

Ajustar template `recipe.html` com uma verificação que permite apenas exibir quando o campo não for `None`.

```Django
<!-- omitido codigo sem alteração -->
{% if recipe.category is not None %}
    <span class="recipe-author-item">
        <a href="{% url 'recipes:category' recipe.category.id %}">
            <i class="fa-solid fa-layer-group"></i>
            <span>{{ recipe.category.name }}</span>
        </a>
    </span>
{% endif %}
<!-- omitido codigo sem alteração -->
```
