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
