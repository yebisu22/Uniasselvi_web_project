# Introdução
Detalhes sobre meu projeto web full-stack simples sobre Pokémon Fire Red.

As tecnologias utilizadas foram:

• Python

• Flask

• Flask-SQLAlchemy

• SQLite

• HTML

• CSS

• Jinja2

# Contextualização
Para começar, tudo foi feito em um sistema Oracle linux 8.9 WSL

O projeto teve grande inspiração em um vídeo do canal Manual do Dev com várias mudanças

https://youtu.be/SjtdH3dWLa8?si=uf4GEIryiyZQP2re

A primeira Mudança é que trataria apenas de 4 pokemons que seria da franquia de jogos pokemon, sendo mais especifico do pokemon fire red

Um site com um guia completo de como encontrá-los com uma página dedicada a cada um deles com animações e sons própios da musica tema e do pokemon

# Imagens do site 

<p float="left">
  <img src="https://github.com/yebisu22/Uniasselvi_web_project/blob/52861573c8531f64bcab016b069979161d6581d0/GIFS/INICIOCERTO-ezgif.com-video-to-gif-converter.gif?raw=true" width="400" />
  <img src="https://github.com/yebisu22/Uniasselvi_web_project/blob/52861573c8531f64bcab016b069979161d6581d0/GIFS/Articuno-ezgif.com-video-to-gif-converter.gif?raw=true" width="400" />
  <img src="https://github.com/yebisu22/Uniasselvi_web_project/blob/52861573c8531f64bcab016b069979161d6581d0/GIFS/Zapdos-ezgif.com-video-to-gif-converter.gif?raw=true" width="400" />
  <img src="https://github.com/yebisu22/Uniasselvi_web_project/blob/52861573c8531f64bcab016b069979161d6581d0/GIFS/Moltres-ezgif.com-video-to-gif-converter.gif?raw=true" width="400" />
  <img src="https://github.com/yebisu22/Uniasselvi_web_project/blob/52861573c8531f64bcab016b069979161d6581d0/GIFS/MewTwocerto-ezgif.com-video-to-gif-converter.gif?raw=true" width="400" />
</p>

Lembrando que como estão em gif perdem bastante qualidade e velocidade por isso parecem travadas



# Como tudo foi montado

Primeiro foi usado o Flask-SQLAlchemy com o SQLite gerenciando as tabelas Pokemon e Imagem como classes Python, e mapeando elas para tabelas no banco SQLite automaticamente

Tudo isso dentro de uma Venv ativada com 
```cpp
source nova_venve/active
```
Depois foi organizado as pastas onde ficaria cada coisa 
```cpp
projeto_pokemon/
│
├── app.py
├── pokemons.db (criado automaticamente ao rodar)
├── templates/
│   ├── index.html
│   └── pokemon.html
└── static/
    ├── images/
    ├── audio/
    ├── imgloc/
    ├── imglocmol/
    └── locmew/
```
A pasta /static foi onde foram armazenados as imagens, gif e sons de todos os pokemons

# Codigos

No arquivo app.py o codigo final ficou 

