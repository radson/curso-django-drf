# Seção 06 - Django Staticfiles: Arquivos estáticos (imagens, css, javascript, etc)

## 37. Sobre arquivos estáticos

Arquivos estáticos são arquivos que são entregues exatamente como estão salvos. Esses arquivos quase não sofrem alterações, por isso, o navegador pode salvá-los em cache para que o conteúdo da página carregue mais rapidamente para o usuário final. Alguns provedores de Internet também adicionam servidores no meio da rede que também podem fazer cache desse arquivos, assim o próprio provedor economiza sua banda de Internet externa.

Frequentemente, sites adicionam seu conteúdo estático em CDNs (Content Delivery Network) que são servidores otimizados para entrega de arquivos estáticos tanto em velocidade de entrega quando em manter os arquivos na localização mais próxima do usuário final.

O Django consegue lidar com arquivos estáticos no seu servidor ou em CDNs.

## 38. Uma longa descrição sobre arquivos estáticos

* Explicação da necessidade de organizar imports e CSS no head do projeto em arquivos estáticos.

## 39. Adicionando Static Files em Recipes para o CSS

### Objetivos

* Implementando os arquivos estáticos no projeto

### Etapas

O Django já vem preparado para reconhecer o diretório ```static``` na raiz do projeto ver mais em [arquivos estáticos](https://docs.djangoproject.com/en/3.2/howto/static-files/). Usando o mesmo conceito de namespacing, será adicionado um subdiretório com o nome do app onde podem conter arquivos estáticos de estilo, javascript ou imagens.

```Bash
mkdir -p recipes/static/recipes/css
> recipes/static/recipes/css/style.css
```

O conteúdo que estava na tag ```style``` no arquivo parcial ```head.html``` será movido para um novo arquivo ```style.css``` dentro do diretório recem criado.

No arquivo ```head.html``` será adicionada a referência ao novo arquivo ```style.css``` usando a tag ```static``` do Django. 

```Django
{% load static %}

<!DOCTYPE html>
<html lang="pt-BR">

<head>
   <!-- omitido código sem alteração -->

    <link rel="stylesheet" href={% static 'recipes/css/styles.css' %}>
    
    <title>Recipes</title>
</head>
```

## 40. STATIC_URL e STATICFILES_DIRS no Django

### Objetivos

* Usando outra forma de utilizar arquivos estáticos que não associadas a uma aplicação.

### Etapas

No ```settings.py``` a lista ```STATICFILES_DIRS``` permite adicionar o caminho de outros diretórios para arquivos estáticos além do caminho padrão que o Django usa.
