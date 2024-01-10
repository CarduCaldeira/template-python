# Objetivo

Este repositorio tem como objetivo criar um ambiente de desenvolvimento em python utilizando poetry, testes, formatadores e documentação em um container.

## Configurando o poetry

Dentro da pasta template-python excecute 
``` 
poetry new template-python 
```
Que ira criar a pasta template-python com a seguinte estrutura
```
.
├── pyproject.toml
├── README.md
├── template-python
│   └── __init__.py
└── tests
    └── __init__.py
```
Por padrão o poetry cria a pacote do seu projeto com o mesmo nome da pasta. Nesse tutorial vamos mudar para source, ao fazer isso, é necessario atualizar o pyproject.toml, mudando name = "template-python" para name = "source".

Outra opção para configurar o ambiente python é dar comando 
```
poetry init
```
na pasta desejada, que interativamente ira construir o seu arquivo pyproject.toml.

### Configurando dependencias do ambiente

Após a inicializaçao do poetry para adicionar dependencias ao projeto basta dar o comando
```
poetry add <lib>
```
Por exemplo
```
poetry add pandas
```
O pyproject.toml deve ficar dessa forma após o comando add
```
[tool.poetry]
name = "source"
version = "0.1.0"
description = "Project for build a template in python"
authors = ["carlos <carducaldeira@gmail.com>"]
license = "MIT"
readme = "README.md"

[tool.poetry.dependencies]
python = "^3.10"
pandas = "^2.1.4"

[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"
```

### Instalando as depedencias

Para instalar as depedencias presentes no arquivo pyproject.toml 
```
poetry install
```
Isso criara um arquivo poetry.lock. Tal arquivo de certa forma é equivalente ao requirements.txt quando utilizado o pip. Nele teremos um ambiente deterministico que satisfaz as condiçoes do arquivo pyptoject.toml, junto dos respectivos hashes de cada dependencia (assim garantindo mais segurança na instalaçao).

Por padrão o ambiente virtual não é instalado na raiz do projeto. Para visualizar onde foi instalado de o comando 
```
poetry env info
```
A saída será algo do tipo 
```
Virtualenv
Python:         3.10.12
Implementation: CPython
Path:           /home/carlos/.cache/pypoetry/virtualenvs/source-FYY5wWM6-py3.10
Executable:     /home/carlos/.cache/pypoetry/virtualenvs/source-FYY5wWM6-py3.10/bin/python
Valid:          True

System
Platform:   linux
OS:         posix
Python:     3.10.12
Path:       /usr
Executable: /usr/bin/python3.10
```
Vamos deletar os arquivos em home/carlos/.cache/pypoetry/virtualenvs/ e tambem o poetry.lock e instalar novamente as dependencias, porem deta vez iremos criar o ambiente virtual junto do projeto. Atribuia a variavel de ambiente POETRY_VIRTUALENVS_IN_PROJECT=true.
Ao dar novamente o comando poetry install note agora que temos a pasta .venv na raiz do projeto.

Para executar o poetry com o ambiente virtual criado de o comando 
```
poetry shell
```

No poetry é facil de administrar as depenpencias do projeto. Para desinstalar algum pacote basta dar o comando
```
poetry remove <lib>
```
Por exemplo,
```
poetry remove pandas
```
Note que o pyptoject.toml nao contem mais o pandas listado (o poetry nao so remove o pandas, mas as dependencias instaladas pelo pandas).

## Configurando ambiente de desenvolvimento

Vamos adicionar ao projeto os pacotes responsaveis pela tarefa de teste, formatação, linter e documentação.

De o comando 
```
poetry add --group dev pytest pytest-cov isort bandit  black blue sphinx taskipy 
```
Ao adicionarmos as depencias sobre a tag --group dev estamos dizendo ao poetry que tais dependencias são utilizadas apenas no desenvolvimento, ao utilizar em produção podemos dar o comando 
```
poetry export --output requirements.txt
```
Que ira exportar as dependencias sem incluir as relacionadas ao grupo dev. Caso queira exportar todas as dependencias (incluindo as relacionadas ao grupo dev) basta dar o comando
```
poetry export --output requirements.txt --dev
```
O poetry utiliza o poetry-plugin-export para exportar tais dependencias, nas versoes futuras do poetry sera necessario sua instalação  
```
poetry self add poetry-plugin-export
```

Um exemplo interessante é no caso é que o black é dependente do pacote click, que se instalado em uma versao ^8.1.0 apresentara problemas. Para solucionar isso, no arquivo pyproject.toml em [tool.poetry.group.dev.dependencies] adicione a linha click = "8.0.4". Para atualizar o projeto com as dependencias atualidas de o comando 
```
poetry update
```

### Construindo Gitignore

No site https://www.toptal.com/developers/gitignore/api/python podemos obter um gitignore cofigurado para projetos python.

### Fazendo a documentação

Crie uma pasta docs e dentro dela de o comando 
```
sphinx-quickstart
```
Apos isto a pasta docs tera a seguinte estrutura
```
├── _build
├── conf.py
├── index.rst
├── make.bat
├── Makefile
├── _static
└── _templates
```
No arquivo config.py insira 
```
import os
import sys
sys.path.insert(0, os.path.abspath('..'))
```
e 
```
extensions = [ 'sphinx.ext.autodoc',
               'sphinx.ext.doctest',
               'sphinx.ext.intersphinx'
              ]
```
Então na raiz do projeto podemos dar o comando
```
sphinx-apidoc -o docs/ source/
```
Isso ira gerar dois arquivos: source.rst e modules.rst. Adicone eles (sem o final .rst) em docs/index.rst. 

Para visualizar o arquivo html na pasta docs de o comando
```
make html
```
Note que ao adicionar novos codigos e modulos sera necessario atualizar tais arquivos .rst dando navamente o comando sphinx-apidoc -o docs/ source/

Com o proposito de que tal template seja utilizado para o desenvolvimento de projetos gerais, com exceção dos arquivos docs/config.py e docs/index.rst (sem adicionar os arquivos .rst gerados anteriormente) não serão comitados.

## Conteinerização do template

A seguir vamos conteinerizar a aplicação, adiconando um dockerfile e um docker compose. 