```python
from flask import Flask, jsonify, render_template
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///pokemons.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
db = SQLAlchemy(app)


class Pokemon(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    nome = db.Column(db.String(80), unique=True, nullable=False)
    tipo = db.Column(db.String(80), nullable=False)
    imagem_url = db.Column(db.String(200))
    imagem_animada = db.Column(db.String(200))
    lat = db.Column(db.Float)
    lng = db.Column(db.Float)
    cor_dominante = db.Column(db.String(7))

    def to_dict(self):
        return {
            "nome": self.nome,
            "tipo": self.tipo,
            "imagem_url": self.imagem_url,
            "imagem_animada": self.imagem_animada,
            "lat": self.lat,
            "lng": self.lng,
            "cor_dominante": self.cor_dominante
        }


class Imagem(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    url = db.Column(db.String(200), nullable=False)
    descricao = db.Column(db.String(100))
    pokemon_id = db.Column(db.Integer, db.ForeignKey('pokemon.id'), nullable=False)
    pokemon = db.relationship('Pokemon', backref=db.backref('imagens', lazy=True))


@app.before_first_request
def criar_tabelas():
    db.create_all()
    if not Pokemon.query.first():
        pokemons = [
            Pokemon(
                nome="Articuno",
                tipo="Gelo/Voador",
                imagem_url="https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/144.png",
                imagem_animada="https://img.pokemondb.net/sprites/black-white/anim/normal/articuno.gif",
                lat=40.0,
                lng=-74.0,
                cor_dominante="#7AC7E3"
            ),
            Pokemon(
                nome="Zapdos",
                tipo="Elétrico/Voador",
                imagem_url="https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/145.png",
                imagem_animada="https://img.pokemondb.net/sprites/black-white/anim/normal/zapdos.gif",
                lat=39.0,
                lng=-75.0,
                cor_dominante="#FFD700"
            ),
            Pokemon(
                nome="Moltres",
                tipo="Fogo/Voador",
                imagem_url="https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/146.png",
                imagem_animada="https://img.pokemondb.net/sprites/black-white/anim/normal/moltres.gif",
                lat=38.0,
                lng=-76.0,
                cor_dominante="#F7786B"
            ),
            Pokemon(
                nome="Mewtwo",
                tipo="Psíquico",
                imagem_url="https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/150.png",
                imagem_animada="https://img.pokemondb.net/sprites/black-white/anim/normal/mewtwo.gif",
                lat=37.0,
                lng=-122.0,
                cor_dominante="#A55FC2"
            )
        ]
        db.session.add_all(pokemons)
        db.session.commit()

        articuno = Pokemon.query.filter_by(nome="Articuno").first()
        zapdos = Pokemon.query.filter_by(nome="Zapdos").first()
        moltres = Pokemon.query.filter_by(nome="Moltres").first()
        mewtwo = Pokemon.query.filter_by(nome="Mewtwo").first()

        imagens = [
            # Articuno

            Imagem(url="/static/images/artloc.png", descricao="Mapa art", pokemon=articuno),
            Imagem(url="/static/images/invisivel.png", descricao="Espaço invisivel", pokemon=articuno),
            Imagem(url="/static/imgloc/parte 1.png", descricao="Lugar 1", pokemon=articuno),
            Imagem(url="/static/imgloc/parte 2.png", descricao="Lugar 2", pokemon=articuno),
            Imagem(url="/static/imgloc/parte 3.png", descricao="Lugar 3", pokemon=articuno),
            Imagem(url="/static/imgloc/parte 4.png", descricao="Lugar 4", pokemon=articuno),
            Imagem(url="/static/imgloc/parte 5.png", descricao="Lugar 5", pokemon=articuno),
            Imagem(url="/static/imgloc/parte 6.png", descricao="Lugar 6", pokemon=articuno),

            # Zapdos
            Imagem(url="/static/images/zaploc.png", descricao="Mapa art", pokemon=zapdos),
            Imagem(url="/static/images/invisivel.png", descricao="Espaço invisivel", pokemon=zapdos),
            Imagem(url="/static/images/casazap.png", descricao="Lugar 1", pokemon=zapdos),


            # Moltres
            Imagem(url="/static/images/ilhas.png", descricao="Mapa art", pokemon=moltres),
            Imagem(url="/static/images/invisivel.png", descricao="Espaço invisivel", pokemon=moltres),
            Imagem(url="/static/imglocmol/parte 1.png", descricao="lugar 3", pokemon=moltres),
            Imagem(url="/static/imglocmol/parte 2.png", descricao="Lugar 1", pokemon=moltres),
            Imagem(url="/static/imglocmol/parte 3.png", descricao="Lugar 2", pokemon=moltres),
            Imagem(url="/static/images/invisivel.png", descricao="Esapço invisivel", pokemon=moltres),

            # Mewtwo
            Imagem(url="/static/locmew/loc 1.png", descricao="Mapa art", pokemon=mewtwo),
            Imagem(url="/static/locmew/loc 2.png", descricao="Lugar 1", pokemon=mewtwo),
            Imagem(url="/static/locmew/parte 1.png", descricao="Lugar 2", pokemon=mewtwo),
            Imagem(url="/static/locmew/parte 2.png", descricao="Lugar 3", pokemon=mewtwo),
            Imagem(url="/static/locmew/parte 3.png", descricao="Lugar 4", pokemon=mewtwo),
            Imagem(url="/static/locmew/parte 4.png", descricao="Lugar 5", pokemon=mewtwo),
        ]

        db.session.add_all(imagens)
        db.session.commit()


@app.route("/")
def index():
    return render_template("index.html")


@app.route("/api/pokemons")
def listar_pokemons():
    pokemons = Pokemon.query.all()
    return jsonify([p.to_dict() for p in pokemons])


@app.route("/pokemon/<nome>")
def pokemon(nome):
    p = Pokemon.query.filter_by(nome=nome).first_or_404()
    return render_template("pokemon.html", pokemon=p)


if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000, debug=True) banco de dados
```
# Explicando app.py
```python
from flask import Flask, jsonify, render_template
from flask_sqlalchemy import SQLAlchemy
```
Inicio responsavel pela importação Flask

