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