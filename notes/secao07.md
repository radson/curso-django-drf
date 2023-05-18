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

