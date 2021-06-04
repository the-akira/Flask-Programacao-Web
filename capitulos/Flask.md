# Flask

Este projeto tem como objetivo apresentar o microframework Flask e suas principais características que nos possibilitam desenvolver aplicações web completas e **[escaláveis](https://www.romexsoft.com/blog/scalable-website/)**.

## Introdução

**[Flask](https://flask.palletsprojects.com/en/1.1.x/)** foi criado por Armin Ronacher, seu lançamento oficial ocorreu no ano de 2010. Por ser pequeno, leve e minimalista, ele é conhecido como um microframework Python, mas isso não quer dizer que ele seja somente limitado a aplicações pequenas, muito pelo contrário, Flask pode ser utilizado em grandes aplicações que escalam, sua fama de microframework ocorre pelo fato de Flask manter o núcleo de uma aplicação simples, porém extensível, Flask não tem construído uma camada de abstração para banco de dados, nem mesmo validação para formulários, porém Flask suporta **extensões** que são capazes de adicionar todas essas funcionalidades para nossa aplicação.

Flask na verdade é a união de dois frameworks muito populares.

1. **[Werkzeug](https://palletsprojects.com/p/werkzeug/)**: Uma biblioteca **[WSGI (Web Server Gateway Interface)](https://en.wikipedia.org/wiki/Web_Server_Gateway_Interface)** para Python, que inclui sistema de rotas de URL, objetos *request* e *response* com características completas e um poderoso *debugger*.
2. **[Jinja2](https://palletsprojects.com/p/jinja/)**: Um **[Template Engine](https://en.wikipedia.org/wiki/Template_processor)** completo para Python

Como já vimos, tarefas de alto-nível como acesso ao banco de dados, formulários web e autenticação de usuários são suportados através de **'extensões'**.

Dentro do escopo da arquitetura **[MVC (Model-View-Controller)](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)**, Werkzeug abrange o Controller (C) e Jinja2 abrange a View (V). Flask não fornece um Model (M) integrado, ele deixa você escolher sua solução de banco de dados favorita. Uma escolha muito popular é **[Flask-SQLAlchemy](https://flask-sqlalchemy.palletsprojects.com/en/2.x/)** que traz um **[ORM (Object-Relational Mapper)](https://en.wikipedia.org/wiki/Object-relational_mapping)** sob um banco de dados relacional como MySQL ou PostgreSQL.

Sumarizando, o microframework Flask é capaz de nos fornecer:

- Uma interface em conformidade com o **WSGI**
- Sistema de Rotas de URL e Request/Response
- Cookies e Sessions Seguros
- Servidor Web de desenvolvimento e Debugger construídos
- Cliente de Unit Test para testes unitários 
- Templates Jinja2 (tags, filtros, macros, etc)
- Extensiva Documentação
- Extensões disponíveis para aperfeiçoar e agilizar o desenvolvimento

Através de Flask você pode lidar com requisições HTTP e AJAX, rotear requests para um controller, avaliar e validar dados requisitados e também fornecer respostas com **HTML** ou **JSON**.

Você pode conferir projetos e corporações que usam Flask em: **[flask-powered](https://github.com/rochacbruno/flask-powered)**

## Instalando Flask

Para instalarmos Flask é necessário que tenhamos Python em nosso computador, caso você ainda não o tenha, faça o download através do **[Website Oficial](https://www.python.org/downloads/)** e você estará pronto para começar.

Agora abra a sua **[Interface de Linha de Comandos](https://en.wikipedia.org/wiki/Shell_(computing))** ![terminal](https://i.imgur.com/XYkmrFY.png) e digite:

```
python --version
```

Nos será retornada a versão atual do Python em nosso computador, em nosso caso estamos lidando com a versão `3.7.1`. Agora que já temos o Python em nossa máquina, podemos utilizar o **[pip](https://pypi.org/project/pip/)** para instalarmos o Flask. **Pip** é um gerenciador de pacotes Python que nos permite instalar diversos módulos escritos por outros desenvolvedores, ele já vem automaticamente instalado com Python, porém caso você tenha algum problema, poderá obtê-lo em: **[pip](https://pypi.org/project/pip/)**.

Antes de instalarmos Flask é importante termos em mente o conceito de Ambientes Virtuais, que nos permitem isolar os pacotes utilizados em nosso projeto para que possamos evitar conflitos de versões com nosso ambiente global. A ferramenta **[virtualenv](https://virtualenv.pypa.io/en/latest/)** nos auxilia nesse processo, para obtermos ela é muito simples:

```
pip install virtualenv
```

Tendo a ferramenta **virtualenv** em nossa máquina, podemos agora criar um Ambiente Virtual, crie um diretório com o nome de sua escolha, em nosso caso será `Exemplo` e **dentro** dele digite o seguinte comando

```
python -m venv ambiente
```

Será criado para nós um diretório com o nome `ambiente`, este que representará o nosso ambiente virtual, para ativarmos ele digitamos o seguinte comando

```
source ambiente/bin/activate
```

ou

```
. ambiente/bin/activate
```

Para desativarmos basta digitarmos `deactivate` e o ambiente será desativado, por hora vamos mantê-lo ativo, pois iremos instalar a Biblioteca Flask através do **pip**:

```
pip install flask
```

Depois dessa rápida instalação, agora já temos Flask disponível para trabalharmos em nossa máquina. Antes de iniciarmos nossos experimentos com Flask, vamos experimentar alguns comandos **pip** importantes

Listando Pacotes Instalados no Ambiente Virtual

```
pip list
```

Nos será trazida a seguinte resposta:

```
Click        7.0 
Flask        1.1.1 
itsdangerous 1.1.0 
Jinja2       2.10.1 
MarkupSafe   1.1.1 
pip          10.0.1 
setuptools   39.0.1 
Werkzeug     0.15.5
```

Criando um arquivo `requirements.txt`

Através do **pip** é possível criarmos um arquivo `requirements.txt` que servirá como o guardião dos pacotes necessários para que nossa aplicação funcione corretamente. Caso alguma pessoa queira utilizar nossa aplicação, ela poderá instalar os requerimentos através desse arquivo. Para criarmos ele devemos digitar o seguinte comando:

```
pip freeze > requirements.txt
```

Nos será criado um arquivo `requirements.txt` com o seguinte conteúdo

```
Click==7.0
Flask==1.1.1
itsdangerous==1.1.0
Jinja2==2.10.1
MarkupSafe==1.1.1
Werkzeug==0.15.5
```

Para instalar os pacotes contidos no arquivo `requirements.txt` podemos utilizar o comando

```
pip install -r requirements.txt
```

Caso tenha interesse em se aprofundar mais na questão dos Ambientes Virtuais, você pode visitar os guias: **[Gerenciadores de Pacotes e Ambientes Virtuais](https://github.com/the-akira/Python-Iluminado/blob/master/Capitulos/29.PIP.md)** e **[Environment](https://exploreflask.com/en/latest/environment.html#)**

## Flask Hello World

Agora que já temos nosso Ambiente Virtual funcionando corretamente e Flask está instalado em nosso Ambiente isolado, vamos criar nosso primeiro **Hello World**. Para isso, vamos criar em nosso diretório `Exemplo` o arquivo `app.py` que terá o seguinte conteúdo

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello World!'

if __name__ == '__main__':
  app.run()
```

1. Começamos importando a classe Flask. Uma instância dessa classe será nossa aplicação **WSGI**
2. Em seguida nós criamos uma instância dessa classe. O primeiro argumento é o nome do módulo da aplicação ou pacote. Se você estiver utilizando um único módulo (como em nosso exemplo) você deve usar `__name__` porque dependendo se é iniciado como aplicativo ou importado como módulo, o nome será diferente, isso é necessário para que Flask saiba onde buscar Templates e arquivos Static.
3. Nós usamos o **[decorador](https://realpython.com/primer-on-python-decorators/)** **route()** para dizer ao Flask qual URL irá ativar nossa função.
4. À função é dado um nome no qual também é utilizado para gerar URL's para essa função em particular, e retorna a mensagem que desejamos apresentar no **[browser](https://en.wikipedia.org/wiki/Web_browser)** do usuário.
5. O bloco **if** é basicamente uma forma de Python dizer 'Execute esse código apenas se eu rodá-lo'. Como vimos antes `__name__` é uma variável que Python cria automaticamente e ela é igual a `__main__` quando somos nós que rodamos o código.

Em resumo podemos dizer que:

```python
# Importamos e criamos nosso app
from flask import Flask
app = Flask(__name__)

# Especificamos nossa rota, sua função e seu retorno
@app.route('/')
def hello_world():
    return 'Hello World!'

# Roda o nosso app
if __name__ == '__main__':
  app.run()
```

Agora finalmente podemos executar nossa aplicação através do comando `python app.py`. Por padrão Flask irá utilizar a porta 5000 de nosso localhost, então navegue até `http://127.0.0.1:5000/` para ver nossa aplicação funcionando. Perfeito, conseguimos executar nossa primeira aplicação em Flask, é incrível como em poucas linhas de códigos conseguimos obter este resultado. Flask é realmente poderoso.

Caso queiramos disponibilizar nossa aplicação para outras pessoas em nossa rede, podemos passar o parâmetro **host** para a função **run()**, vejamos

```python
if __name__ == '__main__':
    app.run(host='0.0.0.0')
```

Para alterarmos a porta em que a aplicação é disponibilizada passamos o parâmetro **port** para a função **run()**

```python
if __name__ == '__main__':
    app.run(port=3535)
```

Nossa aplicação agora estará rodando na porta **3535**. Por fim, mas não menos importante, podemos ativar o modo **debug** para facilitar o processo de desenvolvimento de nossas aplicações.

```python
if __name__ == '__main__':
    app.run(debug=True)
```

Atribuindo **debug** como **True** nos permite um maior controle sobre os erros que ocorrem durante o desenvolvimento, porém é importante lembrarmos que devemos utilizar somente durante essa fase, jamais devemos utilizar em produção.

Você pode ver o exemplo em tempo real acessando: https://repl.it/@theakira/Flask-Introducao

## Sumarizando

Neste capítulo aprendemos

- Um pouco sobre a história do microframework Flask
- Instalação do Flask
- Manipulando Ambientes Virtuais
- Criação de uma simples aplicação `Hello World` 
- Conhecimento básico da estrutura do Flask