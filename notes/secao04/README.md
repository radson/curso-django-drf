# Seção 04 - Ambiente Django + Git e Github com chaves SSH (Iniciando o primeiro projeto)

## Aula 11 - Iniciando o primeiro projeto com Django e venv

Realizar a preparação do primeiro projeto conforme descrito na [Aula 09](https://github.com/radson/curso-django-drf/tree/main/notes/secao03#aula-09---ambiente-virtual-com-python-e-venv)

## Aula 12 - Configurando chaves SSH, git e enviando o projeto para o Github

Nesta aula deve-se criar um repositório no [Github](https://github.com/new)

Configurações básicas para o uso do git, configuração do username e email:

```Shell
git config --global user.name "Nome Sobrenome"
git config --global user.email "nome@email.com.br"
git config --global init.defaultBranch main
git init # dentro do diretorio do projeto
git remote add origin git@github.com:username/reponame.git
git remote -v
```

Para gerar as chaves ssh privada (id_rsa) e publica (id_rsa.pub)

```Shell
ssh-keygen
```
Acessar o arquivo gerado em ```~/.ssh/id_rsa.pub``` e copiar o conteúdo que será incluído no Github na sessão ``` Settings > SSH and GPG keys ``` e colar no formulário ```New SSH key```.

Para evitar que o conteúdo do venv seja incluído no repositório e apenas o código da aplicação desenvolvida, deve-se criar um arquivo chamado gitignore e incluir o nome do diretório.

```Shell
echo "venv" | tee .gitignore
```

Depois deve-se commitar as alterações e enviar para o Github (push)

```Shell
git add .gitignore
git commit -m "Commit inicial"
git push --set-upstream origin main
```

## Aula 13 - Repositório do curso

[Repositório do curso](https://github.com/luizomf/curso-django-projeto1)

## Aula 14 - Um .gitignore exclusivo para o Django e Python

[Fonte utilizada para a aula](https://djangowaves.com/tips-tricks/gitignore-for-a-python-project/)
[Fonte pessoal](https://www.toptal.com/developers/gitignore/api/django,python)


## Aula 15 - Hello Django - Uma breve conferida no servidor de desenvolvimento do Django

Criar um projeto em Django

```Shell
django-admin starproject projeto .
```

O uso do '.' indica o local onde será criado o projeto. Se for omitido o Django vai criar uma estrutura de diretórios com o nome do projeto e dentro dele mais um com o mesmo nome. Essa estratégia visa simplificar a organização do código.

Para executar o servidor http interno do Django para ambiente de desenvolvimento.

```Shell
python manage.py runserver
```

Um warning será mostrado das migrações não aplicadas, mas para esta aula pode ser ignorado.

O ```manage.py``` e o ```django-admin``` contem algumas funcionalidades para gerenciamento de projetos e aplicações Django.

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


