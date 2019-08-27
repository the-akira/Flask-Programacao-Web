# Arquivos Static

---------------------------------------

## Introdução

Os **Arquivos Static**, como seus nomes sugerem, são arquivos que não mudam, **estáticos**. Aplicações Web geralmente precisam servir esses arquivos, que seriam **imagens**, arquivos **Javascript** e **CSS**.

Para trabalharmos com esses arquivos precisaremos criar um diretório chamado `static` dentro de nosso diretório principal que chamamos de `Exemplos`. Dentro do diretório `static` que criamos, vamos criar mais três diretórios respectivamente `css`, `js` e `imagens`.

### CSS

No diretório `css` vamos criar um arquivo `style.css` com o seguinte conteúdo:

```
body {
	background-color: gray;
	color: white;
}
```

Veja que alteramos o fundo de Aplicação para a cor cinza e também alteramos a cor das fontes para branco, um estilo simples para testarmos. 

### Javascript

Agora vamos criar dentro do diretório `js` um arquivo chamado `main.js` com o seguinte conteúdo:

```
alert('Hello World')
```

Veja que é apenas um simples **script** que exibe um alerta 'Hello World'. 

### Imagens

Agora vamos obter o logo do Flask: 

![flask-logo](https://flask.palletsprojects.com/en/1.1.x/_images/flask-logo.png)

Vamos salvá-lo em nosso diretório `imagens`. 

### Estrutura

A estrutura de nosso diretório static ficou dessa forma:

```
static
├── css
│   └── style.css
├── imagens
│   └── flask-logo.png
└── js
    └── main.js
```

---------------------------------------

## Servindo os Arquivos Static

Agora em nossa aplicação, vamos editar o arquivo `app.py` de forma que possamos servir esses **arquivos static**, para isso vamos criar uma nova rota de exemplo.

```
@app.route('/static')
def arquivos_static():
	return render_template('static.html')
```

Veja que é apenas uma simples rota para teste, agora vamos criar um arquivo `static.html` dentro do diretório `templates` para que possamos servir o conteúdo estático. Nosso arquivo `static.html` contará com o seguinte conteúdo:

```
{% extends 'layout.html' %}

{% block conteudo %}
	<link rel="stylesheet" type="text/css" href="{{ url_for('static', filename='css/style.css') }}">
	<p>Arquivos Static</p>
	<img src="{{ url_for('static', filename='imagens/flask-logo.png') }}">
	<script type="text/javascript" src="{{ url_for('static', filename='js/main.js') }}"></script>
{% endblock %}
```

Veja que estamos utilizando o conceito de Herança, em outras palavras, estamos herdando as propriedades de nosso layout principal e nosso conteúdo está contido dentro do `{% block conteudo %}`. 

Através da função **url_for()** estamos buscando dentro do diretório **static** nossos arquivos. Primeiramente carregamos o **CSS** que se encontra em `css/style.css`, depois carregamos nossa imagem que está contida em `imagens/flask.png` e por fim nosso script que se encontra em `js/main.js`.

Agora podemos executar nossa aplicação através do comando `python app.py` e navegar até `http://127.0.0.1:5000/static` para vermos as alterações em nossa aplicação.

---------------------------------------

## Sumarizando

Nesse capítulo aprendemos como gerenciar e servir nossos arquivos static, podemos também observar que Flask torna muito fácil trabalharmos com esses arquivos. 

Caso você queira optar por utilizar uma **[CDN](https://www.webopedia.com/TERM/C/CDN.html)** para servir os seus arquivos static de uma forma mais eficiente, você pode checar este interessante **[Tutorial](https://www.digitalocean.com/community/tutorials/using-a-cdn-to-speed-up-static-content-delivery)**

Agora que já temos o domínio dos Arquivos Static podemos começar a utilizar Formulários para capturar dados dos usuários de nossa aplicação.

---------------------------------------
