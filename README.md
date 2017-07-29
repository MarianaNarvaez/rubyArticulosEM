# rubyArticulosEM
## By: Edwin Montoya - emontoya@eafit.edu.co

# 1. Creating the Article Application

        $ rails article blog

# 2. Starting up the WebApp Server

        $ rails server

* Open browser: http://localhost:3000

# 3. Main page: "Hello World"

        $ rails generate controller Welcome index

        edit:
        app/views/welcome/index.html.erb
        config/routes.rb

# 4. Create REST routes        

* edit: config/routes.rb

        Rails.application.routes.draw do
          get 'welcome/index'

          resources :articles

          root 'welcome#index'
        end

* run:    
        $ rails routes

* output:

      Prefix Verb   URI Pattern                  Controller#Action
      welcome_index GET    /welcome/index(.:format)     welcome#index
      articles GET    /articles(.:format)          articles#index
             POST   /articles(.:format)          articles#create
      new_article GET    /articles/new(.:format)      articles#new
      edit_article GET    /articles/:id/edit(.:format) articles#edit
      article GET    /articles/:id(.:format)      articles#show
             PATCH  /articles/:id(.:format)      articles#update
             PUT    /articles/:id(.:format)      articles#update
             DELETE /articles/:id(.:format)      articles#destroy
        root GET    /                            welcome#index

# 5. Generate controller for 'articles' REST Services

        $ rails generate controller Articles

* modify: app/controllers/articles_controller.rb
* create: app/views/articles/new.html.erb    

* run: http://localhost:3000/articles/new    

# 6. Create a FORM HTML to enter data for an article

* edit: app/views/articles/new.html.erb:

      <%= form_for :article do |f| %>
      <p>
        <%= f.label :title %><br>
        <%= f.text_field :title %>
      </p>

      <p>
        <%= f.label :text %><br>
        <%= f.text_area :text %>
      </p>
      <p>
        <%= f.submit %>
      </p>
      <% end %>

* modify: app/views/articles/new.html.erb:

      <%= form_for :article, url: articles_path do |f| %>

      POST method and require 'create' action.

* add 'create' action to ArticlesController:

        class ArticlesController < ApplicationController
          def new
          end

          def create
            render plain: params[:article].inspect
          end
        end     

# 7. Creating the Article model

      $ rails generate model Article title:string text:text

* look db/migrate/YYYYMMDDHHMMSS_create_articles.rb:

      class CreateArticles < ActiveRecord::Migration[5.0]
        def change
          create_table :articles do |t|
            t.string :title
            t.text :text

            t.timestamps
          end
        end
      end

# 8. Running a Migration

run:

    $ rails db:migrate

# 9. Saving data in the controller

* Open app/controllers/articles_controller.rb

      def create
        @article = Article.new(params[:article])

        @article.save
        redirect_to @article
      end

* fix1:

      @article = Article.new(params.require(:article).permit(:title, :text))

* fix2:

      def create
        @article = Article.new(article_params)

        @article.save
        redirect_to @article
      end

      private
        def article_params
          params.require(:article).permit(:title, :text)
        end      

# 10. Showing Articles

* Route:

      article GET    /articles/:id(.:format)      articles#show

* Controller: add action in app/controllers/articles_controller.rb

      def show
        @article = Article.find(params[:id])
      end

* View: create a new file app/views/articles/show.html.erb

      <p>
        <strong>Title:</strong>
        <%= @article.title %>
      </p>

      <p>
        <strong>Text:</strong>
        <%= @article.text %>
      </p>    

# 11. Listing all articles

* Route:

      articles GET    /articles(.:format)          articles#index

* Controller: add action in app/controllers/articles_controller.rb

      def index
         @articles = Article.all
      end

* View: create a new file app/views/articles/index.html.erb

      <h1>Listing articles</h1>

      <table>
        <tr>
          <th>Title</th>
          <th>Text</th>
          <th></th>
        </tr>

        <% @articles.each do |article| %>
          <tr>
            <td><%= article.title %></td>
            <td><%= article.text %></td>
            <td><%= link_to 'Show', article_path(article) %></td>
          </tr>
        <% end %>
      </table>

# 12. Adding links

* View: Open app/views/welcome/index.html.erb

      <h1>Hello World EAFIT</h1>
      <%= link_to 'My Articles', controller: 'articles' %>  

* View: app/views/articles/index.html.erb

      <%= link_to 'New article', new_article_path %>    

