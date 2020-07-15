# Formulários Web

---------------------------------------

## Introdução

**[Formulários HTML](https://en.wikipedia.org/wiki/Form_(HTML))** são um dos principais pontos de **interação** entre um usuário e um site ou aplicativo. Eles permitem que os usuários enviem dados para nossa aplicação. Na maioria das vezes, os dados são enviados para o servidor Web, mas a página Web também pode interceptá-los para usá-los por conta própria.

Um formulário HTML é feito de um ou mais *widgets*. Esses *widgets* podem ser campos de texto (linha única ou multilinha), caixas de seleção, botões, caixas de checagem ou botões de opção. Na maioria das vezes, esses widgets são combinados com um rótulo que descreve sua finalidade - os rótulos implementados adequadamente podem instruir claramente os usuários cegos e com visão sobre o que inserir em uma entrada de formulário.

Veja todos os tipos possíveis de input: **[html_form_input_types](https://www.w3schools.com/html/html_form_input_types.asp)**

A principal diferença entre um formulário HTML e um documento HTML regular é que, na maioria das vezes, os dados coletados pelo formulário são enviados para um servidor Web. Nesse caso, você precisa configurar um servidor da Web para receber e processar os dados, felizmente Flask já traz todos esses elementos para nós.

Exemplo de um simples Formulário Web

![form](https://i.imgur.com/zlB95Sn.png)

Este formulário nos reflete o seguinte código HTML

```html
<form>
  Nome:
  <br>
  <input type="text" name="nome" value="Tetsuo">
  <br>
  Sobrenome:
  <br>
  <input type="text" name="sobrenome" value="Shima">
  <br>
  <input type="submit" value="Enviar">
</form> 
```

Perceba que definimos uma tag `<form>` e dentro dela três tags `<input>`, sendo as duas primeiras com atributos de **type** `text` e a última de **type** `submit`. É essencial que definamos o tipo de nosso input. 

O atributo **name** serve como um identificador para nos referirmos a cada input específico, veja que fornecemos um **name** para nossos dois primeiros input, respectivamente **nome** e **sobrenome**. 

Nesse capítulo não vamos abordar o aspecto **HTML** de forma detalhada, nosso foco é o **Flask**! Caso você queira se aprofundar no **HTML** você pode ler esses dois **importantes** guias:

- [W3Schools - HTML Forms](https://www.w3schools.com/html/html_forms.asp)
- [Developer Mozilla - HTML Forms](https://developer.mozilla.org/en-US/docs/Learn/HTML/Forms)

---------------------------------------

## Básico de Formulários em Flask

Para entendermos o básico de como os formulários funcionam em Flask, vamos agora criar uma nova rota em nossa aplicação, para isso vamos alterar nosso arquivo `app.py` e adicionar o seguinte conteúdo:

```python
@app.route('/formulario')
def formulario():
	return render_template('formulario.html')
```

Agora que temos uma nova rota chamada de **formulario**, vamos então criar um template `formulario.html` dentro de nosso diretório `templates`, o conteúdo dele será:

```html
{% extends 'layout.html' %}

{% block conteudo %}
	<p>Formulário</p>
	<form method="POST">
	  Nome:
	  <br>
	  <input type="text" name="nome">
	  <br>
	  Email:
	  <br>
	  <input type="email" name="email">
	  <br>
	  Senha:
	  <br>
	  <input type="password" name="senha">
	  <br>
	  <input type="submit" value="Enviar">
	</form>
{% endblock %}
```

Executamos nossa aplicação com o comando `python app.py` e navegamos até `http://127.0.0.1:5000/formulario` para ver que tudo está funcionando corretamente, exceto que, podemos perceber que nosso formulário ainda não apresenta nenhuma funcionalidade, caso venhamos a tentar enviar dados através dele, nos será apresentado um erro **Method Not Allowed**, isto ocorre pois nossa aplicação não está apta a receber requisições **[POST](https://en.wikipedia.org/wiki/POST_(HTTP))**. Para resolvermos esse problema vamos alterar nossa função **formulario()** de forma que ela possa capturar esses dados. Para isso vamos editar nosso arquivo `app.py`

Primeiramente vamos começar alterando os **imports** de nossa aplicação

```python
from flask import Flask, render_template, request, redirect
```

- A função **request()** nos possibilitará trabalharmos com os dados enviados através do formulário
- A função **redirect()** nos possibilitará redirecionarmos o usuário para uma URL após a submissão do formulário

Agora vamos alterar a função **formulario()** de forma que possamos receber os dados do usuário

```python
@app.route('/formulario', methods=['GET','POST'])
def formulario():
	if request.method == 'POST':
		req = request.form

		nome = req['nome']
		email = req.get('email')
		senha = request.form['senha']

		print(nome,email,senha)
		
		return redirect(request.url)
		
	return render_template('formulario.html')
```

- Adicionamos um **if** que será avaliado como **True** caso seja uma requisição POST
- Uma vez que a requisição é POST, vamos guardar na variável **req** uma estrutura de dados chamada de <class 'werkzeug.datastructures.ImmutableMultiDict'> que é muito similar a um dicionário Python, através dessa estrutura vamos poder acessar cada campo de nosso formulário.
- Através de **req['nome']** acessamos o campo nome e guardamos na variável **nome**
- Através de **req.get('email')** acessamos o campo email e guardamos na variável **email**
- Através de **request.form['senha']** acessamos o campo senha e guardamos na variável **senha**
- Utilizamos a função **print()** para imprirmirmos os dados enviados pelo usuário (**Importante**: Os dados serão impressos em nosso terminal, onde nosso servidor está executando)
- Por fim retornamos o usuário para a mesma URL através da função **redirect()** passando como parâmetro **request.url** que representa a própria URL da página.
- Se eventualmente não estivermos lidando com uma requisição POST, significa que o usuário fez uma requisição GET, em outras palavras, ele deseja ver o conteúdo da página, nesse caso renderizamos o template através da função **render_template()**

Momento de testarmos nossa aplicação, vamos executá-la através do comando `python app.py` e vamos visitar a URL: `http://127.0.0.1:5000/formulario`. Se fizermos uma submissão do formulário poderemos observar que eles serão impressos em nosso terminal, muito legal, não? Agora já conseguimos capturar os dados enviados através de formulários por usuários! 

Vamos agora aperfeiçoar nosso conhecimento e trabalhar com a biblioteca **[Flask-WTF](https://flask-wtf.readthedocs.io/en/stable/)**, esta que nos possibilitará o gerenciamento de nossos formulários de forma muito mais simples e amigável e também irá colaborar com a segurança de nossa aplicação.

---------------------------------------

## Formulários com Flask-WTF

Para podermos utilizar a Biblioteca **Flask-WTF** em nossa aplicação é necessário instalarmos ela, recomendamos você fazer a instalação dela com o seu Ambiente Virtual ativado, sendo assim, execute este comando

```
pip install flask-wtf
```

Uma vez instalada com sucesso, agora podemos utilizar ela em nosso projeto, vamos então fazer algumas alterações em nosso arquivo `app.py`, porém, antes disso é importante dizermos que embora a estrutura de nossa aplicação pareça um pouco desorganizada, é porque ainda não entramos no aspecto de como estruturar nossa aplicação, por enquanto estamos focando apenas nos fundamentos, que são essenciais para executarmos diversos trabalhos, sendo assim vamos continuar trabalhando no arquivo `app.py`

Primeiramente vamos alterar nossos **imports** para que possamos trabalhar com novas funcionalidades

```python
from flask import Flask, render_template, request, redirect, session, url_for
from flask_wtf import FlaskForm 
from wtforms import StringField, BooleanField, SelectField, TextField, TextAreaField, SubmitField
from wtforms.validators import DataRequired
```

- Na primeira linha importamos **session** que nos permitará trabalhar com variáveis de sessão temporárias e a função **[url_for()](https://flask.palletsprojects.com/en/1.0.x/api/#flask.url_for)** que possibilita construirmos uma URL para uma função específica de nossa aplicação
- Na segunda linha importamos **FlaskForm** de **wtforms** que será essencial para construirmos nosso Formulário
- Na terceira linha importamos todos os campos que vamos utilizar em nosso formulário
- Por fim, na quarta linha importamos o validador **DataRequired** que irá indicar que determinado campo deve ser obrigatoriamente preenchido

Agora que todos os nossos **imports** estão organizados corretamente é o momento de definirmos o nosso formulário que será representado através de uma Classe Python que chamaremos de **LivroForm**, novamente vamos editar o arquivo `app.py`

```python
app.config['SECRET_KEY'] = 'chavesecreta'

class LivroForm(FlaskForm):
	titulo = StringField('Titulo do Livro', validators=[DataRequired()])
	autor = StringField('Nome do Autor', validators=[DataRequired()])
	vivo = BooleanField('Autor está Vivo?')
	genero = SelectField('Gênero:',choices=[('aventura','Aventura'),('cientifico','Cientifico'),('romance','Romance')])
	resumo = TextAreaField()
	submit = SubmitField('Enviar')
```

1. Iniciamos atribuindo o valor 'chavesecreta' à nossa variável de configuração **SECRET_KEY**, ela irá nos auxiliar na proteção contra ataques **[Cross-Site Request Forgery](https://en.wikipedia.org/wiki/Cross-site_request_forgery)**, importante lembrarmos que não é uma boa prática o que estamos fazendo, o ideal é guardarmos esses dados em uma **[Environment Variable](https://en.wikipedia.org/wiki/Environment_variable)**, vamos deixar dessa forma para simplicidade, uma vez que nosso objetivo é entender os fundamentos.
2. Através da palavra-chave **class** definimos nosso formulário com o nome de **LivroForm** que recebe como parâmetro **FlaskForm**, este que importamos mais cedo
3. Definimos a variável **titulo** que será um Campo de String com a label 'Titulo do Livro', veja que utilizamos o validador **DataRequired()** para indicar que o campo deve ser obrigatoriamente preenchido
4. Definimos a variável **autor** que será um Campo de String com a label 'Nome do Autor', veja que utilizamos o validador **DataRequired()** para indicar que o campo deve ser obrigatoriamente preenchido
5. Definimos a variável **vivo** que será um Campo Boolean com a label 'Autor está Vivo?'
6. Definimos a variável **genero** que será um Campo de Seleção contendo as opções 'Aventura', 'Cientifico' e 'Romance'
7. Definimos a variável **resumo** que será um Campo de Área de Texto
8. Por fim definimos a variável **submit** que representará nosso botão de submissão contendo a label 'Enviar'

Agora que temos nosso formulário definido, vamos definir **duas novas rotas** para experimentarmos. Novamente editamos nosso arquivo `app.py`:

```python
@app.route('/wtf', methods=['GET','POST'])
def wtf():
	form = LivroForm()
	if form.validate_on_submit():
		session['titulo'] = form.titulo.data 
		session['autor'] = form.autor.data 
		session['vivo'] = form.vivo.data 
		session['genero'] = form.genero.data 
		session['resumo'] = form.resumo.data 

		return redirect(url_for('obrigado'))

	return render_template('wtf.html', form=form)

@app.route('/obrigado')
def obrigado():
	return render_template('obrigado.html')
```

- A primeira rota **wtf()** vai processar o formulário (caso seja uma requisição POST) ou vai renderizar o formulário (caso seja uma requisição GET)
- A segunda rota **obrigado()** vai ser ativada através da função **redirect()**, nela iremos exibir os dados submetidos pelo usuário através das variáveis de sessão
- A variável **form** irá guardar o objeto que representa nosso Formulário, nesse caso **LivroForm**
- Perceba que as variáveis serão atribuídas somente se o formulário for válido, por isso utilizamos o método **validate_on_submit()**
- Caso o formulário não seja válido ou a requisição seja **GET** vamos apenas renderizar o template com o formulário

Agora precisamos criar nossos templates para que possamos exibir o formulário e os dados submetidos, para isso vamos criar os arquivos `wtf.html` e `obrigado.html` na pasta `templates`

`wtf.html`

```html
<h1>Cadastro de Livros Flask-WTF</h1>

<form method="POST">
	{{ form.hidden_tag() }}
	{{ form.titulo.label }} {{ form.titulo() }}
	<br>
	{{ form.autor.label }} {{ form.autor() }}
	<br>
	{{ form.vivo.label }} {{ form.vivo() }}
	<br>
	{{ form.genero.label }} {{ form.genero() }}
	<br>
	<p>Resumo da Obra</p>
	<br>
	{{ form.resumo() }}
	<br>
	{{ form.submit() }}
</form>
```

- Este template espera um objeto **form** que foi instanciado de **LivroForm()** como já vimos anteriormente
- O elemento HTML `<form>` é usado como um container para o formulário web, como também já vimos
- O método **form.hidden_tag()** nos gera um campo escondido que inclui um token que será utilizado para nos proteger contra ataques Cross-Site Request Forgery, tudo que precisamos para ter nosso formulário protegido é incluir esse campo e termos uma **SECRET_KEY** definida
- Por fim renderizamos todos os labels e formulários em nossa página

Por fim vamos editar nosso arquivo `obrigado.html` com o seguinte conteúdo

```html
<h1>Obrigado por cadastar o livro em nossa aplicação</h1>

<ul>
	<li>Título: {{ session['titulo'] }}</li>
	<li>Autor: {{ session['autor'] }}</li>
	<li>Vivo: {{ session['vivo'] }}</li>
	<li>Genero: {{ session['genero'] }}</li>
	<li>Resumo: {{ session['resumo'] }}</li>
</ul>
```

- Através da tag `<h1>` criamos um simples cabeçalho para este template
- Através da tag `<ul>` criamos uma lista não-ordenada contendo cinco elementos `<li>`
- Através da variável temporária podemos exibir os dados enviados através do formulário

Finalmente podemos executar nossa aplicação com o comando `python app.py`, vamos navegar até o endereço `http://127.0.0.1:5000/wtf` para vermos nosso formulário. Sinta-se a vontade para experimentar com as submissões e testar o formulário e para se aprofundar nas ferramentas visite suas respectivas documentações: **[WTForms](https://wtforms.readthedocs.io/en/stable/)** e **[Flask-WTF](https://flask-wtf.readthedocs.io/en/stable/)**

Para experimentar o código deste tutorial: https://repl.it/@theakira/Flask-Formularios-Web

---------------------------------------

## Sumarizando

Neste capítulo estudamos conceitos básicos de formulários, bem como a utilização deles em Flask. Através da biblioteca Flask-WTF aprendemos a criar formulários de uma forma mais eficaz e segura. Uma vez que adquirimos a habilidade de capturar os dados dos usuários, agora nosso caminho é guardá-los em um banco de dados que possa persistí-los e não somente armazená-los em variáveis de sessão.

---------------------------------------
