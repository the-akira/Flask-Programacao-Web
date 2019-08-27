# Banco de Dados

---------------------------------------

## Introdução

Um **[banco de dados](https://pt.wikipedia.org/wiki/Banco_de_dados)** é uma coleção organizada de dados, geralmente armazenados e acessados eletronicamente através de um sistema de computador.

O **[sistema de gerenciamento de banco de dados (DBMS)](https://pt.wikipedia.org/wiki/Sistema_de_gerenciamento_de_banco_de_dados)** é o software que interage com usuários finais, aplicativos e o próprio banco de dados para capturar e analisar os dados.

**[Conolly e Begg](https://www.pearson.com/us/higher-education/program/Connolly-Database-Systems-A-Practical-Approach-to-Design-Implementation-and-Management-6th-Edition/PGM116956.html)** definem os Sistemas de Gerenciamento de Banco de Dados como um software que habilita usuários a definir, criar, manter e controlar o acesso ao banco de dados.

A funcionalidade provida por uma DBMS pode variar bastante entre cada solução, porém as principais funcionalidades são o armazenamento, recuperação e atualização dos dados.

Em determinado momento muitos aplicações web terão de lidar com banco de dados, como já estudamos, por ser um microframework, Flask não traz consigo um mecanismo para lidarmos com banco de dados, porém existe uma solução muito eficaz chamada **[SQLAlchemy](https://www.sqlalchemy.org/)**, que é um kit de ferramentas SQL para a linguagem Python e um **[Object Relational Mapper](https://en.wikipedia.org/wiki/Object-relational_mapping)** que fornece aos desenvolvedores de aplicação o poder total e flexibilidade de SQL.

Um Object Relational Mapper é basicamente uma camada de abstração que se estabelece no topo de **[consultas SQL brutas](https://www.codecademy.com/articles/sql-commands)** sendo executadas em nosso banco dados. SQLAlchemy nos fornece uma **[API](https://en.wikipedia.org/wiki/Application_programming_interface)** consistente para diversos sistemas de banco de dados, os mais populares incluem MySQL, PostgreSQL e SQLite e também torna fácil movermos dados entre nossos modelos e nosso banco de dados e ainda torna fácil por exemplo a alteração de um sistema de banco de dados e a migração de nossos **[schemas](https://en.wikipedia.org/wiki/Database_schema)**. Em resumo, SQLAlchemy nos possibilitará escrever todo o código em Python, sem a necessidade de escrevermos códigos SQL.

Neste guia específico, vamos utilizar a versão do SQLAlchemy para Flask chamada de **[Flask-SQLAlchemy](https://flask-sqlalchemy.palletsprojects.com/en/2.x/)** que nos possibilitará um início rápido e vamos escolher o sistema gerenciador de banco de dados [SQLite3](https://www.sqlite.org/index.html), que embora não seja uma solução escalável, ela é muito conveniente para pequenas aplicações como a nossa, além disso, em SQLite cada base de dados é armazenada em um único arquivo em disco e não há necessidade de executarmos um servidor de banco de dados como em MySQL e PostgreSQL.

---------------------------------------

## Flask-SQLAlchemy

Flask-SQLAlchemy torna ainda mais simples o uso da biblioteca SQLAlchemy com Flask, para trabalharmos com ela é necessário fazermos sua instalação, com nosso Ambiente Virtual ativo, vamos digitar:

```
pip install flask-sqlalchemy
```

Uma vez instalada com sucesso, agora temos a biblioteca a nossa disposição para trabalharmos. Vamos então criar um novo arquivo chamado `models.py` dentro da pasta `Exemplos` este arquivo que representará um modelo de uma simples biblioteca.

Vamos começar com as configurações básicas

```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy 

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///livros.db'
db = SQLAlchemy(app)
```

Nós primeiramente importamos as bibliotecas necessárias, que nesse caso é o próprio Flask e o Flask-SQLAlchemy, posteriormente criamos nosso objeto **app** e indicamos onde o arquivo do banco de dados deve ficar localizado (root da aplicação). Por fim criamos o objeto **db** que nos permite integrar SQLAlchemy na nossa aplicação Flask.

Agora que temos nossas configurações estabelecidades, vamos começar a trabalhar efetivamente em nosso banco de dados, vamos criar uma classe **Autor** que representará uma tabela Autor em nosso banco de dados, este será nosso modelo.

```python
class Autor(db.Model):
	id = db.Column(db.Integer, primary_key=True)
	nome = db.Column(db.String(30), nullable=False)
	sobrenome = db.Column(db.String(30), nullable=False)
	livros = db.relationship('Livro', backref='autor', lazy=True)

	def __repr__(self):
		return f'Autor("{self.nome}", "{self.sobrenome}")'
```

Começamos definindo o nome de nossa classe como **Autor**, esta que herda de `db.Model`, uma classe base para todos os modelos de Flask-SQLAlchemy. Esta classe define diversos campos como variáveis de classe. Campos são criados como instâncias da classe `db.Column`, no qual recebem o campo como argumento, mais alguns argumentos adicionais como por exemplo `primary_key` que índica o campo como uma chave primária da tabela, que deve ser único.

Nossa tabela então é composta pelos seguintes campos:

- **id**, valor inteiro único que representa nossa chave primária
- **nome**, String de até 30 caracteres que não pode ser nulo
- **sobrenome**, String de até 30 caracteres que não pode ser nulo
- **livros**, Este campo é um relacionamento com a classe **Livro** que vamos criar, o argumento **backref** define o nome do campo que será adicionado aos objetos da classe 'muitos' que apontará de volta para o objeto 'único'. 

Para entendermos melhor, estamos fazendo uma relação em que um **Autor** poderá ter a autoria sob muitos livros, porém um livro pertencerá a um único autor. 

É o que chamamos de relação **[one-to-many](https://en.wikipedia.org/wiki/One-to-many_(data_model))**

- Por fim, o método mágico `__repr__` diz ao Python como imprimir os objetos dessa classe, o que será muito útil caso precisemos debuggar.

Vamos agora criar a Classe que representará a tabela **Livros**

```python
class Livro(db.Model):
	id = db.Column(db.Integer, primary_key=True)
	titulo = db.Column(db.String(100), nullable=False)
	sumario = db.Column(db.String(250), nullable=False)
	autor_id = db.Column(db.Integer, db.ForeignKey('autor.id'), nullable=False)

	def __repr__(self):
		return f'Livro("{self.titulo}")'
```

Como em nossa classe Autor, começamos definindo o nome de nossa classe como **Livro** e herdamos de `db.Model`. Criamos o campo **id** que representa nossa chave primária, o **titulo** de nosso livro que será uma String de até 100 caracteres e o **sumario** que será uma string de até 250 caracteres. O campo **autor_id** é chamado de **ForeignKey**, que seria como uma chave estrangeira para acessarmos o autor do respectivo livro.

- Por fim, o método mágico `__repr__` diz ao Python como imprimir os objetos dessa classe

Agora que nossos modelos estão estabelecidos, é hora de testarmos nosso banco de dados para termos certeza que podemos inserir dados. Dentro de seu diretório `Exemplos` onde reside o arquivo `models.py`, abra o interpretador Python para que possamos trabalhar interactivamente. Para isso digite `python`

Começamos com os imports necessários

```python
>>> from models import db, Autor, Livro
```

Inicializando Banco de Dados e Tabelas

```python
>>> db.create_all()
```

Cadastrando um autor em nosso banco de dados

```python
>>> autor = Autor(nome='Aldous', sobrenome='Huxley')
>>> db.session.add(autor)
>>> db.session.commit()
```

Perceba que é necessário primeiro adicionarmos o **autor** criado através de `db.session.add()` e depois para escrevermos as mudanças em nosso banco de dados executamos `db.session.commit()`. Feito, conseguimos popular a nossa tabela autor com nosso primeiro autor! Vamos agora cadastrar um livro

```python
>>> livro = Livro(titulo='Brave New World', sumario='Dystopian Novel', autor_id=autor.id)
>>> db.session.add(livro)
>>> db.session.commit()
```

Seguimos o mesmo procedimento anterior, exceto que passamos o atributo **id** do autor para o campo **autor_id**, dessa forma estamos cadastrando o autor que criamos recentemente como o autor deste livro. Agora que temos os cadastro feitos, podemos nos referir aos campos de nossas tabelas para acessarmos os dados, por exemplo

Se desejarmos acessar os atributos da tabela Livro

```python
>>> livro.titulo
>>> livro.sumario
>>> livro.autor
```

Nos retornará, respectivamente

```
'Brave New World'
'Dystopian Novel'
Autor("Aldous", "Huxley")
```

Podemos fazer o mesmo com a tabela Autor

```python
>>> autor.nome
>>> autor.sobrenome
>>> autor.livros
```

Que nos retorna

```
'Aldous'
'Huxley'
[Livro("Brave New World")]
```

Podemos também executar consultas através de SQLAlchemy, vejamos alguns exemplos

```python
>>> Autor.query.all()
```

O método `query.all()` nos retorna todos os Autores em nosso banco de dados, nesse caso só temos um, então nos será retornado `[Autor("Aldous", "Huxley")]`. 

Podemos também consultar um livro pelo seu respectivo **id**:

```python
Livro.query.get(1)
```

O método `query.get()` recebe como parâmetro um **id** e nos retorna o respectivo Livro com tal **id**, nesse caso: `Livro("Brave New World")`

---------------------------------------

## Sumarizando

- Neste capítulo estudamos os conceitos básicos de banco de dados
- Vimos alguns Sistemas Gerenciadores de Banco de Dados e sua importância
- Aprendemos sobre o conceito de **ORM**
- Utilizamos o **ORM** Flask-SQLAlchemy para criarmos nosso primeiro banco de dados
- Executamos algumas manipulações básicas como inserção de dados e consultas simples

Este Capítulo fecha a saga dos **fundamentos básicos de Flask**, agora vamos partir para a segunda parte de nossa caminhada, onde vamos aprender a estruturar uma aplicação de uma forma mais consistente, bem como unir todos os conhecimentos que adquirimos até então.

---------------------------------------