```python
app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///pokemons.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
db = SQLAlchemy(app)
```

app = Flask(__name__): cria a aplicação Flask.

SQLALCHEMY_DATABASE_URI: define o banco SQLite local chamado pokemons.db.

SQLALCHEMY_TRACK_MODIFICATIONS: desativa alertas de modificações que não são necessárias.

db = SQLAlchemy(app): inicializa o ORM (SQLAlchemy) ligado ao app.

```python
class Pokemon(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    nome = db.Column(db.String(80), unique=True, nullable=False)
    tipo = db.Column(db.String(80), nullable=False)
    imagem_url = db.Column(db.String(200))
    imagem_animada = db.Column(db.String(200))
    lat = db.Column(db.Float)
    lng = db.Column(db.Float)
    cor_dominante = db.Column(db.String(7))
```
Define a tablea pokemons com as informações principais como nome tipo a foto e a sprite deles animada 

Ao longo do projeto, acabei abandonando a lat e a lgn que seriam a localização, para um projeto mais sofisticado 

```python
def to_dict(self):
    return {
        "nome": self.nome,
        "tipo": self.tipo,
        "imagem_url": self.imagem_url,
        "imagem_animada": self.imagem_animada,
        "lat": self.lat,
        "lng": self.lng,
        "cor_dominante": self.cor_dominante
    }
```
Serve para transformar um Pokémon em JSON

```python
class Imagem(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    url = db.Column(db.String(200), nullable=False)
    descricao = db.Column(db.String(100))
    pokemon_id = db.Column(db.Integer, db.ForeignKey('pokemon.id'), nullable=False)
    pokemon = db.relationship('Pokemon', backref=db.backref('imagens', lazy=True))
```
Tabela usada para armaenar imagens relaciondas a cada pokemon como por exemplo as localizações
```python
@app.before_first_request
def criar_tabelas():
    db.create_all()
    if not Pokemon.query.first():
        pokemons = [
            Pokemon(
                nome="Articuno",
                tipo="Gelo/Voador",
                ...
            ),
            ...
        ]
        db.session.add_all(pokemons)
        db.session.commit()
        
        articuno = Pokemon.query.filter_by(nome="Articuno").first()
        ...
        
        imagens = [
            Imagem(url="/static/images/artloc.png", descricao="Mapa art", pokemon=articuno),
            ...
        ]
        db.session.add_all(imagens)
        db.session.commit()
```
Criação de tabelas e população de dados 

@app.before_first_request: executa essa função antes da primeira requisição ao servidor.

Cria as tabelas do banco se não existirem.

Popula o banco com alguns Pokémon e suas imagens, se o banco estiver vazio.

Me permitindo poder apagar o arquivo pokemon.db quantas vezes necessarias

```python
@app.route("/")
def index():
    return render_template("index.html")
```
Define a rota para index.html e exibe como pagina principal do site

