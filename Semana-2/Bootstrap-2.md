# Introducao Bootstrap

## Setting npm and webpack

    1. criou uma pasta
    2. git init
    3. npm init
    4. cria um gitignore e coloca o node_modules

### Bundler com Webpack

Instalando as dependências `npm install webpack webpack-cli webpack-dev-server --save-dev`

Crie um arquivo `webpack.config.js` na raiz do projeto

### Icones com fontawesome

Instalando as dependências `npm install --save-dev @fortawesome/fontawesome-free`

### Fibers

Utilizado para usar as coroutines do node

Instalando as dependências `npm install --save-dev fibers`
obs: tive que instalar `sudo apt-get install g++ build-essential` no meu Ubuntu antes

### Sass

Instalando as dependências `npm install --save-dev sass`

### Dependências do webpack

sass-loader
node-sass
css-loader
autoprefixer
postcss-loader
file-loader

Instalando as dependências `npm install --save-dev sass-loader node-sass css-loader autoprefixer postcss-loader file-loader`

mini-css-extract-plugin -> extrai o CSS em arquivos separados
Instalando as dependências `npm install --save-dev mini-css-extract-plugin`

### Instalando o Bootstrap

Instalando as dependências `npm install bootstrap`

O Bootstrap possui com dependência o jquery (lib js) e o popper.js (engine para tooltip)
`npm install --save jquery popper.js`

### Criando a estrutura do projeto

A estrutura do projeto esta dessa forma:

```
- dist (pasta com os arquivos de produção)
  - css
  - fonts
  - js
  index.html
src
  - js
    plugins (pasta com arquivos js de terceiros)
    - app.js
    - custom.js
  - sass
    - app
      - components (css dos componentes da pagina)
      - pages (css das paginas ex pagina Login)
      - vendor (css para sobreescrever css de terceiros)
    - _customize-bootstrap.scss
    - _vars.scss
    - main.scss
.gitignore
package.json
webpack.config.js
```

### configurando o webpack

```js
const path = require("path");
const webpack = require("webpack");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  devServer: {
    contentBase: path.resolve(__dirname, "dist"), //de onde nossos arqs serão servidos
    compress: true, //aqr final será comprimido
    publicPath: "dist",
    writeToDisk: true
  },
  entry: "./src/js/app.js", //arq js inicial do nosso desenvolvimento
  output: {
    filename: "app.js", //nome do arquivo de saída
    path: path.resolve(__dirname, "dist"), //localização do arquivo de saída
    publicPath: "dist"
  },
  module: {
    rules: [
      {
        test: /\.(scss)$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader
          },
          {
            loader: "css-loader"
          },
          {
            loader: "postcss-loader",
            options: {
              plugins: function() {
                return [
                  require("autoprefixer") //utilizado pelo bootstrap sass file para compilar mais rapido
                ];
              }
            }
          },
          {
            loader: "sass-loader"
          }
        ]
      },
      {
        test: /\.(eot|woff|woff2|ttf|svg)(\?\S*)?$/, //utilizado para as fonts
        use: [
          {
            loader: "file-loader",
            options: {
              name: "[name].[ext]",
              outputPath: "../fonts/",
              publicPath: "../fonts/"
            }
          }
        ]
      }
    ]
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: "../css/app.css" //nome do arq final com o css extraido
    }),
    new webpack.ProvidePlugin({
      $: "jquery", //coloca o $ como variavel global para o jQuery
      jQuery: "jquery"
    })
  ]
};
```

Para testar basta criar um boilerplate de html no `dist/index.html` e importar o script `js/app.js`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Document</title>
    <link rel="stylesheet" href="css/app.css" />
  </head>
  <body>
    <h1>Hello World!</h1>

    <script src="js/app.js"></script>
  </body>
</html>
```

Dentro de `src/js/app.js` import o bootstrap

```js
import "bootstrap";
import "../sass/main.scss";
import "./custom";
```

Após isso crie no `package.json` o script de build `"build": webpack`

### Servindo a aplicação com webpack-dev-server

Para desenolvermos utilizaremos um servidor de arquivos estáticos chamado `webpack-dev-server`. Todas a configuração dele fizemos no `webpack.config` agora precisamos somente criar um script de inicialização no `packjage.json`:
`"start:dev": webpack-dev-server`

Após criado o script rode-o na linha de comando e abra o link `localhost:8080` no seu navegador. Se tudo estiver ok deverá aparecer a mensagem Hello World

## Creating a bootstrap templates

### Criando o básico do main.scss

```css
/*!
* HQ Admin Theme
* Author: Henrique Jensen
*/

