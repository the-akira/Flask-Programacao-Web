# Flask Referência

Flask é um **microframework** [WSGI](https://wsgi.readthedocs.io/) para desenvolvimento de aplicações web. É projetado para você começar desenvolvendo rápido e com facilidade. Com a possibilidade de escalar em aplicações complexas, sua história começou como um simples *wrapper* sob [Werkzeug](https://palletsprojects.com/p/werkzeug) e [Jinja](https://palletsprojects.com/p/jinja) e se tornou um dos frameworks Python de aplicações web mais popular.

Flask oferece sugestões, porém não força nenhuma dependência ou *layout* de projeto, em outras palavras, te dá liberdade total. É importante lembrar que existem muitas extensões disponíveis para Flask, que tornam a possibilidade de adicionar novas funcionalidades em nossa aplicação muito mais fácil.

- Website Oficial: [Flask](https://palletsprojects.com/p/flask/)
- Documentação: [Flask Documentação](https://flask.palletsprojects.com/en/1.1.x/)

## Instalando Flask

```python
pip install Flask
```

## Aplicação Simples

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello World'
```

## Rotas

```python
@app.route('/teste/<string:nome>') # exemplo.com/teste/Gabriel
def teste(nome):
    return 'Hello ' + nome # nos retornará Hello Gabriel
```

## Métodos Request Permitidos

```python
@app.route('/teste') # Padrão, apenas permite requisições GET
@app.route('/teste', methods=['GET', 'POST']) # Permite apenas os métodos GET e POST
@app.route('/teste', methods=['PUT']) # Permite apenas o método PUT
```

## Configuração

```python
app.config['CONFIG_NAME'] = 'valor' # Acesso direto à configuração
app.config.from_envar('NOME_ENV_VAR') # Importando de uma variável de ambiente exportada com o caminho para o arquivo
```

## Templates

```python
from flask import render_template

@app.route('/')
def index():
    return render_template('index.html', variavel=variavel)
```

## Respostas em JSON

```python
import jsonify

@app.route('/arquivojson')
def arquivojson():
    lista = [1,2,3,4,5]
    dict = {'números': lista, 'nome': 'Números'}

    return jsonify({'output': dict})
```

## Acessando Dados de Request

```python
request.args['nome'] # acessando argumentos query
request.form['nome'] # acessando dados de formulário
request.method # tipo de requisição
request.cookies.get('nome_do_cookie') # acessa dados de cookies
request.session['nome'] # acessa dados sobre a sessão
request.files['nome'] # acessa dados de arquivos
```

## Redirecionando

```python
from flask import url_for, redirect

@app.route('/home')
def home():
    return render_template('home.html')

@app.route('/redirecionar')
def redirecionar():
    return redirect(url_for('home'))
```

## Abortando

```python
from flask import abort

@app.route('/')
def index():
    abort(404) # Retorna o erro 404
    render_template('index.html') # Essa linha nunca é executada
```

## Setando Cookies

```python
from flask import make_response

@app.route('/')
def index():
    resposta = make_response(render_template('index.html'))
    resposta.set_cookie('nome_do_cookie', 'valor_do_cookie')
    return resposta
```

## Lidando com Sessões

```python
import session

app.config['SECRET_KEY'] = 'qualquer string aleatória'

@app.route('/login_sucesso') # Seta a sessão
def login_sucesso():
    session['nome_da_chave'] = 'valor_da_chave' # Guarda um cookie seguro no browser
    return redirect(url_for('index'))

@app.route('/') # Sessão de leitura
def index():
    if 'nome_da_chave' in session:
        # Sessão existe e há uma chave
        session_var = session['valor_da_chave']
    else:
        # Sessão não existe	
```

## Estruturando uma Aplicação com Blueprints

A árvore seguinte representa um projeto com o nome de **projeto**

```
run.py
projeto/
    __init__.py
    config.py
    forms.py
    models.py
    admin/
        __init__.py
        routes.py
    main/
        __init__.py
        routes.py
    templates/
        index.html
    static/
        css/
            style.css
```

### Em `run.py`

```python
from projeto import app

if __name__ == '__main__':
    app.run()
```

### Em `projeto/__init__.py`

```python
from flask import Flask
from project.main.routes import main
from project.admin.routes import admin

app = Flask(__name__)

app.register_blueprint(main, url_prefix='/')
app.register_blueprint(admin, url_prefix='/admin')
```

### Em `projeto/main/routes.py`

```python
from flask import Blueprint

main = Blueprint('main', __name__)

@main.route('/')
def index():
    return "Hello, World! Essa é a página principal."
```

### Em `projeto/admin/routes.py`

```python
from flask import Blueprint

admin = Blueprint('admin', __name__)

@main.route('/')
def index():
    return "Hello, World! Essa é a página de administração."
```

## Utilizando Jinja2 (Template Engine)

Renderizando um template através de `main/routes.py`:

```python
from flask import Blueprint, render_template

@main.route('/')
def home():
    # Define uma lista de dicionários posts
    posts = [
        {
            "texto": "Hello, this is a post",
            "data": "This is a date and time",
        }
    ]
	
    # Passa a lista de dicionários para o template HTML
    return render_template("home.html", posts=posts)
```

Utilizando Jinja2 dentro de `templates/home.html`:

```html
{% for posts in posts %}
    <div>
        <p>{{ post.texto }}</p>
        <p>{{ post.data }}</p>
    </div>
{% endfor %}
```

Utilizando filtros:

```
{{ post.texto|upper }}
```

Utilizando **extends**:

```html
{% extends 'base.html' %}

{% block content %}
<p>
    Hello, world!
</p>
{% endblock %}
```

Em `base.html` vamos definir nossa estrutura base:

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width initial-scale=1">
    </head>
    <body>
        {% blockcontent %}{% endblock %}
    </body>
</html>
```

Utilizando **include** para adicionar arquivos parciais:

```
{% include '_post.html' %}
```

Adicionando **folhas de estilo** para `templates/base.html`:

```html
<link rel="stylesheet" href="{{ url_for('static', filename='css/style.css') }}">
```

## Criando Modelos com SQLAlchemy

Em `projeto/models.py`:

```python
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()

class Post(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    body = db.Column(db.String(256))

    def __init__(self, body):
        self.body = body

    def __repr__(self):
        return "<Post({})>".format(self.id)
```

Em `projeto/__init__.py`:

```python
from projeto.models import db

app = Flask(__name__)
app.config.from_object(config.DevelopmentConfig)
with app.app_context():
    db.init_app()
```

Em `projeto/config.py`:

```python
import os
BASE_DIR = os.path.abspath(os.path.dirname(__name__))

class BaseConfig(object):
    # ...
    SQLALCHEMY_DATABASE_URI = 'sqlite:///' + os.path.join(BASE_DIR, 'data.db')
    SQLALCHEMY_TRACK_MODIFICATIONS = False
```

Criando a Base de Dados e interagindo com os modelos e *queries* dentro de uma [shell](https://en.wikipedia.org/wiki/Shell_(computing))

```
>>> from projeto import app, db
>>> from projeto.models import Post
>>> app.app_context().push()
>>> db.create_all()
>>> p = Post('Hello!')
>>> db.session.add(p)
>>> db.session.commit()
>>> posts = Post.query.all()
```

## Plugins Importantes

- [Flask-PyMongo](https://flask-pymongo.readthedocs.io/en/latest/)
- [Flask-SQLAlchemy](https://pypi.org/project/Flask-SQLAlchemy/)
- [Flask-Mail](https://pythonhosted.org/Flask-Mail/)
- [Flask-RESTful](https://flask-restful.readthedocs.io/en/latest/)
- [Flask-Uploads](https://flask-uploads.readthedocs.io/en/latest/)
- [Flask-WTF](https://flask-wtf.readthedocs.io/en/stable/)
- [Flask-User](https://flask-user.readthedocs.io/en/latest/)
- [Flask-Login](https://flask-login.readthedocs.io/en/latest/)
