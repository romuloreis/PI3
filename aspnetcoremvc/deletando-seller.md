# Deletando Vendedores

## Criando métodos necessários para deletar um vendedor 
no serviço SellerService.

Em _SerlerService_ vamos criar os métodos _FindById_ e _Remove_

```cs
public Seller FindById(int id){
  return _context.Seller.FirstOrDefault(obj => obj.Id == id)
}

public void Remove(int id){
  /*Crio uma variável para apontar para o objeto que vou remover*/
  var obj = _context.Seller.Find(id);
  /*Remove o objeto do DB Set*/
  _context.Seller.Remove(obj);
  /*Confirmar a remoção ao EntityFramework para persistir as mudanças*/
  _context.SaveChanges();
}
```

## Criando Action Delete - GET

No controlador de vendedores (_SellersController_) vamos criar 
a ação _Delete_ como GET.
Esse método como GET vai servir apenas para chamar uma tela de confirmação 
da exclusão. Essa ação ainda não vai deletar o vendedor, até porque nem tenho 
como passar qual vendedor quero deletar usando o método GET.

```cs
/*Note que o parametro id é opçãonal. 
Para indicar isso, usamos o ponto de interrogação após o int*/
public IActionResult Delete(int? id){
  /*Primeiro vou testar se o id está nulo*/
  if(id == null){
    /*Se estiver nulo, é pq foi efetuada uma requisição de forma indevida,
    nesse caso, iremos retornar uma página de erro padrão.
    Posteriormente vamos personalizar essa página de erro.*/
    return NotFound();
  }
  
  /*Se o id não estiver nulo, vou buscar meu registro no banco.
  Note que usamos id.value, pois se trata de uma variável que pode
  ter um valor nulo (nulable)*/
  var obj = _sellerService.FindById(id.Value);
  
  /*Verificar se o id é válido e retornou algum registro.
  Se nenhum vendedor for localizado com esse id, será retornado 
  o valor null do método FindById(int id)*/
  if(obj == null){
      return NotFound();
  }
  return View(obj);
}

```

Links sobre nullable-types
  - [link1](http://www.macoratti.net/11/03/c_ntp.htm)
  - [link2](https://docs.microsoft.com/pt-br/dotnet/csharp/programming-guide/nullable-types/)
  - [link3](https://docs.microsoft.com/pt-br/dotnet/csharp/programming-guide/nullable-types/using-nullable-types)
  - [link4](https://docs.microsoft.com/pt-br/dotnet/api/system.nullable-1?view=netframework-4.8)
  
## Views relacionadas à action Delete


Em View/Sellers/Index, verifique se o link para a ação _Delete_ está correta.
Nós usamos o _asp-action="Delete"_ para chamar a ação _Delete_ e a rota
_asp-route-id="@item.Id"_ indica que é passado o id do vendedor que eu clicar.

```cs
<a asp-action="Delete" asp-route-id="@item.Id">Delete</a>
```


Agora vamos criar a view de confirmação da exclusão **View/Sellers/Delete**. 
Lembrando que o nome da View deve ser o mesmo nome da Action.
Vamos utilizar um template vazio para essa View.

  > Não selecione a opção "Create as a partial view"


No arquivo da View _Delete_ em **View/Sellers/**, vamos definir o tipo do model
que ela vai receber.

```html
@SuperProject.Models.Seller

@{
  ViewData["Title"] = "Delete";
}
<h2>@ViewData["Title"]</h2>

<h3>Are you sure you want to delete this? vai deleta mesmo?</h3>

<div>
  <h4>Seller</h4>
  <hr />
  <dl class="dl-horizontal">
    <dt>
      @Html.DisplayNameFor(model => model.Name)
    </dt>
    <dd>
       @Html.DisplayFor(model => model.Name)
    </dd>
    <dt>
      @Html.DisplayNameFor(model => model.Email)
    </dt>
    <dd>
       @Html.DisplayFor(model => model.Email)
    </dd>
     <dt>
      @Html.DisplayNameFor(model => model.BirthDate)
    </dt>
    <dd>
       @Html.DisplayFor(model => model.BirthDate)
    </dd>
     <dt>
      @Html.DisplayNameFor(model => model.BaseSalary)
    </dt>
    <dd>
       @Html.DisplayFor(model => model.BaseSalary)
    </dd>    
  </dl>
</div>

<!--Ao clicar no botão, vai chamar a action Delete-->
<form asp-action="Delete">
  <!--Vanmos passar o id do vendedor que deve ser deletado por meio
do campo oculto abaixo->
  <input type="hidden" asp-for="Id" />
  <input type="submit" value="Delete" class="btn btn-danger"/>
  <a asp-action="Index">Back to List</a>
</form>
```

Hora de testar!

Note que ao clicar no botão para confirmar a exclusão, nada vai ocorrer,
pois ainda não implementamos a action _Delete_ com método POST.

Sendo assim, no controlador de vendedores, 
vamos criar a action _Delete_ com método POST.


```cs
[HttpPost]
public IActionResult Delete(int id){
  _sellerService.Remove(id);
  return RedirectToAction(nameof(Index));
}
```

Hora de testar novamente!