```python
@app.route("/api/pokemons")
def listar_pokemons():
    pokemons = Pokemon.query.all()
    return jsonify([p.to_dict() for p in pokemons])
```
Retorna o JSON com os dados dos pokemons

```python
@app.route("/pokemon/<nome>")
def pokemon(nome):
    p = Pokemon.query.filter_by(nome=nome).first_or_404()
    return render_template("pokemon.html", pokemon=p)
```
Responsavel pelo detalhe de cada pokemon em uma pagina individual e retornando tudo para pokemon.html
```python
if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000, debug=True)
```
Responsavel pelo servidor flask rodar na porta 5000 com o debug ativado

# Codigo INDEX.HTML

INDEX.HTML

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Guia Pokémon FireRed - Lendários</title>

  <style>

    body {
      margin: 0;
      height: 100vh;

        background:
        linear-gradient(rgba(0,0,0,0.8), rgba(0,0,0,0.8)),
        url('/static/images/agua.gif') no-repeat center center fixed;

      background-size: cover;
      font-family: Arial, sans-serif;
      display: flex;
      flex-direction: column;
      align-items: center;
      color: white;
    }

    h1 {
      margin-top: 20px;
      margin-bottom: 10px;
      font-size: 2.5em;
      text-shadow: 1px 1px 3px #000;
    }


    ul#lista {
      list-style: none;
      padding: 0;
      margin: 0;
      display: flex;
      gap: 20px;
      flex-wrap: wrap;
      justify-content: center;
      max-width: 90vw;
      width: 100%;
    }

    ul#lista li button {
      //background: rgba(128, 128, 128, 0.5);
      border: none;
      border-radius: 10px;
      cursor: pointer;
      color: #1D1D1D;
      text-align: center;
      font-size: 1.1em;
      padding: 10px;
      transition: transform 0.2s;
    }

    ul#lista li button:hover {
      transform: scale(1.1);
    }

    ul#lista li img {
      display: block;
      margin: 5px auto 0 auto;
      width: 150px;
      height: 150px;
      object-fit: contain;
      pointer-events: none;
      border: none;
      box-shadow: none;
    }
  </style>
</head>
<body>
  <h1>Guia Pokémon FireRed - Lendários</h1>

  <div style="display: flex; justify-content: center;">
    <img src="/static/images/mapagba.png"
         alt="Mapa FireRed"
         style="max-width: 90vw; max-height: 400px; width: auto; height: auto; margin-bottom: 20px; border-radius: 8px;">
  </div>

  <ul id="lista"></ul>

  <audio id="bg-music" loop autoplay>
    <source src="/static/audio/Firered.wav" type="audio/mpeg" />
    Seu navegador não suporta áudio.
  </audio>

  <script>
    const music = document.getElementById("bg-music");
    music.volume = 0.3;

    window.addEventListener("click", () => {
      music.muted = false;
      music.play().catch(() => {
        console.log("Autoplay bloqueado pelo navegador.");
      });
    }, { once: true });

    fetch("/api/pokemons")
      .then(res => res.json())
      .then(pokemons => {
        const lista = document.getElementById("lista");
        pokemons.forEach(p => {
          const li = document.createElement("li");
          const btn = document.createElement("button");
          const img = document.createElement("images");
          img.src = p.imagem_url;
          img.alt = p.nome;
          img.style.width = "100px";




          btn.innerHTML = 
            <strong>${p.nome}</strong><br>
            <img src="${p.imagem_url}" alt="${p.nome}">
          ;

          btn.appendChild(img);

          btn.onclick = () => {
            window.location.href = /pokemon/${p.nome};
          };

          li.appendChild(btn);

          lista.appendChild(li);

        });
      });
  </script>
</body>
</html>
```
Codigo responsavel pela primeira parte do site 

```html
body {
  background: url('/static/images/agua.gif') ...;
  color: white;
  font-family: Arial, sans-serif;
  ...
}
```
Parte do CSS com um gif de agua de fundo, definições de fonte e alinhamento 

```html
<audio id="bg-music" loop autoplay>
  <source src="/static/audio/Firered.wav" type="audio/mpeg" />
