# Templates

## Introdução

Templates são um elemento essencial em Flask e muitos outros frameworks, eles são o que chamamos de camada de apresentação ou *view* de nossa aplicação. Um template é um arquivo que contém textos estáticos, assim como *placeholders* para renderizarmos conteúdo dinâmico.

![templates](https://raw.githubusercontent.com/the-akira/Flask-Programacao-Web/master/imagens/FlaskTemplate.png)

Como podemos ver na ilustração:

- Temos nossos dados armazenados em um banco de dados
- Temos um arquivo template

O template engine conecta com o modelo de dados e processa os códigos específicos nos templates e então direciona o output para um arquivo específico que normalmente é renderizado em um browser de forma que possamos ver a apresentação dos dados.

## Jinja2

Jinja é uma moderna e amigável linguagem de Templates para Python. É veloz, amplamente utilizado e seguro e conta com características como:

- Execução em Sandbox
- Um poderoso sistema automático de *escaping* para prevenção contra ataques **[XSS](https://en.wikipedia.org/wiki/Cross-site_scripting)**
- Herança de Templates
- Compila para código python otimizado *[just in time](https://en.wikipedia.org/wiki/Just-in-time_compilation)*
- Fácil de debugar
- Sintáxe Configurável

Podemos instalar Jinja2 através do comando `pip install Jinja2`, porém uma vez que já instalamos Flask, não precisamos fazê-lo, pois Jinja2 já faz parte do Flask.

É muito importante sabermos que existem dois tipos de delimitadores em Jinja2:

- `{% ... %}`: Usado para executar comandos como **for loops** ou atribuição de valores
- `{{ ... }}`: Usado para imprimirmos o resultado de expressões no template

## Trabalhando com Templates em Flask

Atualmente contamos com um diretório `Exemplos` que contém nosso Ambiente Virtual, o arquivo `requirements.txt` e nossa simples aplicação `app.py` que no momento contém o seguinte código:

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello World!'

if __name__ == '__main__':
  app.run(debug=True)
```

Dentro de nosso diretório `Exemplos` vamos agora criar um novo diretório chamado de `templates` e nele vamos criar um arquivo `index.html` com o seguinte conteúdo:

```html
<p>Nosso primeiro Template</p>
```

Agora vamos modificar nosso arquivo `app.py` de forma que possamos servir esse template.

```python
from flask import Flask, render_template
app = Flask(__name__)

@app.route('/')
def index():
    return render_template('index.html')

if __name__ == '__main__':
  app.run(debug=True)
```

Observe que tivemos que importar a função **render_template()** para podermos trabalhar com os templates. 

- Essa função recebe como argumento o **nome do template** a ser renderizado ou um iterável com nomes de templates, em que o primeiro será renderizado.
- O **contexto**, em outras palavras: as variáveis que estarão disponíveis no contexto do template.

Para saber mais detalhes sobre a função visite: **[flask.render_template](https://flask.palletsprojects.com/en/1.1.x/api/#flask.render_template)**

Agora execute a aplicação com o comando `python app.py` e visite `http://127.0.0.1:5000/` para ver o resultado.

## Passando Variáveis para o Template

Até então renderizamos um arquivo HTML estático, o que já nos fornece bastante flexibilidade para criarmos páginas web, porém o grande poder dos templates está em sua capacidade de receber variáveis para que possamos apresentar os dados para os usuários que irão visualizar nossa aplicação. 

Vamos alterar nossos arquivos `app.py` e `index.html` para vermos um simples exemplo

```python
from flask import Flask, render_template
app = Flask(__name__)

@app.route('/')
def index():
	nome = 'Gabriel'
	return render_template('index.html', nome=nome)

if __name__ == '__main__':
  app.run(debug=True)
```

```
<p>Olá {{ nome }}</p>
```

Novamente executamos nossa aplicação através do comando `python app.py` e podemos ver o resultado que nos é retornado.

### Trabalhando com Estruturas de Dados, Funções e Classes

Vamos agora criar uma nova rota e um novo template de forma que possamos trabalhar com estruturas variadas. Em nosso diretório **templates** vamos criar o arquivo `jinja.html`, no momento ele terá o seguinte conteúdo

```html
<h1>Jinja Template</h1>
```

Agora vamos alterar nosso arquivo `app.py` de forma que possamos servir esse novo template

```python
from flask import Flask, render_template
app = Flask(__name__)

@app.route('/')
def index():
	nome = 'Gabriel'
	return render_template('index.html', nome=nome)

@app.route('/jinja')
def jinja():
	return render_template('jinja.html')

if __name__ == '__main__':
  app.run(debug=True)
```

Executamos nossa aplicação e agora podemos navegar até `http://127.0.0.1:5000/jinja` para ver o resultado. Agora vamos criar alguns dados para experimentarmos em nosso template, novamente vamos editar o arquivo `app.py` com o seguinte conteúdo:

```python
from flask import Flask, render_template
app = Flask(__name__)

@app.route('/')
def index():
	nome = 'Gabriel'
	return render_template('index.html', nome=nome)

@app.route('/jinja')
def jinja():
	num = 10

	langs = ['Python', 'Javascript', 'C', 'C++', 'Bash', 'Haskell']

	personagens = {
		'DBZ': 'Goku',
		'Naruto': 'Kakashi',
		'Death Note': 'Raito Yagami',
		'Akira': 'Tetsuo',
		'Pokémon': 'Ash'
	}

	cores = ('red', 'green', 'blue')

	online = True

	class Aplicacao:
		def __init__(self, nome, descricao, url):
			self.nome = nome
			self.descricao = descricao
			self.url = url 

		def nome(self):
			return f'O nome da aplicação é {self.nome}'

		def endereco(self):
			return f'Você pode acessar a aplicação atraves do endereço: {self.url}'

	app = Aplicacao(nome='Flask Programação Web', descricao='Tutorial Completo', url='https://github.com/the-akira')

	def cubo(x):
		return x**3

	html = "<h1>Titulo Grande</h1>"

	malicioso = "<script>alert('Voce foi hackeado!')</script>"

	return render_template(
		'jinja.html', 
		num=num,
		langs=langs,
		personagens=personagens,
		cores=cores,
		online=online,
		Aplicacao=Aplicacao,
		cubo=cubo,
		app=app,
		html=html,
		malicioso=malicioso
	)

if __name__ == '__main__':
  app.run(debug=True)
```

Podemos observar que:

1. Atribuimos o valor 10 à variável **num**
2. Criamos uma lista **langs** com importantes linguagens de programação
3. Criamos um dicionário **personagens** com personagens de animes
4. Criamos uma tupla **cores** com os valores **[RGB](https://www.w3schools.com/colors/colors_rgb.asp)**
5. Atribuimos o valor **True** à variável **online**
6. Definimos uma classe **Aplicacao** que modela uma Aplicação
7. Construimos um objeto **app** através do Construtor **Aplicacao()**
8. Definimos uma função **cubo()** que eleva um número passado por parâmetro ao cubo
9. Atribuimos uma String contendo HTML à variável **html**
10. Atribuimos um String contendo um Script à variável malicioso
11. Por fim, retornamos a função **render_template()** que recebe o nosso template `jinja.html` e todas as variáveis que definimos

Agora que temos acesso a todos esses dados em nosso template, podemos editar nosso arquivo `jinja.html` para apresentarmos os dados em nossa página.

```html
<h1>Jinja Template</h1>
<hr>

<h3>1. Variáveis</h3>
<p>Número: {{ num }}</p>

<hr>

<h3>2. Percorrendo a lista com o For Loop</h3>
<ul>
	{% for lang in langs %}
		<li>{{ lang }}</li>
	{% endfor %}
</ul>

<hr>

<h3>3. Percorrendo a lista com índices através do For Loop</h3>
<ul>
	{% for lang in langs %}
		<li>{{ loop.index }} : {{ lang }}</li>
	{% endfor %}
</ul>

<hr>

<h3>4. Percorrendo dicionário com o For Loop</h3>
<ul>
	{% for k, v in personagens.items() %}
		<li>{{ k }} : {{ v }}</li>
	{% endfor %}
</ul>

<hr>

<h3>5. Acessando um Membro da Lista</h3>
<p>{{ langs[0] }}</p>

<hr>

<h3>6. Acessando um Membro do Dicionário</h3>
<p>{{ personagens['Akira'] }}</p>	

<hr>

<h3>7. Acessando Atributos de Classe</h3>
<p>{{ app.nome }}</p>

<hr>

<h3>8. Desempacotando Variáveis</h3>
<p>{% set a, b, c = cores %}</p>	
<p>{{ a }}</p>
<p>{{ b }}</p>
<p>{{ c }}</p>

<hr>

<h3>9. Chamando uma Função</h3>
<p>{{ cubo(3) }}</p>
<p>{{ cubo(2) }}</p>

<hr>

<h3>10. Chamando um Método de Classe</h3>
<p>{{ app.endereco() }}</p>

<hr>

<h3>11. Condicionais</h3>
{% if online %}
	<p>Usuário se encontra online</p>
{% endif %}

<hr>

<h3>12. Comparações</h3>
{% if num < 18 %}
	<p>Valor menor que 18</p>
{% elif num == 20 %}
	<p>Valor é igual a 20</p>
{% else %}
	<p>Nenhuma das outras opções</p>
{% endif %}

<hr>

<h3>13. Expressões Matemáticas</h3>
<p>{{ num/2 }}</p>
<p>{{ (5*5)/10 }}</p>
<p>{{ 'x'*3 }}</p>
<p>{{ (5+3)*(7-2) }}</p>

<hr>

<h3>14. Filtros de Template</h3>
<p>{{ langs|length }}</p>
<p>{{ langs|lower }}</p>
<p>{{ langs|upper }}</p>

<hr>

<h3>15. Filtros de Template - join</h3>
<p>{{ langs|join(', ') }}</p>	

<hr>

<h3>16. Escaping</h3>
{{ html }}
{{ html|safe }}
{{ malicioso }}
```

Podemos observar que:

1. Imprimimos o valor da variável **num**
2. Percorremos e imprimimos os elementos da lista **lang**
3. Percorremos e imprimimos os elementos da lista **lang** juntamente com seu índice
4. Percorremos o dicionário **personagens** e imprimimos os pares chave:valor
5. Acessamos o primeiro elemento da lista **lang**
6. Acessamos o elemento de chave **Akira** do dicionário **personagens**
7. Acessamos o atributo **nome** do objeto **app**
8. Desempacotamos os valores da tupla **cores** e os acessamos individualmente através das variáveis **a**, **b** e **c**
9. Chamamos a função **cubo()** para calcular o cubo dos valores **3** e **2**
10. Invocamos o método **endereco** através do objeto **app**
11. Aqui utilizamos o condicional **if** que testa se a variável **online** é True, uma vez que ela True, o conteúdo 'Usuário se encontra online' é apresentado
12. Comparamos a variável **num** com diversos valores
13. Testamos diversas expressões matemáticas
14. Utilizamos os **Filtros de Template** para modificar a apresentação de nossos dados
15. Utilizamos o filtro **join()** para transformar nossa lista em uma String
16. Exibimos os valores das variáveis **html** e **malicioso**, perceba que o filtro **safe** nos permite indicar que é seguro renderizar os dados, caso ele não seja utilizado, tags html e scripts não serão renderizados em nossa página por motivos de segurança.

Finalmente vamos executar nossa aplicação através do comando `python app.py` e vamos navegar até `http://127.0.0.1:5000/jinja`. Veja como são exibidos nossos dados e experimente da forma que melhor lhe agrade.

## Herança de Template

A Herança de Template nos permite criar um arquivo que servirá como o **layout** principal de nossa aplicação, de forma que não seja necessário ficarmos repetindo código desnecessariamente. Por exemplo, em aplicações web é muito como a existência de uma barra de navegação que contém diversas rotas, para não precisarmos ficar repetindo blocos html podemos usar o conceito de herança. Para entendermos melhor vamos então criar um arquivo `layout.html` em nosso diretório `templates` com o seguinte conteúdo

```html
<html>
    <head>
        <title>Aplicação Flask</title>
    </head>
    <body>
        Navegar para Jinja: <a href="/jinja">Jinja</a>
        <hr>
        {% block conteudo %}{% endblock %}
    </body>
</html>
```

Veja que utilizamos o comando **block** que define um bloco com o nome **conteudo** arbitrariamente escolhido por nós, este bloco será capaz de receber conteúdo de outros templates. Agora vamos editar nosso arquivo `index.html` para que ele possa herdar as propriedades de nosso `layout.html`

```html
{% extends 'layout.html' %}

{% block conteudo %}<p>Olá {{ nome }}</p>{% endblock %}
```

Através do comando **extends** indicamos que este arquivo irá herdar as propriedades de nosso `layout.html` e dentro do bloco **conteudo** nós definimos o HTML a ser exibido. Lembrando que é necessário fecharmos o bloco com **endblock**. Agora podemos executar nossa aplicação com o comando `python app.py` para vermos o nosso **layout** em ação.

Caso queira experimentar o código em tempo real: https://repl.it/@theakira/Flask-Templates

## Sumarizando

Neste capítulo:

- Estudamos fundamentos sobre Template Engines
- Trabalhamos com Jinja2 em Flask
- Passamos variáveis para nosso Template
- Apresentamos dados em nosso Template
- Criamos um Layout principal para nossa simples Aplicação

Agora que já temos o domínio dos Templates vamos estudar os Arquivos Static que são um aspecto importante em uma aplicação.