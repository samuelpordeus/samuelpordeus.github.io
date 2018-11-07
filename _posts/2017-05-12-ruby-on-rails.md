---
layout: post
title: Aplicação de Gerenciamento de Projetos com Ruby on Rails
excerpt: Material do minicurso introdutório de Ruby on Rails que ministrei em evento do IEEE.
tags: [Web, Rails, Portuguese]
date: 2017-09-05
comments: true
---
# Minicurso - Ruby on Rails
Introdução ao Framework de desenvolvimento web *Ruby on Rails* e passo a passo da aplicação de gerenciamento de projetos. Abordaremos aqui os seguintes temas:
1. Arquitetura MVC
* Rotas
* Banco de dados e *migration files*
* Gerenciador de projetos
* Gemas e bundler
* Autenticação com Devise
* Front-end com bootstrap
------
## Introdução
Aos que estão no minicurso de Rails, boa tarde! Aos que estão acompanhando o material pelo meu site, bem-vindos!

Preparei esse minicurso com muito carinho, tentando falar coisas que eu queria que tivessem me explicado quando eu comecei a desenvolver com Rails.

Vamos aprender o básico de Rails enquanto desenvolvemos uma aplicação de gerenciamento de projetos chamada Gerentão, espero que vocês saiam daqui com pelo menos um pouco mais de conhecimento do que quando chegaram, vamos nessa.

