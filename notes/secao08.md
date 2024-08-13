# Seção 8: Django Models e ORM (Object Relational Mapper)

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

## 61. Corrigindo autor nulo

### Objetivos

* Tratar a exibição do autor da receita quando o usuário do autor é excluído.

### Etapas

Em `recipe.html`, adicionar uma verificação para quando a receita não tem um autor (campo `author` é `None`).

```Django
<!-- omitido codigo sem alteração -->
<div class="recipe-author">
    {% if recipe.author is not None %}
        <span class="recipe-author-item">
            <i class="fa-solid fa-user"></i>
            {% if recipe.author.first_name %}
                {{ recipe.author.first_name }} {{ recipe.author.last_name }}
            {% else %}
                {{ recipe.author.username }}
            {% endif %}
        </span>
    {% endif %} 
<!-- omitido codigo sem alteração -->
```
Adicionalmente, segue o processo para criação de usuários via shell do Django

```Bash
python manage.py shell
```

```Python
from django.contrib.auth.models import User
User.objects.create_user(firts_name='Maria', last_name='Helena', username='maria', email='maria@email.com', password='123456')
```

Se a criação for realizada com sucesso, o método `create_user` retorna a instância do usuário criado.

## 62. Mostrando erro 404 Not Found para páginas que não existem

### Objetivos

* Adicionar tratamento de exceção para páginas inexistentes.
* Adicionar o nome da categoria na barra de títulos para auxiliar os mecanismos de busca.

### Etapas

Em `views.py` adicionar tratamento para a consulta não retornar objetos `Category`. Caso retorne, pegar do primeiro elemento o campo `name`.

```Python
from django.http import Http404

# omitido codigo sem alteração 

def category(request, category_id):
    # omitido codigo sem alteração 

    if not recipes:
        raise Http404('Not found.')

    return render(request, 'recipes/pages/category.html', context={
        'recipes': recipes,
        'title': f'{recipes.first().category.name} - Category '
    })

# omitido codigo sem alteração 
```

No template `category.html`, remover a logica do template (que agora está na view - boa prática), e passar no contexto a variável `title`.

```Django
<!-- omitido codigo sem alteração -->
{% block title %}{{ title }}{% endblock title %}
<!-- omitido codigo sem alteração -->
```

## 63. Usando o shortcut get_list_or_404 (atalho)

### Objetivos

* Usando shortcut para quando não existir um elemento ou lista.

### Etapas

O shortcut `get_list_or_404` retorna uma lista de objetos ou lança o código de erro HTTP 404. Para acessar o primeiro valor da lista, usa-se o índice 0.

```Python
from django.shortcuts import get_list_or_404, render

# omitido codigo sem alteração 

def category(request, category_id):
    recipes = get_list_or_404(
        Recipe.objects.filter(
        category__id=category_id,
        is_published=True).order_by('-id')
    )

    return render(request, 'recipes/pages/category.html', context={
        'recipes': recipes,
        'title': f'{recipes[0].category.name} - Category '
    })
```

## 64. Mostrando a página de detalhes da receita

### Objetivos

* Implementar a exibição de uma receita a partir do banco de dados.

### Etapas

Em `views.py` alterar a view que retorna uma receita, usando o shortcut `get_object_or_404`.

```Python
from django.shortcuts import get_object_or_404

# omitido codigo sem alteração 
def recipe(request, id):
    recipe = get_object_or_404(
        Recipe.objects.filter(
        pk=id, is_published=True).order_by('-id')
    )

    return render(request, 'recipes/pages/recipe-view.html', context={
        'recipe': recipe,
        'is_detail_page': True,
    })
```

No template `recipe.html` adicionar uma estrutura de seleção para quando for exibir HTML na página e permitir renderizar com a tag `safe`.

```Django
<!-- omitido codigo sem alteração -->
{% if is_detail_page is True %}
    <div class="preparation-steps">
        {% if recipe.preparation_steps_is_html is True %}
            {{ recipe.preparation_steps|safe }}
        {% else %}
            {{ recipe.preparation_steps|linebreaksbr }}
        {% endif %}
    </div>
{% endif %}
```

## 65. Ajustando o css do modo de preparo

### Objetivos

* Ajustando o css do modo de preparo.

### Etapas

Em `recipe/static/recipes/css/style.css` adicionar no final do arquivo, estilo para melhor exibição de receitas com modo de preparo com HTML.

```Css
/* omitido codigo sem alteração */
.preparation-steps h1,
.preparation-steps h2,
.preparation-steps h3,
.preparation-steps h4,
.preparation-steps h5,
.preparation-steps h6,
.preparation-steps p,
.preparation-steps pre {
    margin: 2rem 0;
}

.preparation-steps ol,
.preparation-steps ul{
    margin: 2rem;
    padding: 0 2rem;
}
```

## 66. Usando get_object_or_404 ao invés de fazer a consulta na mão

### Objetivos

* Corrigir a view `recipe` para o shortcut `get_object_or_404`.

### Etapas

Em `views.py` corrigir para uso do shortcut e remover o `order_by` pois não é necessário para um único objeto.

```Python
# omitido codigo sem alteração
def recipe(request, id):
    recipe = get_object_or_404(
        Recipe.objects.filter(
        pk=id, is_published=True)
    )
    # omitido codigo sem alteração
```

No template `recipe-view.html` ajustar o titulo para pegar dinamicamente.

```Django
<!-- omitido codigo sem alteração -->
{% block title %}{{ recipe.title }} |{% endblock title %}
<!-- omitido codigo sem alteração -->
```

## 67. Usar get_list_or_404 na home ou não?

### Objetivos

* Ajustar a home para quando não houverem receitas cadastradas.

### Etapas

No template `home.html` ajustar o `for` para usar a tag `empty` quando a lista `recipes` é vazia.

```Django
<!-- omitido codigo sem alteração -->
{% block content %}
    <div class="main-content main-content-list container">
        {% for recipe in recipes %}
            {% include 'recipes/partials/recipe.html' %}
        {% empty %}
            <div class="center m-y">
                <h1>No recipes found here.</h1>
            </div>
        {% endfor %}
    </div>
{% endblock content %}
```

Ajustar o `styles.css` para melhorar exibição da mensagem no `h1`.

```Css
/* omitido codigo sem alteração */
/* Generics */

.center {
    text-align: center;
}

.m-x {
    margin: 0 var(--spacing-gutter-medium);
}

.m-y {
    margin: var(--spacing-gutter-medium) 0;
}
```

## 69. Renomeando um projeto Django inteiro

### Objetivos

* Mostrando como alterar o nome de um projeto. 
* Corrigir nome `projeto` em portugues e app `recipes` em ingles.

### Etapas

Alterar a string `projeto` para `projects` na lista de arquivos a seguir, desconsiderar as demais linhas que permaneceram inalteradas.

`projeto/asgi.py`

```Python
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'project.settings')
```

`projeto/wsgi.py`

```Python
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'project.settings')
```

`projeto/settings.py`

```Python
ROOT_URLCONF = 'project.urls'

WSGI_APPLICATION = 'project.wsgi.application'
```

`manage.py`

```Python
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'project.settings')
```

Por fim, renomear o diretório `projeto` que está no mesmo nível de `recipes`:

```Bash
mv projeto project
```

Executa novamente o projeto para certificar que esta tudo funcionando:

```Bash
python manage.py runserver
```