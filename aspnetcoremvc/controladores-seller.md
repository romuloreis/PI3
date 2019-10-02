# Adicionando Contoller de Seller e sua Index View

Vamos começar a fazer o controlador de vendedores, mas antes…

## Adicionando links de acesso no menu da Home

Vamos criar os links para _Departments_ e _Sellers_ no menu de navegação.

No arquivo __Layout.cshtml_ vamos usar nossa tag helper _asp-controller_ para adicionar os links, 
que são chamadas à uma action do controller:

 > O asp-area=”” serve para ter uma outra divisão lógica na nossa aplicação. Não vamos usar isso por enquanto, 
 > inclusive podemos deletar esse atributo.


```html
    <ul class="nav navbar-nav">
        <li><a asp-controller="Home" asp-action="Index">Home</a></li>
        <li><a asp-controller="Departments" asp-action="Index">Departments</a></li>
        <li><a asp-controller="Sellers" asp-action="Index">Sellers</a></li>
        <li><a asp-controller="Home" asp-action="About">About</a></li>
        <li><a asp-controller="Home" asp-action="Contact">Contact</a></li>
    </ul>
```

## Criando Controlador Seller

Vamos criar nosso controlador de Sellers (_SellersController_), para isso clique com o botão direito do mouse na pasta **Controllers**
e acesse o menu Add -> Controller -> MVC Controller (Empty)

No arquivo _SellersController_ note que já foi criado o método Index()

```cs
    public class SellersController : Controller
    {
        public IActionResult Index()
        {
            return View();
        }
    }
```

## Criando View Seller
Agora vamos criar a pasta **Sellers** dentro da pasta **Views**