## Arquitetura MVC
MVC (Modelo-visão-controlador), é um padrão de arquitetura de software utilizada na implementação de programas com interface de usuários. Dividimos o código em três partes: modelos, controladores e visões.
![MVC](https://vaidegrails.files.wordpress.com/2015/06/camadas_mvc.png?w=1000&h=451)
![MVC](https://daveh.io/user/pages/01.blog/the-model-view-controller-pattern/the-mvc-pattern.png)
Não tem problema não entender a razão da divisão em três camadas, principalmente da necessidade dos controladores, por ora! É muito mais fácil de se entender quando você tem um pouco de prática.

## Migration files e Active Record
O **Active Record** é o **M** do nosso **MVC**, é o ORM (Object Relational Mapping system) do Rails.
Dentre suas funções estão:
* Representar modelos e seus dados.
* Representar associações entre esses modelos.
* Representar heranças hierárquicas de modelos relacionados.
* Validar modelos antes que eles sejam adicionados ao banco de dados.
* Realizar operações no banco de dados de uma forma orientada à objetos.

**Migration Files** são uma forma conveniente de alterarmos as tabelas do nosso banco de dados, sem precisar utilizar comandos em SQL puro. Além disso, torna nossa aplicação independente de um banco de dados específico, podendo rodá-la em outra máquina sem a menor dificuldade, apenas rodando os arquivos de migração com um simples comando no terminal:

```sh
rails db:migrate
rake db:migrate
Os 2 comandos são equivalentes, o 1º foi adicionado recentemente no Rails 5
```
------

## Rotas
O Roteador do rails lê uma URL e a entrega para uma ação do controlador encarregado pela rota. As rotas ficam no arquivo **config/routes.rb**

Exemplo:
A requisição **GET /projetos/16** tendo a seguinte rota:
```ruby
get '/projetos/:id' to: 'projetos#show'
```
Ou seja, ao acessarmos a URL: http://127.0.0.1:3000/projetos/16 acionaremos o **show** do controlador **projetos** e receberemos o conteúdo no browser por meio da **visão/view**

------
## A aplicação que criaremos
Criaremos uma aplicação simples para gerenciarmos nossos projetos pessoais

------
## Iniciando a aplicação
Para começar um novo projeto, entre no terminal no terminal e entre com:
```sh
rails new gerentao
```
Depois disso, entre na pasta do **Gerentão**, para podermos testá-lo utilizando o comando:
```sh
rails server
```
Abra o navegador em http://127.0.0.1:3000 e confira se o Rails te retorna a página inicial!
![Sucesso](http://i.imgur.com/wsWDa8b.png)

Agora adicionaremos os Projetos à nossa aplicação!

Invés de gerar o modelo diretamente, adicionaremos o modelo e o controlador de projetos utilizando um único comando, o **Scaffold**. Scaffold (Andaime) no Rails invoca um modelo, uma *migration* para o banco de dados desse modelo, um controlador para manipulá-lo, visões para visualizar e manipular os dados, além de uma suíte de testes para tudo mencionado acima.

No terminal:  
```sh
rails generate scaffold Projeto user_id:integer nome:string descricao:text terminado:boolean
rails db:migrate
```

O modelo tem o id do criador do projeto, um nome, uma descrição e uma variável booleana que diz se o projeto foi terminado ou não, não precisamos nos preocupar com o ID do projeto, pois o rails cuida disso para nós!

-------
## Adicionando gemas
Gemas são módulos com códigos prontos, normalmente feitas por pessoas incríveis, que adicionamos às nossas aplicações para nos ajudar a desenvolver.

Abra o arquivo Gemfile (Cuidado para não confundir com Gemfile.lock) e você verá que o Rails já vem com uma série de gemas que são essenciais para seu funcionamento.

A primeira gema que instalaremos, Devise, é uma solução fantástica, seguindo os mais altos padrões do desenvolvimento web, para a autenticação de usuários no Rails.

### Instalando o Devise
Dentro da Gemfile, adicionaremos o Devise embaixo de ***gem 'sqlite3'***
```ruby
gem 'devise'
```
Depois disso, salve a Gemfile e rode o comando no terminal:
```sh
bundle install
```
Pronto, gema instalada! Masssss, ainda teremos que respeitar uma etapa. Muitas vezes, quando instalamos uma gema, não temos de seguir nenhum passo posterior, mas como o Devise é uma solução muito completa, teremos que rodar o seguinte comando no terminal para podermos utilizá-la, efetivamente:
```sh
rails generate devise:install
rails generate devise User
rake db:migrate
```
Seguiremos as instruções que o rails generate devise:install nos dá e depois seguiremos às configurações.

Para podermos requerir que o usuário esteja logado antes de realizar qualquer ação em nossa aplicação, em **app/controllers/application_controller.rb** colocaremos dentro da classe ApplicationController:
```ruby
before_action :authenticate_user!
```

Adicionaremos também no **routes.rb**

### Instalando o Boostrap
Ao instalarmos esta gema, teremos a integração do popular framework de front-end, Bootstrap. que deixará o **Gerentão** com uma aparência mais agradável e profissional!
```ruby
gem 'twitter-bootstrap-rails'
```

No terminal, dê o **bundle install** e depois rode:

```sh
rails generate bootstrap:install static
```
Se quiser aprender mais sobre bootstrap, acesse:  
http://getbootstrap.com/

Pronto, agora vamos abrir novamente o servidor com **rails server** e testar um pouco da nossa aplicação!

------
### Adicionando a relação entre projetos e usuário
Vamos ao **models/user.rb** e dentro da classe, adicionaremos:
```ruby
has_many :projetos
```
Agora vamos ao **models/projeto.rb** e dentro da classe, adicionaremos:
```ruby
belongs_to :user
```
Certo, agora, com o auxílio do Active Record, os projetos estão relacionados com os usuários!

### Apenas o criador do projeto pode editá-lo ou terminá-lo
Vamos agora às visões. Precisamos impedir o acesso de um usuário qualquer para deletar ou editar um projeto criado por um outro usuário. Abriremos em **app/views/index.html.erb**.

Um arquivo **ERB** permite que o programador faça a interação entre o HTML e um código em Ruby, neste caso, dos controladores.

Embaixo de show, adicionaremos um if, envolvendo o edit e destroy:
```html
<% if current_user == projeto.user %>
<td><%= link_to 'Edit', edit_projeto_path(projeto) %></td>
<td><%= link_to 'Destroy', projeto, method: :delete, data: { confirm: 'Are you sure?' } %></td>
<% end %>
```

Repetiremos o processo para a visualização individual de um projeto, em **app/views/show.html.erb**:
```html
<% if current_user == @projeto.user %>
  <%= link_to 'Edit', edit_projeto_path(@projeto) %> |
<% end %>
<%= link_to 'Back', projetos_path %>
```

Agora voltando rapidamente ao **app/views/index.html.erb**, vamos adicionar um link simples de logout:
```html
<%= link_to 'New Projeto', new_projeto_path %> |
<%= link_to('Logout', destroy_user_session_path, :method => :delete) %>
```

Podemos aproveitar esse momento para deixar os nomes em português também.

### Vamos associar o usuário que está logado ao projeto no momento da criação.
Abra o **app/controllers/projetos_controller.rb** e dentro da ação de create coloque abaixo da primeira linha (@projeto = Projeto.new(projeto_params)):
```ruby
@projeto.user = current_user
@projeto.terminado = false
```
No final do arquivo, remova a permissão de user_id em **projeto_params**:
```ruby
def projeto_params
  params.require(:projeto).permit(:nome, :descricao, :terminado)
end
```

Também removeremos dentro da **View** form.html.erb

### Adicionando tarefas
```sh
rails generate model Tarefa projeto_id:integer nome:string terminada:boolean
rails generate controller Tarefas new create finish
rails db:migrate
```
Faremos as associações como entre usuário e projetos, só que entre projeto e tarefas agora:
Vamos ao **models/projeto.rb** e dentro da classe, adicionaremos:
```ruby
has_many :tarefas
```
Agora vamos ao **models/tarefa.rb** e dentro da classe, adicionaremos:
```ruby
belongs_to :projeto
```

Já vamos às rotas, reparem que o arquivo **routes.rb** deve estar mais ou menos assim:
```ruby
Rails.application.routes.draw do
  get 'tarefas/new'

  get 'tarefas/create'

  get 'tarefas/finish'

  devise_for :users
  resources :projetos
  root to: 'projetos#index'
end
```
As rotas não foram criadas com resource, de forma bonitinha, pois dessa vez decidimos (No caso, eu decidi, hehe) fazer na mão, esse esforço teria sido poupado como no caso dos projetos se eu tivesse utilizado um Scaffold para gerar as Tarefas, mas não se preocupem com isso, estamos indo devagar para ter dúvidas e aprender mais.

Ela deverá ficar assim:
```ruby
Rails.application.routes.draw do
  devise_for :users
  resources :projetos
  root to: 'projetos#index'
  resources :tarefas
  post '/tarefas/finish/:id', to: 'tarefas#finish', as: 'finish_tarefa'
end
```

Depois disso, olhem o controlador de tarefas.

Sim, está vazio. Mas não se desespere! É mais fácil do que parece para configurá-lo!

Primeiramente vamos configurar o new e o create. New é um **GET** e Create é um **POST**, em um caso geral, quando quisermos criar algo, vamos recebemos via HTTP numa requisição **GET** uma página "New" e quando queremos enviar o formulário com o que iremos criar, enviamos uma requisição "Create" com **POST**
```ruby
  def new
    @tarefa = Tarefa.new
  end

  def create
    @tarefa = Tarefa.new(tarefa_params)
    @tarefa.terminada = false
    @tarefa.projeto_id = params[:tarefa][:projeto_id]
    @tarefa.save
    redirect_to request.referer, notice: 'Tarefa foi criada com sucesso.'
  end
```

Como queremos adicionar as tarefas dentro da página dos projetos, entramos em um terreno mas complicado, mas ainda simples.

Dentro do controlador de projeto, iremos adicionar dentro de **show** a seguinte linha:
```ruby
@tarefa = Tarefa.new
```
Pronto, vamos adicionar na visão de Show dos projetos uma tabela com todas as tarefas e a capacidade de adicioná-las, via formulário e ERB:
```html
<% if @projeto.tarefas %>
  <table>
    <thead>
      <tr>
        <th>Nome</th>
        <th>Status</th>
        <th colspan="3"></th>
      </tr>
    </thead>
    <tbody>
      <% @projeto.tarefas.each do |tarefa| %>
        <tr>
          <td><%= tarefa.nome %></td>
          <% if tarefa.terminada %>
            <td>Concluída</td>
          <% else %>
            <td>Pendente</td>
          <% end %>
        </tr>
      <% end %>
    </tbody>
  </table>
<% end %>

<%= form_for(@tarefa) do |f| %>
  <div class="field">
    <%= f.label :nome %>
    <%= f.text_field :nome %>
  </div>
  <div class="actions">
    <%= hidden_field_tag "tarefa[projeto_id]", "#{@projeto.id}" %>
    <%= f.submit %>
  </div>
<% end %>
```
Já que estamos manipulando Tarefas dentro de uma View de Projetos, precisamos da *hidden field tag* para passar o ID do projeto para o controlador das Tarefas.

Agora que já temos como adicionar as tarefas, vamos marcá-las como concluída, uma linha abaixo da parte na tabela onde tem 'Concluída':

```html
<td><%= link_to 'Concluir', finish_tarefa_path(tarefa), method: :post %></td>
```

Analogamente, vamos adicionar uma função finish para os projetos, fazendo uma nova ação no controlador, a sua rota correspondente e algumas mudanças na visão.

**routes.rb**
```ruby
Rails.application.routes.draw do
  devise_for :users
  resources :projetos
  root to: 'projetos#index'
  resources :tarefas
  post '/tarefas/finish/:id', to: 'tarefas#finish', as: 'finish_tarefa'
  post '/projetos/finish/:id', to: 'projetos#finish', as: 'finish_projeto'
end
```
**projetos_controller.rb**
```ruby
def finish
  @projeto = Projeto.find(params[:id])
  @projeto.terminado = !@projeto.terminado
  @projeto.save
  redirect_to request.referer, notice: 'Estado do projeto alterado com sucesso.'
end
```
**index.html.erb**
```html
<p id="notice"><%= notice %></p>

<h1>Projetos</h1>

<table>
  <thead>
    <tr>
      <th>User</th>
      <th>Nome</th>
      <th>Descricao</th>
      <th>Terminado</th>
      <th colspan="3"></th>
    </tr>
  </thead>

  <tbody>
    <% @projetos.each do |projeto| %>
      <tr>
        <td><%= projeto.user.email %></td>
        <td><%= projeto.nome %></td>
        <td><%= projeto.descricao %></td>
        <td><%= projeto.terminado %></td>
        <td><%= link_to 'Show', projeto %></td>
        <% if current_user == projeto.user %>
        <td><%= link_to 'Editar', edit_projeto_path(projeto) %></td>
        <td><%= link_to 'Concluir', finish_projeto_path(projeto), method: :post %></td>
        <td><%= link_to 'Apagar', projeto, method: :delete, data: { confirm: 'Tem certeza?' } %></td>
        <% end %>
      </tr>
    <% end %>
  </tbody>
</table>

<br>

<%= link_to 'New Projeto', new_projeto_path %> |
<%= link_to('Logout', destroy_user_session_path, :method => :delete) %>
```

------
### Adicionando estilo com HTML/CSS
Terminamos as funcionalidades básicas do Gerentão, que tal adicionarmos um pouco de estilo às views agora? Vamos adicionar um arquivo: **app/assets/stylesheets/custom.css.scss** para usarmos como stylesheet. Como essa etapa não é o foco do minicurso e eu não sei nem se teremos tempo de executá-la, deixei os códigos prontos aqui, mas num geral, foi só ir pesquisando componentes no site do bootstrap e ir deixando a aplicação mais bonita!

**app/views/layouts/application.html.erb**
```html
<!DOCTYPE html>
<html>
  <head>
    <title>Gerentão</title>
    <%= csrf_meta_tags %>

    <%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
  </head>
  <body>
    <h1 class="text-center">Gerentão</h1>
    <div class="container">
      <div class="jumbotron">
        <%= yield %>
      </div>
      <div class ="text-right">
        <%= button_to 'Sair', destroy_user_session_path, :class => "btn btn-default", :method => :delete %>
      </div>
    </div>
  </body>
</html>
```

**app/assets/stylesheets/custom.css.scss**
```css
h1 {
  font-size: 10em;
}
```

**app/views/projetos/index.html.erb**
```html
<p id="notice"><%= notice %></p>
<div class="row">
  <table class="table">
    <thead>
      <tr>
        <th>Criador</th>
        <th>Projeto</th>
        <th>Descrição</th>
        <th>Ativo</th>
      </tr>
    </thead>

    <tbody>
      <% @projetos.each do |projeto| %>
        <tr>
          <td class="col-md-1"><%= projeto.user.email %></td>
          <td class="col-md-2"><%= projeto.nome %></td>
          <td class ="col-md-4"><%= projeto.descricao %></td>
          <% if projeto.terminado %>
            <td>Inativo</td>
          <% else %>
            <td>Ativo</td>
          <% end %>
          <td><%= link_to 'Detalhes', projeto %></td>
          <% if current_user == projeto.user %>
          <td><%= link_to 'Editar', edit_projeto_path(projeto) %></td>
          <td><%= link_to 'Concluir', finish_projeto_path(projeto), method: :post %></td>
          <td><%= link_to 'Apagar', projeto, method: :delete, data: { confirm: 'Tem certeza?' } %></td>
          <% end %>
        </tr>
      <% end %>
    </tbody>
  </table>
  <%= button_to 'Novo Projeto', new_projeto_path, :class => "btn btn-default", :method => :get %>
</div>
<br>
```
**app/views/projetos/show.html.erb**
```html
<p id="notice"><%= notice %></p>

<p>
  <strong>Projeto:</strong>
  <%= @projeto.nome %>
</p>

<p>
  <strong>Criador:</strong>
  <%= @projeto.user.email %>
</p>

<p>
  <strong>Descrição:</strong>
  <%= @projeto.descricao %>
</p>

<p>
  <strong>Status:</strong>
  <% if @projeto.terminado %>
    Inativo
  <% else %>
    Ativo
  <% end %>
</p>

<% if @projeto.tarefas %>
  <table class="table table-condensed">
    <thead>
      <tr>
        <th>Nome</th>
        <th>Status</th>
      </tr>
    </thead>
    <tbody>
      <% @projeto.tarefas.each do |tarefa| %>
        <tr>
          <td><%= tarefa.nome %></td>
          <% if tarefa.terminada %>
            <td class="text-success">Concluída</td>
          <% else %>
            <td class="text-warning">Pendente</td>
            <td>
                <%= link_to '<i class="glyphicon glyphicon-ok" aria-hidden="true"></i>'.html_safe, finish_tarefa_path(tarefa), method: :post %>
            </td>
          <% end %>
        </tr>
      <% end %>
    </tbody>
  </table>
<% end %>

<% if current_user == @projeto.user %>
  <%= form_for(@tarefa) do |f| %>
    <div class="field">
      <%= f.text_field :nome %>
    </div>
    <div class="actions">
      <%= hidden_field_tag "tarefa[projeto_id]", "#{@projeto.id}" %>
      <%= f.submit "Criar nova tarefa", :class => "btn btn-primary btn-mini" %>
    </div>
  <% end %>
  <%= link_to 'Edit', edit_projeto_path(@projeto) %> |
<% end %>
<%= link_to 'Back', projetos_path %>
```

Espero que vocês tenham gostado do minicurso, que eu não tenha errado muito e que eu esteja bem pleno e sem dor de garganta também!

------
Referências:  
https://daveh.io/blog/the-model-view-controller-pattern  
http://guides.rubyonrails.org/command_line.html  
http://guides.rubyonrails.org/routing.html  
http://guides.rubyonrails.org/active_record_basics.html  
http://guides.rubyonrails.org/association_basics.html
http://guides.rubyonrails.org/action_controller_overview.html
http://getbootstrap.com
