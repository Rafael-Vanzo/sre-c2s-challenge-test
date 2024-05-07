## **Relatório de correções de Bugs e Melhorias**

## 1. Corrige configuração de localização (commit b8520e1328b4ccb8a366d3159c92d194862b2b5a)

**Descrição do Bug:** A configuração de localização estava incorreta, causando problemas ao rodar o comando "rails db:create".

**Localização do Bug:** application.rb, linha 18.

**Correção Realizada:** Antes, a configuração estava incorreta e causava um erro ao rodar o comando "rails db:create". A linha 18 estava assim: `congif.locales(brasil_america)`. A correção foi feita ajustando a configuração de localização. A linha 18 foi alterada para: `config.i18n.default_locale = "pt-BR"`. 
Aproveitei para adicionar uma configuração de time_zone para o projeto: `config.time_zone = "Brasilia"`

## 2. Modifica rota principal (commit 6b04a74b95852d3a3ef68fc89926ad205906f7d9)

**Descrição da Alteração:** A rota principal (root) estava direcionando para uma página ou ação não especificada.

**Localização da alteração:** config/routes.rb, linha 3.

**Modificação Realizada:** Antes, a rota principal não estava definida, estava apenas na pagina padrão de quando se cria uma nova aplicação em Rails. A modificação foi feita ajustando a rota principal para direcionar para a ação index do controlador leads. A linha 3 foi alterada para: `root to: "leads#index"`
Após essa alteração, a rota principal agora direciona corretamente para a ação index do controlador leads.

## 3. Corrige bug ao abrir lead#index (commit 91b68d0cae2e2827160b78b3e7736a4a7f07ddef)

**Descrição do Bug:** Ocorria um erro ao tentar abrir lead_url(root) devido a um link de stylesheet incorreto.

**Localização do Bug:** layouts/application.html.erb, linha 8.

**Correção Realizada:** Antes, a linha 8 estava assim: `<%= stylesheet_link_tag "tailwind", "inter-font", "data-turbo-track": "reload" %>`. 
A correção foi feita ajustando o link de stylesheet. A linha 8 foi alterada para: `<%= stylesheet_link_tag "application.tailwind", "inter-font", "data-turbo-track": "reload" %>`
Após essa alteração, lead_url(root) abre corretamente sem erros.

## 4. Corrige bug nos parâmetros de criação de lead (commit f77e89d80976b3f92c5832682bb1eba280745336)

**Descrição do Bug:** Os parâmetros de criação de lead estavam incompletos, faltando os campos 'name' e 'phone'.

**Localização do Bug:** leads_controller.rb, linha 68.

**Correção Realizada:** Antes, a linha 68 estava assim: `params.require(:lead).permit(:email)`.
A correção foi feita ajustando os parâmetros permitidos para a criação de lead. A linha 68 foi alterada para:
`params.require(:lead).permit(:name, :email, :phone)`.
Após essa alteração, a criação de lead agora inclui corretamente os campos 'name', 'email' e 'phone'.

## 5. Corrige bug no caminho para edição de lead (commit c2210b8f380f019c0a51d792a8e72c2f07d23871)

**Descrição do Bug:** O caminho para a edição de lead estava incorreto, assim como a variável utilizada para referenciar o lead.

**Localização do Bug:** /views/leads/show.html.erb, linhas 9 e 11.

**Correção Realizada:** A correção foi feita ajustando o caminho para a edição de lead e a variável utilizada para referenciar o lead.
Linha 9: estava com o `edit_lead_path` incorreto, estava `edi_lead_path`. 
Linha 11: estava com `@lead` incorreto, estava `@lea`.
Após essa alteração, o caminho para a edição de lead e a destruição de lead estão corretos.

## 6. Adiciona validação de email e telefone (commit a18ad70fa3f7face017ef980ae9dba66c7ce609c)

**Descrição do Bug:** As validações para os campos de email e telefone estavam ausentes.

**Localização do Bug:** /models/lead.rb

**Correção Realizada:** A modificação foi feita adicionando validações para os campos de email e telefone. O modelo Lead foi alterado para:
```ruby
class Lead < ApplicationRecord
  validates :email, presence: true, uniqueness: false, format: { with: URI::MailTo::EMAIL_REGEXP, message: :invalid }
  validates :name, presence: true
  validates :phone, presence: true, format: { with: /\A\(?\d{2}\)?\s?\d{4,5}-?\d{4}\z/, message: :invalid }
end
```
Após essa alteração, o modelo Lead agora valida corretamente os campos de email e telefone.

## 7. Altera datatype do phone para string (commit 983ada348378b22cf4c70a56f29e58bba147f5cc)