* View: app/views/articles/new.html.erb


      <%= form_for :article, url: articles_path do |f| %>
        ...
      <% end %>

      <%= link_to 'Back', articles_path %>

* View: app/views/articles/show.html.erb

      <p>
        <strong>Title:</strong>
        <%= @article.title %>
      </p>

      <p>
        <strong>Text:</strong>
        <%= @article.text %>
      </p>

      <%= link_to 'Back', articles_path %>   

# 13. Updating Articles     

* Route:     

      article GET    /articles/:id(.:format)      articles#show

* Controller: edit action to the ArticlesController ->  app/controllers/articles_controller.rb

      def edit
      @article = Article.find(params[:id])
      end

* View: new page: app/views/articles/edit.html.erb

      <h1>Edit article</h1>

      <%= form_for(@article) do |f| %>

        <% if @article.errors.any? %>
          <div id="error_explanation">
            <h2>
              <%= pluralize(@article.errors.count, "error") %> prohibited
              this article from being saved:
            </h2>
            <ul>
              <% @article.errors.full_messages.each do |msg| %>
                <li><%= msg %></li>
              <% end %>
            </ul>
          </div>
        <% end %>

        <p>
          <%= f.label :title %><br>
          <%= f.text_field :title %>
        </p>

        <p>
          <%= f.label :text %><br>
          <%= f.text_area :text %>
        </p>

        <p>
          <%= f.submit %>
        </p>

      <% end %>

      <%= link_to 'Back', articles_path %>  

* Controller: update action in app/controllers/articles_controller.rb

      def update
        @article = Article.find(params[:id])

        if @article.update(article_params)
          redirect_to @article
        else
          render 'edit'
        end
      end

* View: add link 'edit' in app/views/articles/index.html.erb

      <table>
        <tr>
          <th>Title</th>
          <th>Text</th>
          <th colspan="2"></th>
        </tr>

        <% @articles.each do |article| %>
          <tr>
            <td><%= article.title %></td>
            <td><%= article.text %></td>
            <td><%= link_to 'Show', article_path(article) %></td>
            <td><%= link_to 'Edit', edit_article_path(article) %></td>
          </tr>
        <% end %>
      </table>

* View: add link 'edit' in app/views/articles/show.html.erb:

      ...
      <%= link_to 'Edit', edit_article_path(@article) %> |
      <%= link_to 'Back', articles_path %>

# 14. delete an Article

Route:

      DELETE /articles/:id(.:format)      articles#destroy  

Controller: app/controllers/articles_controller.rb

      def destroy
        @article = Article.find(params[:id])
        @article.destroy

        redirect_to articles_path
      end                                      

View: add 'delete' link to app/views/articles/index.html.erb

      ...
      <td><%= link_to 'Edit', edit_article_path(article) %></td>
      <td><%= link_to 'Delete', article_path(article), method: :delete,
              data: { confirm: 'Are you sure?' } %></td>
      ...

# 20. Deploy the Article Web App on Linux Centos 7.x (test or production)

* references:

      https://www.digitalocean.com/community/tutorials/how-to-use-postgresql-with-your-ruby-on-rails-application-on-centos-7


* Connect remote server: (user1 is a sudo user)

      local$ ssh user1@10.131.137.236
      Password: *****

      user1@test$

* verify and install rvm, ruby, rails, postgres and nginx

* install rvm (https://rvm.io/rvm/install)

        user1@test$ gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB

        user1@test$ \curl -sSL https://get.rvm.io | bash

* reopen terminal app:

        user1@test$ exit

        local$ ssh user1@10.131.137.236
        Password: *****

* install ruby 2.4.1

        user1@test$ rvm list known
        user1@test$ rvm install 2.4.1

* install rails

        user1@test$ gem install rails

* install postgres:

        user1@test$ sudo yum install -y postgresql-server postgresql-contrib postgresql-devel
        Password: *****

        user1@test$ sudo postgresql-setup initdb

        user1@test$ sudo vi /var/lib/pgsql/data/pg_hba.conf

        original:

        host    all             all             127.0.0.1/32            ident
        host    all             all             ::1/128                 ident

        updated:

        host    all             all             127.0.0.1/32            md5
        host    all             all             ::1/128                 md5

* run postgres:

        user1@test$ sudo systemctl start postgresql
        user1@test$ sudo systemctl enable postgresql

* Create Database User:

        user1@test$ sudo su - postgres

        user1@test$ createuser -s pguser

        user1@test$ psql

        postgres=# \password pguser
        Enter new password: changeme

        postgres=# \q

        user1@test$ exit
