# Seção 9: Djanto testing - Introdução aos testes unitários no Django com Pytest e Unittest

## 70. Corrigindo erros de tipagem e linha longa no settings.py

### Objetivos

* Fazer ajustes para ignorar erros do flake ou do mypy

### Etapas

No arquivo `settings.py` na variável `ALLOWED_HOSTS` adicionar comentário do mypy para ignorar erro do tipo de lista não declarado.

```Python
ALLOWED_HOSTS = [] # type: ignore
```

Em linhas que forem erros do flake, pode ser usada o comentário.

```Python
SECRET_KEY = 'django-insecure-23+&*8v5@0wywyop-ogm5$w_xtxvb(z72fo&v@04csq#_asa1+' # noqa: E501
```

## 71. Iniciando com testes no Django com Pytest, Pytest-Django e Unittest

### Objetivos

* Configurar o ambiente de testes.

### Etapas

No `.vscode/settings.json` devem estar as configurações:

```json
  "python.testing.unittestEnabled": false, // ms-python.python
  "python.testing.pytestEnabled": true,
  "python.testing.pytestArgs": [], // -x to bail
```

No terminal, com venv ativado instalar os pacotes

```shell
pip install pytest pytest-django
```

Criar um `pytest.ini` com o conteúdo a seguir, que define mascara para os arquivos de teste. Mais detalhes na documentação [Pytest-Django Docs](https://pytest-django.readthedocs.io/en/latest/index.html#example-using-pytest-ini-or-tox-ini)

```ini
[pytest]
DJANGO_SETTINGS_MODULE = project.settings
python_files = test.py tests.py test_*.py tests_*.py *_tests.py *_test.py
addopts =
    --doctest-modules
    --strict-markers
markers = 
    slow: Run tests that are slow
    fast: Run fast tests
```

No arquivo `recipes\tests.py` é onde serão definidos os testes para o app recipes. Cada categoria de testes é definido por uma classe e cada metódo um teste.

```Python
from django.test import TestCase


class RecipeURLsTest(TestCase):
    def test_the_pyteste_is_ok(self):
        assert 1 == 1, 'Um igual a um'
```

Para rodar os testes no terminal, com venv ativado.

```shell
pytest
```

Para usar o test runner do Django.

```shell
python manage.py test
```

## 72. O que é um Test Runner?


Na programação, um teste automatizado é formado por vários componentes, um deles é o Test Runner. Este é o componente responsável por orquestrar a execução de todos os testes automatizados do seu programa e prover uma saída para o usuário, como testes que passaram e falharam, erros e stack trace dos erros, mensagens e informações sobre esses testes.

Você observou na última aula dois Test Runners diferentes, o do Unittest e o do Pytest. Mas você pode encontrar Test Runners que funcionam de maneira diferente, como via Interface Gráfica, via Web, via terminal ou integrado na IDE (ou editor de textos).

Para executar o Test Runner do Pytest, use o comando:

    pytest

Para executar o Test Runner do Unittest (padrão do Django) use o comando:

    python manage.py test

Sempre com ambiente virtual ativo.

A ideia de todos os Test Runners é sempre a mesma, executar e prover informações sobre os testes executados.

## 73. Asserções (afirmações), Django Reverse URL e Pytest-Watch

### Objetivos

* Iniciar a implementação dos testes usando o Django Reverse URL.

### Etapas

No arquivo `recipes\tests.py`, remover o teste anterior `test_the_pyteste_is_ok` e criar um novo para teste da URL home. O nome dos testes deve ser descritivo para identificar o teste, nesse caso o nome será `test_recipe_home_url_is_correct`.

```Python
from django.test import TestCase
from django.urls import reverse


class RecipeURLsTest(TestCase):
    def test_recipe_home_url_is_correct(self):
        home_url = reverse("recipes:home")
        self.assertEqual(home_url, "/")
```

Para executar o teste, com venv ativado

```shell
pytest
```

## 74. Testando Django Reverse URL com argumentos args e kwargs

### Objetivos

* Testar demais URLs.

### Etapas

Semelhante ao teste anterior, adicionar para as próximas URLs. No caso delas que recebem parametro, usar o kwargs.

```Python
# Omitindo código anterior
def test_recipe_category_url_is_correct(self):
    url = reverse("recipes:category", kwargs={"category_id": 1})
    self.assertEqual(url, "/recipes/category/1/")

def test_recipe_detail_url_is_correct(self):
    url = reverse("recipes:recipe", kwargs={"id": 1})
    self.assertEqual(url, "/recipes/1/")
```

## 75. Usando resolve para entender qual view function está sendo usada

### Objetivos

* Implementar testes para verificar se as URLs estão chegando nas views e rederizando como esperado.

### Etapas

A função `resolve` do Django irá resolver qual função da URL está sendo usada. O `assertIs` verifica se a função retornada em resolve é a mesma referencia que `views.home`.

```Python
from django.urls import resolve
from recipes import views

# omitido código sem alteração

class RecipeViewsTest(TestCase):
    def test_recipe_home_view_function_is_correct(self):
        view = resolve(reverse("recipes:home"))
        self.assertIs(view.func, views.home)

    def test_recipe_category_view_function_is_correct(self):
        view = resolve(reverse("recipes:category", kwargs={"category_id": 1}))
        self.assertIs(view.func, views.category)

    def test_recipe_detail_view_function_is_correct(self):
        view = resolve(reverse("recipes:recipe", kwargs={"id": 1}))
        self.assertIs(view.func, views.recipe)
```

## 76. Separando os testes em um package chamado tests com `__init__.py`

### Objetivos

* Em vista do crescimento do número e categorias de testes, oganizar os testes em packages do Python.

### Etapas

Dentro do app `recipes` adicionar um diretório `tests` com o arquivo `__init__´.py` que indica um pacote.

```shell
mkdir recipes/tests
> recipes/tests/__init__.py
> recipes/tests/test_recipe_url.py
> recipes/tests/test_recipe_views.py
```

O conteúdo do `test_recipe_url.py` será:

```Python
from django.test import TestCase
from django.urls import reverse


class RecipeURLsTest(TestCase):
    def test_recipe_home_url_is_correct(self):
        url = reverse("recipes:home")
        self.assertEqual(url, "/")

    def test_recipe_category_url_is_correct(self):
        url = reverse("recipes:category", kwargs={"category_id": 1})
        self.assertEqual(url, "/recipes/category/1/")

    def test_recipe_detail_url_is_correct(self):
        url = reverse("recipes:recipe", kwargs={"id": 1})
        self.assertEqual(url, "/recipes/1/")
```

O conteúdo do `test_recipe_views.py` será:

```Python
from django.test import TestCase
from django.urls import resolve, reverse

from recipes import views


class RecipeViewsTest(TestCase):
    def test_recipe_home_view_function_is_correct(self):
        view = resolve(reverse("recipes:home"))
        self.assertIs(view.func, views.home)

    def test_recipe_category_view_function_is_correct(self):
        view = resolve(reverse("recipes:category", kwargs={"category_id": 1}))
        self.assertIs(view.func, views.category)

    def test_recipe_detail_view_function_is_correct(self):
        view = resolve(reverse("recipes:recipe", kwargs={"id": 1}))
        self.assertIs(view.func, views.recipe)
```

Depois de criar os arquivos e separar os conteúdos das classes nos respectivos arquivos, deletar o `recipes/tests.py` e testar se os testes irão passar.

```shell
rm recipes/tests.py
pytest
```

## 77. Usando self.client do Django para simular o cliente nos testes

### Objetivos

* Adicionar recursos extras de validação como status code e template de cada view.

### Etapas

O Django prove um client para testar as requests e responses do HTTP. Para ambos os casos o cliente usará um `get()`. No teste de template, usa-se um assert específico `assertTemplateUsed` que verifica se na response o template especificado foi carregado.

No arquivo `test_recipe_views.py` adicionar os seguintes testes.

```Python
class RecipeViewsTest(TestCase):
    # omitindo código já existente

    def test_recipe_home_view_returns_status_code_200_OK(self):
        response = self.client.get(reverse("recipes:home"))
        self.assertEqual(response.status_code, 200)

    def test_recipe_home_view_loads_correct_template(self):
        response = self.client.get(reverse("recipes:home"))
        self.assertTemplateUsed(response, "recipes/pages/home.html")
```

## 78. Testando "No recipes found" quando receitas não existem

### Objetivos

* Testar quando uma view que deveria listar recipes não encontra nenhum e registo mas retorna uma string específica.

### Etapas

Para esse caso, usa-se o `assertIn` que busca uma substring dentro do `response.content`, que é o HTML do template. Neste caso há a string previamente definida.


```Python
class RecipeViewsTest(TestCase):
    # omitindo código já existente

    def test_recipe_home_template_shows_no_recipe_found_if_no_recipes(self):
        response = self.client.get(reverse("recipes:home"))
        self.assertIn("No recipes found", response.content.decode("utf-8"))
```

ATENÇÂO: Mesmo que haja recipes no banco de dados, quando o runner executa os testes ele cria um banco vazio em memória, neste caso específico o teste irá passar. Nas próximas seções serão usados os fixtures que são dados para os testes.

## 79. Testando 404 Not Found quando receitas não existem

### Objetivos

* Testar quando a view de category ou detail não encontrar nenhum registo e retornar 404.

### Etapas

A mesma lógica utilizada para verificar `status_code`. 

```Python
class RecipeViewsTest(TestCase):
    # omitindo código já existente

    def test_recipe_category_view_returns_404_if_no_recipes_found(self):
        response = self.client.get("recipes:category", kwargs={"category_id": 1})
        self.assertEqual(response.status_code, 404)
    
    def test_recipe_detail_view_returns_404_if_no_recipes_found(self):
        response = self.client.get("recipes:recipe", kwargs={"id": 1})
        self.assertEqual(response.status_code, 404)
```

Para testar, apresentando um modo diferente, onde apenas um dos testes precisa ser validado, usando o parametro `-k` tanto do `pytest` quanto do `manage.py test` onde informamos o nome do teste.

```shell
pytest -k 'test_recipe_detail_view_returns_404_if_no_recipes_found'
python manage.py test -k 'test_recipe_detail_view_returns_404_if_no_recipes_found'
```

## 80. Criando fixtures para um teste único (Recipe, Category e Author)

### Objetivos

* Testar views com conteúdo usando fixtures.

### Etapas

Inicialmente, adicionar um teste que cria manualmente os objetos. Qualquer parte de código que é criado para dar suporte ao teste é chamado de fixture.

Na próxima aula adicionar as assertions.

```Python
from recipes.models import Category, Recipe, User

class RecipeViewsTest(TestCase):
    # omitindo código já existente

    def test_recipe_home_templats_loads_recipe(self):
        category = Category.objects.create(name="Category")
        author = User.objects.create_user(
            first_name="User",
            last_name="Last",
            username="username",
            password="1234",
            email="username@gmail.com",
        )
        recipe = Recipe.objects.create(
            category=category,
            author=author,
            title="Recipe Title",
            description="Recipe Description",
            slug="recipe-slug",
            preparation_time="10",
            preparation_time_unit="Minutos",
            servings=5,
            servings_unit="Porções",
            preparation_steps="Recipe Preparation Stepes",
            preparation_steps_is_html=False,
            is_published=True,
        )
```

## 81. Testando o context e content da response no template

### Objetivos

* Implementando os asserts para verificar o conteúdo retornado.

### Etapas

 Será verificado no context o número de recipes criado e se no content há evidência das strings esperadas.

 ```Python
     def test_recipe_home_templats_loads_recipe(self):
        # omitindo código já existente

        response = self.client.get(reverse("recipes:home"))
        content = response.content.decode("utf-8")
        response_context_recipes = response.context["recipes"]

        self.assertEqual(len(response_context_recipes), 1)
        self.assertIn("Recipe Title", content)
        self.assertIn("10 Minutos", content)
        self.assertIn("5 Porções", content)
```

## 82. Criando uma classe de base para os testes

### Objetivos

* Usar métodos de `Set Up` e `Tear Down` para serem usados antes e depois do testes. Criar uma classe apenas do `Set Up`.

### Etapas

Adicionar o arquivo `recipes\tests\test_recipe_base.py` onde será implementada uma classe base que herda de `TestCase`. Nesta classe será movido o conteúdo que cria os objetos Category, User e Recipe que estavam anteriormente em `test_recipe_home_templats_loads_recipe`.

 ```Python
from django.test import TestCase
from recipes.models import Category, Recipe, User

class RecipeTestBase(TestCase):
    def setUp(self) -> None:
        category = Category.objects.create(name="Category")
        author = User.objects.create_user(
            first_name="User",
            last_name="Last",
            username="username",
            password="1234",
            email="username@gmail.com",
        )
        recipe = Recipe.objects.create(
            category=category,
            author=author,
            title="Recipe Title",
            description="Recipe Description",
            slug="recipe-slug",
            preparation_time="10",
            preparation_time_unit="Minutos",
            servings=5,
            servings_unit="Porções",
            preparation_steps="Recipe Preparation Stepes",
            preparation_steps_is_html=False,
            is_published=True,
        )
        return super().setUp()

```

Em `test_recipe_views.py` pode-se remover o import dos `models` e adicionar o import da classe base.

 ```Python
from .test_recipe_base import RecipeTestBase

class RecipeViewsTest(RecipeTestBase):
    # omitindo código já existente
```

Nesta aula, com essa implementação o teste `test_recipe_home_template_shows_no_recipe_found_if_no_recipes` irá falhar pois não deveria encontrar registros. Será implementada solução na próxima aula.