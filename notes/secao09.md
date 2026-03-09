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