//Bootstrap
@import "_vars.scss";
@import "_customize-bootstrap.scss";
@import "~bootstrap/scss/bootstrap"; //import de dentro da node_modules

$fa-font-path: "~@fortawesome/fontawesome-free/webfonts";
@import "~@fortawesome/fontawesome-free/scss/fontawesome.scss";
@import "~@fortawesome/fontawesome-free/scss/regular.scss";
@import "~@fortawesome/fontawesome-free/scss/solid.scss";

//App layout

@import "app/components/app"; //import do css do componente app
```

Após todo o arquivo criado deixe rodando seu `start:dev` script e perceba que a sua pagina se atualizou graças ao hot reload do webpack-dev-server

### Grid e estrutura básica do bootstrap

Dentro da pasta `dist` crie a partir do html da `index.html` um arquivo `forms.html`
Dentro da tag body crie o layout do seu forms

```html
<div class="container">
  <div class="row">
    <div class="col-md-6"></div>
    <div class="col-md-6"></div>
  </div>
</div>
```

container -> elemento principal que envelopa as coisas dentro de `rows` e `columns`. Possui um width fixo ou fluido.
row ->
col -> grid do bootstrap. É dividido em 12 partes, logo podemos fazer um combinação delas para criarmos nosso design. AS colunas são responsivas por default

class="col-3" //teremos um coluna de tamanho 3

Se quisermos que as colunas mudem de acordo com o tamanho da tela, precisamos utilizar as propriedades `small`, `medium` ou `large`

class="col-md-3" //teremos um coluna de tamanho 3, para telas acima da medida medium, abaixo dela como não passamos nada o elemento vai ocupar toda a linha

### Trabalhando com formulários

Continuando o layout do nosso formulário

```html
<div class="container">
  <div class="row">
    <div class="col-md-6">
      <form>
        <div class="form-group">
          <label for="username">Username</label>
          <input
            class="form-control"
            type="text"
            name="username"
            id="username"
          />
        </div>

        <div class="form-group">
          <label for="password">Password</label>
          <input
            class="form-control"
            type="text"
            name="password"
            id="password"
          />
        </div>

        <div class="form-group">
          <button class="btn btn-primary">
            Submit
          </button>
        </div>
      </form>
    </div>

    <div class="col-6"></div>
  </div>
</div>
```

form-group -> classe pai para criarmos um grupo para nosso input
form-control -> classe filha do form-group
btn -> classe base para botoes
btn-primary -> classe que aplica a cor primaria do bootstrap no botao

Para a segunda coluna o codigo deverá ser o seguinte

```html
<form>
  <div class="form-group row">
    <label class="col-sm-2 col-form-label" for="username">Username</label>
    <div class="col-sm-10">
      <input class="form-control" type="text" name="username" id="username" />
    </div>
  </div>

  <div class="form-group row">
    <label class="col-sm-2 col-form-label" for="password">Password</label>
    <div class="col-sm-10">
      <input class="form-control" type="text" name="password" id="password" />
    </div>
  </div>

  <div class="form-group row">
    <div class="col-sm-10 offset-sm-2">
      <button class="btn btn-primary">
        Submit
      </button>
    </div>
  </div>
</form>
```

col-form-label -> aplica propriedades para alinhar o label
offset-sm-2 -> "pula" duas colunas no grid, este valor pode ser qualquer um entre 1 e 12

Para a terceira coluna, vamos criar uma nova linha e vamos ocupar todas as colunas por isso o col-12

```html
<div class="row">
  <div class="col-12">
    <form action="" class="form-inline">
      <div class="form-group">
        <label class="pr-1" for="username">Username</label>
        <div class="">
          <input
            class="px-1 form-control"
            type="text"
            name="username"
            id="username"
          />
        </div>
      </div>

      <div class="form-group">
        <label class="px-1" for="password">Password</label>
        <div class="">
          <input
            class="px-1 form-control"
            type="text"
            name="password"
            id="password"
          />
        </div>
      </div>

      <button class="mx-1 btn btn-primary">
        Submit
      </button>
    </form>
  </div>