</audio>
```
Musica tema do fire red de fundo com volume ajustavel no script

```html
<img src="/static/images/mapagba.png" ...>
```
Imagem do mapa do jogo com todos os pokemons lendarios 

```html
<ul id="lista"></ul>
```
Lista vazia de pokemon preenchida pela API

```html
fetch("/api/pokemons")
  .then(res => res.json())
  .then(pokemons => {
  });
```
Direciona cada pokemon para sua parte privada com apenas um toque ao escolher 
```html
window.addEventListener("click", () => {
  music.muted = false;
  music.play()
});
```
A musica começa a tocar apenas depois de um toque na tela para evitar problema de conflitos e bloqueio dos navegadores

#Codigo POKEMON.HTML

```html
<!DOCTYPE html>
<html>
<head>
  <title>{{ pokemon.nome }}</title>

  <link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap" rel="stylesheet">

  <style>
    body {
      color: #fff;
      font-family: 'Press Start 2P', cursive;
      text-align: center;
      margin: 0;
      height: 100vh;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding-top: 120px;
      box-sizing: border-box;

     img[src$="invisivel.png"] {
       display: none;
      }

     {% if pokemon.nome == "Articuno" %}
      background:
        linear-gradient(rgba(0,0,0,0.8), rgba(0,0,0,0.8)),
        url('/static/images/artloop.gif') no-repeat center center fixed;
      background-size: cover;

      {% elif pokemon.nome == "Zapdos" %}
      background:
        linear-gradient(rgba(0,0,0,0.8), rgba(0,0,0,0.8)),
        url('/static/images/zaploop.gif') no-repeat center center fixed;
      background-size: cover;

      {% elif pokemon.nome == "Moltres" %}
      background:
        linear-gradient(rgba(0,0,0,0.8), rgba(0,0,0,0.8)),
        url('/static/images/moloop.gif') no-repeat center center fixed;
      background-size: cover;

      {% elif pokemon.nome == "Mewtwo" %}
      background:
        linear-gradient(rgba(0,0,0,0.8), rgba(0,0,0,0.8)),
        url('/static/images/mewloop.gif') no-repeat center center fixed;
      background-size: cover;

      {% endif %}
    }

    .imagem-grande {
      width: 600px;
      height: auto;
      margin-bottom: 20px;
      /* border-radius: 8px; */
    }

    .imagem-pequena {
      width: 600px;
      height: auto;
      border-radius: 8px;
      display: block;
      margin: 10px auto;
    }
    .comentario-entre-imagens {
      background: white;
      color: black;
      border: 3px solid black;
      border-radius: 10px;
      padding: 10px;
      margin: 15px auto;
      max-width: 90vw;
      font-family: 'Press Start 2P', cursive;
      font-size: 12px;
      text-align: center;
      box-shadow: 4px 4px 0 black;
    }
    .comentario-entre-pequenas {
       background: white;
       color: black;
       border: 2px solid black;
       border-radius: 8px;
       padding: 6px 10px;
       margin: 8px auto;
       max-width: 80vw;
       font-family: 'Press Start 2P', cursive;
       font-size: 10px;
       text-align: center;
       box-shadow: 2px 2px 0 black;
    }

    img {
      width: 200px;
      display: block;
      margin: 20px auto;
    }

    .articuno-img {
      width: 150px;
    }

    a {
      color: #fff;
      text-decoration: underline;
      font-size: 12px;
      margin-top: 30px;
      font-weight: bold;
    }

    p {
      font-family: 'Press Start 2P', cursive;
      font-size: 12,5px;
      margin: 20px 0;
      line-height: 1.5;
    }

    .caixa-dialogo {
      background: white;
      border: 4px solid black;
      border-radius: 12px;
      padding: 20px;
      box-shadow: 4px 4px 0 black;
      color: black;
      font-family: 'Press Start 2P', cursive;
      font-size: 12px;
      line-height: 1.4;
      text-align: center;
      position: relative;
    }
    .caixa-dialogo::after {
      content: "";
      position: absolute;
      bottom: 10px;
      right: 15px;
      width: 10px;
      height: 10px;
      border-bottom: 3px solid black;
      border-right: 3px solid black;
      transform: rotate(45deg);
      animation: blink 1s infinite;
    }

    @keyframes blink {
      0%, 50%, 100% { opacity: 1; }
      25%, 75% { opacity: 0; }
    }

  </style>