**Descrição da Alteração:** O tipo de dado da coluna `phone` estava incorreto.

**Localização da Alteração:** Migration do ActiveRecord.

**Modificação Realizada:** Foi criada uma nova migration para alterar o tipo de dado da coluna phone para string. A migration criada foi:
```ruby
class AlteraTipoColunaPhone < ActiveRecord::Migration[7.1]
  def change
    change_column :leads, :phone, :string
  end
end
```
Após essa alteração, o tipo de dado da coluna `phone` agora é `string`, permitindo a validação correta do formato do telefone.

## 8. Instala gem "simple_form" (commit db54f9c41ef694a30ff76755fecce168f25c2aa1)

**Descrição da Alteração:** A gem "simple_form" não estava instalada.

**Localização da Alteração:** Gemfile.

**Modificação Realizada:** Foi adicionada a `gem "simple_form"` ao Gemfile e executado o comando `bundle install` para instalar a gem.

## 9. Corrige bug em edit.html.erb (commit c587f41db7e84e093b60beaf9da3f1e5e9836c12)

**Descrição do Bug:** Havia um bug no arquivo edit.html.erb que impedia a página de abrir.

**Localização do Bug:** /views/leads/edit.html.erb.

**Correção Realizada:**  A modificação foi feita para corrigir o nome do método `simplo_form` para `simple_form_for` e para usar a variável de instância `@lead` em vez da variável local `lead`. O código corrigido ficou assim:
```erb
<%= simple_form_for @lead, html: { class: "contents" } do |form| %>
  <% if @lead.errors.any? %>
    <div id="error_explanation" class="bg-red-50 text-red-500 px-3 py-2 font-medium rounded-lg mt-3">
      <h2><%= pluralize(@lead.errors.count, "error") %> prohibited this @lead from being saved:</h2>

      <ul>
        <% @lead.errors.each do |error| %>
          <li><%= error.full_message %></li>
        <% end %>
      </ul>
```
Após essa alteração, o bug no arquivo edit.html.erb foi corrigido e a página agora abre corretamente.

## 10. Adiciona div na view/leads/edit (commit 98ea41f9f31733ee51ad95aa9451d1793f2a4bfb)

**Descrição da Alteração:** Foi adicionada uma div para manter a consistência com o formulário de criação de lead.

**Localização da Alteração:** /views/leads/edit.html.erb linha 1.

**Modificação Realizada:**  A modificação foi feita para adicionar uma div com classes de estilo. O código modificado ficou assim:
```erb
<div class="mx-auto md:w-2/3 w-full">
  <%= simple_form_for @lead, html: { class: "contents" } do |form| %>
```
Após essa alteração, o formulário de edição de lead agora está em conformidade com o estilo do formulário de criação de lead.

## 11. Corrige bug sem tratamento de erro em leads#show para ID inexistente (commit f7776e229a80668149b0ae4b28f192db674adf49)

**Descrição do Bug:** Havia um bug na ação show do controlador leads que causava um erro sem tratamento quando um ID inexistente era usado.

**Localização do Bug:** Método `set_lead` no /controllers/leads_controller.rb linha 63

**Correção Realizada:**  A modificação foi feita para usar find_by em vez de find e adicionar um método verify_lead_id para tratar o caso em que o lead não é encontrado. O código modificado ficou assim:
```ruby
def set_lead
  verify_lead_id unless @lead = Lead.find_by(id: params[:id])
end

def verify_lead_id
  respond_to do |format|
    format.html { redirect_to leads_url, alert: t('leads.errors.show.not_found') }
    format.json { render json: { error: t('leads.errors.show.not_found') }, status: :not_found }
  end
end
```
Após essa alteração, o erro é tratado corretamente quando um ID inexistente é usado na ação show do controlador leads.

## 12. Adiciona tratamento de erro no leads controller

**Descrição da Alteração:** Foram adicionados tratamentos de erro nos métodos `destroy`, `update`, `create` e `index` do controlador `leads`.

**Localização da Alteração:** leads_controller.rb.

**Modificação Realizada:**
**Commit 1 (a4ef291c2ba226e6a6dbae788f9a7d22d42fe481):** Adiciona tratamento de erro no método `destroy`

O método `destroy` foi modificado para tratar o caso em que o lead não pode ser destruído.
```ruby
def destroy
  begin
    @lead.destroy!
  rescue ActiveRecord::RecordNotDestroyed => e
    respond_to do |format|
      format.html { redirect_to leads_url, notice: t('leads.destroy.error') }
      format.json { render json: { error: t('leads.destroy.error') }, status: :unprocessable_entity }
    end
    return
  end

  respond_to do |format|
    format.html { redirect_to leads_url, notice: t('leads.destroy.success') }
    format.json { head :no_content }
  end
end
```
**Commit 2 (0de271923a8b1b2f9182e1a31572e12c20441fff):** Adiciona tratamento de erro no método `update`