</div>
```

form-inline -> classe que coloca o formulario em 1 linha
pr-1 -> adiciona um padding de 1px a esquerda. A classe p adiciona padding e suas variantes são pr (padding right), pl(left), px(x axis), py (axis), pt (top)
mx-1 -> adiciona margin ao elemento, na mesma ideia do padding

### Trabalhando com tabelas

Dentro da pasta `dist` crie a partir do html da `index.html` um arquivo `tables.html`
Dentro da tag body crie o layout da sua tabela.

Table em bootstrap não é um componente e sim um opt-in. Para usarmos somente adicionamos a class `table` para a tag table e ja temos o layout dela.

```html
<div class="container">
  <table class="table">
    <thead class="thead-dark">
      <tr>
        <th scope="col">#</th>
        <th scope="col">First</th>
        <th scope="col">Last</th>
        <th scope="col">Handle</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <th scope="row">1</th>
        <td>Mark</td>
        <td>Otto</td>
        <td>@mdo</td>
      </tr>
      <tr>
        <th scope="row">2</th>
        <td>Jacob</td>
        <td>Thornton</td>
        <td>@fat</td>
      </tr>
      <tr>
        <th scope="row">3</th>
        <td>Larry</td>
        <td>the Bird</td>
        <td>@twitter</td>
      </tr>
    </tbody>
  </table>

  <div class="table-responsive">
    <table class="table table-bordered table-striped">
      <thead class="thead-dark">
        <tr>
          <th scope="col">#</th>
          <th scope="col">First</th>
          <th scope="col">Last</th>
          <th scope="col">Handle</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <th scope="row">1</th>
          <td>Mark</td>
          <td>Otto</td>
          <td>@mdo</td>
        </tr>
        <tr>
          <th scope="row">2</th>
          <td>Jacob</td>
          <td>Thornton</td>
          <td>@fat</td>
        </tr>
        <tr class="table-success">
          <th scope="row">3</th>
          <td>Larry</td>
          <td>the Bird</td>
          <td>@twitter</td>
        </tr>
      </tbody>
    </table>
  </div>
</div>
```

thead-dark -> muda a cord do cabecalho, podemos variar para thead-primary, thead-light e etc
table-bordered -> cria uma borda em volta de toda a tabela
table-striped -> diferencia as cores das linhas da tabela, uma fica escura e a outra clara
table-responsive -> usado em uma div em volta da tabela para inserir uma barra de rolagem quando temos uma tela menor que a tabela
table-success -> usado para aplicar cor a linha da tabela, pode variar por todas as cores do bootstrap

### Trabalhando com cards

Dentro da pasta `dist` crie a partir do html da `index.html` um arquivo `cards.html`
Dentro da tag body crie o layout do seu card.

Os cards no bootstrap são containeres de conteudo extensíveis.
Geralmente dividimos nossos cards em três partes: Header, Body e Footer.

```html
<div class="container-fluid">
  <div class="row">
    <div class="col-6">
      <div class="card">
        <div class="card-header">
          <h2>Card Title</h2>
        </div>
        <div class="card-body">
          Lorem ipsum dolor sit amet consectetur adipisicing elit. Eos, et est
          ducimus recusandae odit amet modi ipsum aut facilis, nostrum alias
          dignissimos quidem dolor earum quis quia temporibus numquam!
          Assumenda.
        </div>
        <div class="card-footer">Footer</div>
      </div>
    </div>
    <div class="col-6">
      <div class="card">
        <div class="card-header">
          <h2>Card Title</h2>
        </div>
        <div class="card-body">
          Lorem ipsum dolor sit amet consectetur adipisicing elit. Eos, et est
          ducimus recusandae odit amet modi ipsum aut facilis, nostrum alias
          dignissimos quidem dolor earum quis quia temporibus numquam!
          Assumenda.
        </div>
        <div class="card-footer">Footer</div>
      </div>
    </div>
  </div>
  <div class="row mt-3">
    <div class="col-6">
      <div class="card bg-primary">
        <div class="card-header">
          <h2>Card Title</h2>
        </div>
        <div class="card-body">
          Lorem ipsum dolor sit amet consectetur adipisicing elit. Eos, et est
          ducimus recusandae odit amet modi ipsum aut facilis, nostrum alias
          dignissimos quidem dolor earum quis quia temporibus numquam!
          Assumenda.
        </div>
        <div class="card-footer">Footer</div>
      </div>
    </div>
    <div class="col-6">
      <div class="card">
        <div class="card-header bg-danger">
          <h2>Card Title</h2>
        </div>
        <div class="card-body">
          Lorem ipsum dolor sit amet consectetur adipisicing elit. Eos, et est
          ducimus recusandae odit amet modi ipsum aut facilis, nostrum alias
          dignissimos quidem dolor earum quis quia temporibus numquam!
          Assumenda.
        </div>
        <div class="card-footer">Footer</div>
      </div>
    </div>
  </div>
</div>
```

container-fluid -> permite que os cards ocupam toda a extensão da tela
card -> classe container do card
card-header -> cria um elemento header dentro do card
card-body -> classe que englobara o conteudo do seu card
card-footer -> cria um elemento footer dentro do card
bg-danger -> a classe bg altera a cor do background do elemento

## Customizando o bootstrap