Dentro da pasta **Views/Sellers/** vamos criar a View _Index_ com template vazio 
(_Views/Sellers -> Add -> View_)

Altere o título para Sellers

```cs
@{
    ViewData["Title"] = "Sellers";
}

<h2>Index</h2>
```


## SellerService (Serviço de acesso aos dados)

**Contextualizando - Camada Serviços**
Ao usar scaffold, o código gerado automaticamente atribui ao Controller
a responsabilidade de implementar regras de negócio e acessar os dados pelo EntityFramework.
Note em _DepartmentController_ que a classe tem uma referência ao CONTEXT do banco e faz as 
chamadas ao EntityFramework diretamente. E as regras de negócio são definidas e aplicadas nele, 
porém essa não é uma prática recomendável.

O Controlador deve ser algo enxuto, que apenas recebe requisições e coordena às ações que devem 
ser tomada a partir dessa requisição.

O ideal é ter uma camada extra responsável por aplicar às regras de negócio e acessar o Context.
Nesse projeto vamos implementar uma camada SERVIÇO.

Não vamos implementar os repositories (objetos que acessam os dados), pois nosso objeto context do EntityFramework
já implementa repositories.

Então vamos implementar serviços...
A classe _NomeClasseService_ vai ser responsável por realizar operações relacionada a classe _NomeClasse_ e 
também será responsável por implementar as regras de negócio, como restrições para salvar dados no banco.
Pois, não é no controller que às regras de negócio devem ser implementadas.

**Mãos a obra**

Primeiramente vamos criar a pasta **Services** na raíz do projeto.
 > Botão direito em cima do projeto -> add new folder -> Services
 
Dentro da pasta **Services**, adicione a classe _SellerService_, a qual
será responsável por acessar e persistir os dados/registros relacionados 
à classe _Seller_.

No arquivo _Startup.cs_, vamos registrar esse serviço na injeção de dependência do sistema.
Assim, minhas instâncias de classes poderão acessar esse serviço.
Para isso basta adicionar o trecho de código abaixo ao final do método _ConfigureServices_:

```cs
services.AddScoped<SellerService>();
```

## Criando injeção de dependência com o Banco

Em _SellerService_, vamos criar uma injeção de dependência com o contexto do banco, adicionando o seguinte trecho de código:

```cs
public class SellerService
    {
        private readonly SuperApplicationContext _context;

        public SellerService(SuperApplicationContext context)
        {
            _context = context;
        }
    }
```

## Implementando Método FindAll()

Em _SellerService_, vamos implementar método _FindAll()_, que retorna uma List <Seller> (lista de vendedores)

```cs
public List<Seller> FindAll()
{
  //Trás os objetos/dados da tabela vendedores e transforma em uma lista
  //Por enquanto vamos deixar esse método como uma operação SINCRONA, ou seja,
  //quando essa operação for executada a aplicação vai ficar “parada” até essa operação 
  //ser concluída.
  return _context.Seller.ToList();
}
```

## Chamando Método FindAll()

Em _SellersController_, vamos implementar o método _Index()_, o qual deve chamar o método _SellerService.FindAll()_

Mas primeiramente devemos criar a injeção de dependência com **serviço** que acessa os dados de vendedores no banco.

```cs
public class SellersController : Controller
    {
        //Injeção de dependência
        private readonly SellerService _sellerService;

        //construtor para finalizar a injeção de dependência
        public SellersController(SellerService sellerService)
        {
            _sellerService = sellerService;
        }
        //Método para acessar dados (indiretamente, pois os dados são acessados pelo serviço)
        public IActionResult Index()
        {
            //retorna uma lista
            var list = _sellerService.FindAll();
            //Encaminha os dados para a view
            return View(list);
        }
    }
```

## Cirando View Index de Sellers

Agora vamos criar um template de View para listar os vendedores na tela.
Em **Views/Sellers/Index.cshtml**, vamos criar o código para manipular os dados.
Primeiramente, criar o objeto @model, que corresponde ao objeto que passamos como argumento do controller para a View. 
Nesse caso, nosso @model recebe uma lista de objetos que passamos por “return View(list)” no controller.
Declare o tipo de objeto que o objeto @model vai receber do controller, neste caso vamos usar o tipo IEnumerable, 
pois é bem genérico e recebe lista, conjuntos, etc.

```html
@model IEnumerable<SuperApplication.Models.Seller>

@{
    ViewData["Title"] = "Sellers";
}

<h2>@ViewData["Title"]</h2>
```

Agora vamos implementar uma tabela para listar os dados (vendedores) recebidos do controlador

```html
@model IEnumerable<SuperApplication.Models.Seller>

@{
    ViewData["Title"] = "Sellers";
}

<h2>@ViewData["Title"]</h2>

<p>
    <a asp-action="Create" class="btn btn-default">Create New</a>
</p>

<!-- classes do bootstrap (table table-striped table-hover)-->
<table class="table table-striped table-hover">
    <thead>
        <tr class="success">
            <th>
                <!-- recurso do framework(mostrar nome do atributo do objeto "Name" do objeto da lista)
                apenas o nome do atributo, não o valor. Até pq AT model é uma lista-->
                @Html.DisplayNameFor(model => model.Name)
            </th>
            <th>
                @Html.DisplayNameFor(model => model.Email)
            </th>
            <th>
                @Html.DisplayNameFor(model => model.BirthDate)
            </th>
            <th>
                @Html.DisplayNameFor(model => model.BaseSalary)
            </th>
            <th></th>
        </tr>
    </thead>
    <!-- para cada item da lista -->
    <tbody>
        @foreach (var item in Model)
        {
            <tr>
                <td>
                    <!-- DisplayFor mostra o conteúdo da propriedade acessada.
                    Usa-se um apelido qualquer "modelItem", o que conta é  "item.Name"-->
                    @Html.DisplayFor(modelItem => item.Name)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Email)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.BirthDate)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.BaseSalary)
                </td>
                <td>
                    <a asp-action="Edit" asp-route-id="@item.Id">Edit</a>
                    <a asp-action="Details" asp-route-id="@item.Id">Details</a>
                    <a asp-action="Delete" asp-route-id="@item.Id">Delete</a>
                </td>
            </tr>

        }
    </tbody>
</table>

```