O método `update` foi modificado para tratar o caso em que o lead não pode ser atualizado.
```ruby
def update
  respond_to do |format|
    if @lead.update(lead_params)
      format.html { redirect_to lead_url(@lead), notice: t('leads.update.success') }
      format.json { render :show, status: :ok, location: @lead }
    else
      @errors = @lead.errors.messages.transform_values(&:first)
      format.html { render :edit, status: :unprocessable_entity }
      format.json { render json: @errors, status: :unprocessable_entity }
    end
  end
end
```
**Commit 3 (02a4f3f9b1a75b01200673c160c38a9243558c15):** Adiciona tratamento de erro no método `create`

O método `create` foi modificado para tratar o caso em que o lead não pode ser criado.
```ruby
def create
  @lead = Lead.new(lead_params)

  respond_to do |format|
    if @lead.save
      format.html { redirect_to lead_url(@lead), notice: t('leads.create.success') }
      format.json { render :show, status: :ok, location: @lead }
    else
      @errors = @lead.errors.messages.transform_values(&:first)
      format.html { render :edit, status: :unprocessable_entity }
      format.json { render json: @errors, status: :unprocessable_entity }
    end
  end
end
```
**Commit 4 (9516f3342f4380f8e52908a51f984c9bc91470a2):** Adiciona tratamento de erro no método `index`

O método `index` foi modificado para tratar o caso em que os leads não podem ser recuperados.
```ruby
def index
  begin
    @leads = Lead.all
  rescue => e
    respond_to do |format|
      format.html { redirect_to leads_url, notice: t('leads.index.error') }
      format.json { render json: { error: t('leads.index.error') }, status: :unprocessable_entity }
    end
    return
  end
end
```
Após essas alterações, os métodos `destroy`, `update`, `create` e `index` do controlador `leads` agora têm tratamento de erro adequado.

## 13. Internacionalização i18n do projeto (commit bffd61e9c55eede0c3297335c89388840d602c4a)

**Descrição da Alteração:** Foi implementada a internacionalização (i18n) em todo o projeto. Todos os textos que estavam codificados diretamente foram substituídos por chaves i18n.

**Localização da Alteração:** Todo o projeto.

**Modificação Realizada:** O código original tinha textos codificados diretamente, como:
```ruby
format.html { redirect_to leads_url, notice: 'Lead was successfully destroyed.' }
```
A modificação foi feita para substituir esses textos por chaves i18n, como:
```ruby
format.html { redirect_to leads_url, notice: t('leads.destroy.success') }
```
Após essa alteração, o projeto agora está preparado para a internacionalização, com todos os textos substituídos por chaves i18n e padronizado para Português do Brasil (pt-BR).

## 14. Adiciona downcase email e formatação de telefone (commit 1321fabb8f0c7abeb8547d80baa08ad005e2d439)

**Descrição da Alteração:** Foram adicionados dois métodos `before_save` no `lead model` para formatar o campo `email` e `phone`. O método `downcase_email` converte o email para minúsculas e o método `format_phone` formata o número de telefone.

**Localização da Alteração:** models/leads.rb Linhas 2 e 3

**Modificação Realizada:** 
Linha 2 foi adicionado o `before_save :downcase_email` que chama o seguinte metodo:
```ruby
  def downcase_email
    self.email = email.downcase
  end
```
Linha 3 foi adicionado o `before_save :format_phone` que chama o seguinte metodo:
```ruby
  def format_phone
    cleaned_phone = phone.gsub(/[^0-9]/, "")
    
    if cleaned_phone.length == 11
      self.phone = "(#{cleaned_phone[0..1]}) #{cleaned_phone[2..6]}-#{cleaned_phone[7..-1]}"
    elsif cleaned_phone.length == 10
      self.phone = "(#{cleaned_phone[0..1]}) #{cleaned_phone[2..5]}-#{cleaned_phone[6..-1]}"
    else
      self.phone = cleaned_phone
    end
  end
```
Após essa alteração, o modelo Lead agora formata o campo email para minúsculas e formata o campo phone antes de salvar.

## 15. Configura "simple_form" (commit dadb16c59ea062d28c65039681b2561a97895af9)

**Descrição da Alteração:** Foi configurado a gem `"simple_form"` no projeto. Foram criados os arquivos de inicialização e de localização para o `"simple_form"`.

**Localização da Alteração:** config/initializers/simple_form.rb e config/locales/simple_form.en.yml