</head>

<body>

<p>Tipo:
  {% for tipo_nome in pokemon.tipo.split('/') %}
    <span class="tipo" style="
      background:
      {% if tipo_nome == 'Elétrico' %} #F8D030
      {% elif tipo_nome == 'Voador' %} #A890F0
      {% elif tipo_nome == 'Fogo' %} #F08030
      {% elif tipo_nome == 'Psíquico' %} #F85888
      {% else %} {{ pokemon.cor_dominante }} {% endif %};
      color: white;
      padding: 4px 10px;
      border-radius: 12px;
      font-family: 'Press Start 2P', cursive;
      font-size: 12px;
      margin-right: 6px;
      display: inline-block;
      user-select: none;
      text-shadow: 1px 1px 2px rgba(0,0,0,0.5);
    ">
      {{ tipo_nome }}
    </span>
  {% endfor %}
</p>

{% if pokemon.nome == "Articuno" %}
  <audio id="cry" src="/static/audio/Articuno.mp3" preload="auto"></audio>
  <audio id="theme" src="/static/audio/Artcasa.wav" preload="auto" autoplay loop></audio>
{% elif pokemon.nome == "Zapdos" %}
  <audio id="cry" src="/static/audio/Zapdos.mp3" preload="auto"></audio>
  <audio id="theme" src="/static/audio/Zapcasa.wav" preload="auto" autoplay loop></audio>

{% elif pokemon.nome == "Moltres" %}
  <audio id="cry" src="/static/audio/Moltres.mp3" preload="auto"></audio>
  <audio id="theme" src="/static/audio/Molilha.wav" preload="auto" autoplay loop></audio>

{% elif pokemon.nome == "Mewtwo" %}
  <audio id="cry" src="/static/audio/Mewtwo.mp3" preload="auto"></audio>
  <audio id="theme" src="/static/audio/Mewcasa.wav" preload="auto" autoplay loop></audio>
{% endif %}

<h1>{{ pokemon.nome }}</h1>

{% set comentarios_antes = {
  'Articuno': 'Vá até a cidade de "Fuchsia City" Desça o caminho do ginasio e siga o trajeto',
  'Zapdos': 'Se voce deseja encontrá-lo, vá até a rota 10 e siga o caminho',
  'Moltres': 'Viaje até as "Ilhas Sevii" e vá até a terceira ilha',
  'Mewtwo': 'Vá até a rota 24 e use o Surf no local indicado'
} %}

{% set comentarios_entre_pequenas = {
  'Articuno': [
    "Siga o Caminho e Desça a Escada.",
    "Siga o Caminho e Vá Para a Outra Esacada.",
    "Novamente Siga Pela Escada.",
    "Mova as Pedras nas Direções indicadas em Ordem.",
    "Caia no Buraco indicado e a Correntetesa Te Levará.",
    "Ai Está Articuno",
  ],

  'Zapdos': [
    "Siga o caminho,Você encontrará o Zapdos nesta usina abandonada!",
  ],

  'Moltres': [
    "Suba o Monte Ember",
    "Voce encontrará um pequeno Puzzle",
    "Empurre as Pedras na ordem certa.",
    "Ai está Moltres",
  ],
  'Mewtwo': [
    "Siga o caminho",
    "Continue o Trajeto",
    "Desça a escada e siga",
    "Ai está Mewtwo",
  ]
} %}

<img src="{{ pokemon.imagem_animada or pokemon.imagem_url }}" alt="{{ pokemon.nome }}" class="pokemon-img {% if pokemon.nome == 'Articuno' %}articuno-img{% endif %}">

