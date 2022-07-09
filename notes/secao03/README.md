# Seção 03 - Configurações do VSCode e venv Python

## Aula 09 - Ambiente virtual com Python e venv

Foi utilizado pacote da distribuição com 'Python 3.8'

```
mkdir projeto1 && cd projeto1
python3 -venv venv
source venv/bin/activate
```

Atualizar o gerenciador de pacotes do Python

```
pip install -U pip setuptools wheel
```

Instalar o Django e o pytest

```
pip install django==3.2.14
pip install pytest 
```
Salvar lista de pacotes instalados

```
pip freeze > requirements.txt
```

## Aula 10 - Usando VSCode para automatizar a ativação do ambiente virtual e mais

Abrir no VSCode 'code .' em seguida 'CRTL + Shift + P'  para selecionar o interpretador na versão instalada na aula anterior Python 3.8 referente ao venv

```
source projeto1/venv/bin/activate
```

Na coluna da barra de atividades do VSCode será ativado o ícone da extensão de testes por conta dos settings ativados durante a aula e da presença do pytest (confirmar). 

Para testar criar arquivo 'test_testando.py' com o conteúdo:

```
def test_something():
    assert 1 == 1
```

Acessando os testes na barra de atividades o teste criado será reconhecido e pode ser executado.

O VSCode irá notificar sugerindo a instalação de algumas extensões que devem ser realizadas:
- 'autopep8': Um formartter
- 'mypy': Um linter
- 'flake8': Outro linter

Podem também ser instalados pelo terminal

```
pip install -U autopep8 mypy flake8
pip freeze > requirements.txt
```