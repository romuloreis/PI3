# Página simples para criação de vendedores

No arquivo _Views/Sellers/Index_, criar o link para "Create" acima da tabela 

```html

<p>
<!--classes btn e btn-default são do bootstrap-->
    	<a asp-action="Create" class="btn btn-default">Create New</a>
</p>

```

No arquivo _SellersController_,vamos criar nossa ação "Create" do tipo GET, que é o padrão.

```cs
    public IActionResult Create()
        {
            return View();
        }
        
```

No diretótio _Views/Sellers_, devemos criar a view "Create" com template vazio **(Não marcar parcial)**.

Após isso, deve-se testar o botão "create new"

Vamos começar a criar o formulário, deixando de lado às tags de validação.

```html 

<h2>@ViewData["Title"]</h2>
<h4>Seller</h4>
<hr />
<div class="row">
    <!--md-4 corresponde a um terço da tela-->
    <div class="col-md-4">
        <!--asp-action vai chamar a action create, mas como post. Ainda não criamos esse método. -->
        <form asp-action="Create">
            <div class="form-group">
                <!--O tag helper label asp-for vai aparecer internamente no campo,
        informando qual é o nome/label do campo-->
                <label asp-for="Name" class="control-label"></label>
                <input asp-for="Name" class="form-control" />
            </div>
        </form>
     </div>
</div>

```

Note que ele não reconhece **Name**, pois não importamos isso no topo da página, então vamos importa

```html

@model ProjectName.Models.ViewModels.SellerFormViewModel

```

Vamos replicar os campos do formulário

```html

<div class="form-group">
               <label asp-for="Email" class="control-label"></label>
                <input asp-for="Email" class="form-control" />
            </div>
            <div class="form-group">
                <label asp-for="BirthDate" class="control-label"></label>
                <input asp-for="BirthDate" class="form-control" />
            </div>
            <div class="form-group">
                <label asp-for="BaseSalary" class="control-label"></label>
                <input asp-for="BaseSalary" class="form-control" />
            </div>
            
<!--Iremos incluir o campo departamento mais tarde→

```

Ao testar, você vai reparar que falta o botão para chamar a action “Create”, então vamos criar o botão “create”.

```html

<div class="form-group">
       <input type="submit" value="Create" class="btn btn-default" />
</div>

```

Nota que ao enviar o formulário, o item não é criado, pois foi chamado a action “Create” do método POST.
Nós não temos ainda esse método.

Antes de criar ele, devemos criar o método para inserir (Insert) novos registros no arquivo _Services/SellerService_,
afinal é ele o responsável por acessar os dados.

```cs

        public Insert(Seller obj)
        {
            _context.Add(obj);
            /*Confirma a inserção no banco*/
            _context.SaveChanges();
        }
        
```

Agora sim, no _SellersController_, vamos implementar a ação "Create" com método POST 

O método create recebe um objeto Seller, Nós vamos querer que o objeto Seller seja passado 
como parâmetro no método “Create” para podermos instanciar esse objeto para inserir no banco. 
O próprio framework vai fazer essa correlação, basta declarar na chamada do método Seller x;

```cs

/*Seller seller*/
public IActionResult Create(Seller seller){
  /*vamos precisar importar os models*/
  /*basta chamar o método de inserção de dados do serviço que criamos*/
  _sellerService.Insert(seller);
  /*após isso, vamos redirecionar nossa página, chamando a action Index (do meu controller)*/
  Return RedirectToAction(“Index”);
  OU
  Return RedirectToAction(nameof(Index));
}

```

Agora basta indicar que o método da ação é POST por meio notação **[HttpPost]** em cima da declaração do método.


```cs

[HttpPost]
public IActionResult Create(Seller seller){
  /*vamos precisar importar os models*/
  /*basta chamar o método de inserção de dados do serviço que criamos*/
  _sellerService.Insert(seller);
  /*após isso, vamos redirecionar nossa página, chamando a action Index (do meu controller)*/
  Return RedirectToAction(“Index”);
  OU
  Return RedirectToAction(nameof(Index));
}

```

Bora testar, note que o departamento não está sendo informado, vamos resolver isso mais tarde. 
Outra hora falar sobre a notação [ValidateAntiForgeryToken]

Referencia: https://docs.microsoft.com/en-us/aspnet/core/security/anti-request-forgery