<div class="imagens-pokemon">

  <div class="comentario-entre-imagens caixa-dialogo">
    <p>{{ comentarios_antes[pokemon.nome] }}</p>
  </div>

  {% for imagem in pokemon.imagens %}
    {% if imagem.descricao == 'Mapa art' %}
      <img src="{{ imagem.url }}" alt="{{ imagem.descricao }}" class="imagem-grande">
    {% endif %}
  {% endfor %}

  {% set pequenas = pokemon.imagens | selectattr('descricao', 'ne', 'Mapa art') | list %}

  {% for imagem in pequenas %}
    <img src="{{ imagem.url }}" alt="{{ imagem.descricao }}" class="imagem-pequena">
    {% if not loop.last %}

      <div class="comentario-entre-pequenas caixa-dialogo">
        <p>{{ comentarios_entre_pequenas[pokemon.nome][loop.index0] }}</p>
      </div>

    {% endif %}
  {% endfor %}
</div>

<a href="/">← Voltar para ao menu </a>

<script>
  document.addEventListener('click', () => {
    const cry = document.getElementById('cry');
    const theme = document.getElementById('theme');

    if (cry && cry.paused) {
      cry.volume = 0.1;
      cry.play().catch(() => {});
    }
    if (theme && theme.paused) {
      theme.volume = 0.5;
      theme.play().catch(() => {});
    }
  }, { once: true });
</script>

</body>
</html>
```
Codigo html responsavel pela parte individual ao clicar em cada pokemon
```html
{% if pokemon.nome == "Articuno" %}
  background: url('/static/images/artloop.gif')
{% elif pokemon.nome == "Zapdos" %}
  background: url('/static/images/zaploop.gif')
...
```
CSS com fonte estilo jogos antigos (fonte Press Start 2P)., gifs e caixas de dialogo estilo fire red que piscam uma setinha no canto inferior 

.imagem_grande=imagem principal (mapa)

.imagem-pequena = imagens da sequência de localização

.comentario-entre-pequenas seria para poder fazer comentarios entre as imagens pequenas

```html
{% if pokemon.nome == "Articuno" %}
  <audio id="cry" src="/static/audio/Articuno.mp3" ...>
  <audio id="theme" src="/static/audio/Artcasa.wav" ...>
{% elif pokemon.nome == "Zapdos" %}
  ...
```
Audio de cada Pokemon como cry que seria o som que eles fazem e a trilha sonora da região em que são encontrados 
```html
<h1>{{ pokemon.nome }}</h1>
<p>Tipo: <span class="tipo" style="background: ...">{{ tipo_nome }}</span></p>
```
Mostra o nome do Pokémon.

Mostra cada tipo (separado por /) com cor diferente:

Fogo = laranja

Psíquico = rosa

Etc

```html
{% set comentarios_antes = {
  'Articuno': 'Vá até a cidade de "Fuchsia City" ...',
  'Zapdos': 'Se voce deseja encontrá-lo, vá até a rota 10 ...',
  ...
} %}
...
<p>{{ comentarios_antes[pokemon.nome] }}</p>
```
Comentarios iniciais de cada pokemon ficam em cima da imagem grande

```html
{% for imagem in pokemon.imagens %}
  {% if imagem.descricao == 'Mapa art' %}
    <img src="{{ imagem.url }}" class="imagem-grande">
  {% endif %}
{% endfor %}
```
Mapa principal maior

```html
{% for imagem in pequenas %}
  <img src="{{ imagem.url }}" class="imagem-pequena">
  <div class="comentario-entre-pequenas caixa-dialogo">
    <p>{{ comentarios_entre_pequenas[pokemon.nome][loop.index0] }}</p>
  </div>
{% endfor %}
```
Imagens pequenas passo passo 
```html
document.addEventListener('click', () => {
  cry.play();
  theme.play();
});
```
Audio ativo ao clicar 

```html
<a href="/">← Voltar para ao menu </a>
```
Botão clicavel de voltar 

# Transformando tudo em um site acessivel fora do local host

Para isso foi usado a Cloudfire para externar o local_host para um tunel acessivel para todos com o link