**Modificação Realizada:** 
Foram criados os arquivos `config/initializers/simple_form.rb` e `config/locales/simple_form.en.yml` para configurar o `"simple_form"`.

## 16. Corrige tailwind error ao iniciar servidor (commit 6b9da8aaf7393844ddde4db31fad9698747f033c)

**Descrição da Alteração:** Foi corrigido um erro com o Tailwind que estava ocorrendo ao iniciar o servidor. A correção envolveu a execução do script `bin/dev` e a concessão de permissões de execução para o mesmo.

**Localização da Alteração:** app/views/layouts/application.html.erb linha 8

**Modificação Realizada:** Foi executado o script `bin/dev` para corrigir o erro com o Tailwind. Como o script não tinha permissões de execução, foi necessário conceder essas permissões usando o comando `chmod +x bin/dev`.

## 17. Extraí validador de email (commit 7b49967db1c4420f24a6de792d177e3794b0314b)

**Descrição da Alteração:** Foi extraído o método de validação de email que estava no modelo para um validador personalizado `EmailValidator`. A mudança foi feita porque o validador anterior considerava "example@example" como um email válido, o que não é correto, pois não há um domínio após o "@".

**Localização da Alteração:** app/models/lead.rb linha 4 e adiciona arquivo app/validators/email_validator.rb

**Modificação Realizada:** Alterei o codigo no model para:
```ruby
  validates :email, presence: true, uniqueness: { case_sensitive: false }, email: true
```
Adicionei o novo validator para o email:
```ruby
class EmailValidator < ActiveModel::EachValidator
    def self.compliant?(value)
        email_regexp = /\A[\w+\-.]+@[a-z\d\-.]+(\.[a-z\dz\-]+)*\.[a-z]+\z/i

        value.match(email_regexp)
    end

    def validate_each(record, attribute, value)
        return if value.blank? || self.class.compliant?(value)

        record.errors.add(attribute, I18n.t("errors.messages.invalid"))
    end
end
```
Após essa alteração, a validação de email agora é feita pelo EmailValidator, que considera "example@example" como um email inválido.

## 18. Refatoração do Simple Form (commit 026c40fa26b9a3935c2cf580f83eff35243e642f)

**Descrição da Alteração:** A refatoração foi realizada para otimizar a utilização do Simple Form, que facilita a criação de formulários e a manipulação de erros. Anteriormente, o código não estava aproveitando todas as funcionalidades do Simple Form. Além disso, foi implementado o DRY, para não repetir codigo nas views.

**Localização da Alteração:** _form.html.erb, new.html.erb, edit.html.erb e leads_controller.rb:40.

**Modificação Realizada:** A view parcial _form.html.erb foi refatorada para utilizar o Simple Form de maneira mais eficiente. As mensagens de erro agora são gerenciadas automaticamente pelo Simple Form. Além disso, a view parcial agora é utilizada tanto para a criação quanto para a edição de leads. O código refatorado é o seguinte:
```erb
<%= simple_form_for(lead, html: { class: 'contents', novalidate: true }) do |f| %>
  <div class="my-5">
    <%= f.input :name, label: t('leads.form.name'), error: false, input_html: { class: "block shadow rounded-md border border-gray-200 outline-none px-3 py-2 mt-2 w-full" } %>
    <%= f.error :name, class: 'text-red-500'%>
  </div>

  <div class="my-5">
    <%= f.input :email, label: t('leads.form.email'), error: false, input_html: { class: "block shadow rounded-md border border-gray-200 outline-none px-3 py-2 mt-2 w-full" } %>
    <%= f.error :email, class: 'text-red-500'%>
  </div>

  <div class="my-5">
    <%= f.input :phone, label: t('leads.form.phone'), error: false, input_html: { class: "block shadow rounded-md border border-gray-200 outline-none px-3 py-2 mt-2 w-full" } %>
    <%= f.error :phone, class: 'text-red-500'%>
  </div>

  <div class="inline">
    <%= f.button :submit, t('leads.form.create'), class: "rounded-lg py-3 px-5 bg-blue-600 text-white inline-block font-medium cursor-pointer" %>
  </div>

  <%= link_to t('leads.form.cancel'), leads_path, class: "rounded-lg py-3 px-5 bg-gray-200 text-gray-700 inline-block font-medium cursor-pointer" %>
<% end %>
```
Com essa refatoração, a aplicação está seguindo o princípio DRY. Acredito que era isso que o teste esperava que fizessemos, pois o simple_form estava la e não estava sendo utilizado corretamente.

## 19. Em relação a testes:
Como este é um projeto pequeno, preferi focar em melhorias ao invés da criação de testes. Acredito que as melhorias agregaram mais neste pequeno projeto. Porem testes de MVC seriam muito bem vindos.
