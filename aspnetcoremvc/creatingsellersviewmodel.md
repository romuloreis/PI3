# Criando o campo Select e usando ViewModel

Um ViewModel é um MODEL composto que você pode colocar o que quiser (fora do padrão MVC, mas de maneira complementar)
Útil para quando são necessários mais dados do que os dados de apenas uma ENTIDADE
Nesse caso, teremos dados da entidade Department na View de Sellers (campo para selecionar departamento)

## Criando Serviço DepartmentService

Primeiramente, Criar _DepartmentService.cs_ com método _FindAll()_ na pasta _Services_
_DepartmentService.cs_ vai ter às mesmas dependências de _SerllerServices_ que é o contexto do EntityFramework

```cs 

private readonly SalesWebMvcContext _context;

        public DepartamentService(SalesWebMvcContext context)
        {
            _context = context;
        }

public List<Departament> FindAll()
        {
            return _context.Department.OrderBy(x => x.Name).ToList();
        }
        
```

Em _Startup.cs_ vamos registrar esse serviço no sistema de injeção de dependência...

No método _ConfigureServices()_ vamos adicionar:

```cs

services.AddScoped<DepartmentService>();

```

Vamos criar _SellerFormViewModel_, dentro de _Models/ViewModels_

Essa classe que vai conter os dados para usarmos no cadastro de vendedores

Vamos precisar de um objeto Seller e uma lista de Departments, sendo assim, teremos às propriedades:

```cs
Prop Seller Seller…
Prop ICollection<Department> Departments {get; set;}
```
## Usando Serviço DepartmentService

No Controlador _SellersController_ vamos criar uma dependência à _DepartmentService_

```cs
//Injeção de dependência
private readonly SellerService _sellerService;
private readonly DepartmentService _departmentService;

//construtor para finalizar a injeção de dependência
public SellersController(SellerService sellerService, DepartmentService departmentService) {
      _sellerService = sellerService;
      _departmentService = departmentService;
}
        
```

Atualizar o método _Create()_ GET

```cs

//Método para acessar dados (indiretamente, pois os dados são acessados pelo serviço)
 public IActionResult Create(){
	  //Carrega Departamentos
    var departaments =  _departmentService.FindAll();
    //Instância nosso ViewModel, que vai ter duas propriedades, a primeira é a lista de departamentos, que já temos.
    var viewModel = new SellerFormViewModel{ Departments = departments }
	  //Encaminha os dados para a view
    //Agora na tela de cadastro, já vou poder acessar a lista de departamentos 
    return View(viewModel);
 }
 
 ```
Atualizar minha **view Create** de _/Views/Sellers/_ para usar o *model* _SellerFormViewModel_

O **@model** da view que vai receber não vai mais ser do tipo Seller, 
devemos atualizar a classe do model para xxx.ViewModels.SellerFormViewModel

Atualizar campos do formulário:

Exemplo:

```html

<label asp-for="Seller.Name” class="control-label"

Adicionar o componente de seleção para DepartamentoId, que terá um bind (ligação) com o atributo DepartmentId

<div class="form-group">
<label asp-for="Seller.DepartmentId" class="control-label"></label>
<select asp-for="Seller.DepartmentId" asp-items="@(new SelectList(Model.Departments,"Id", "Name"))" class="form-control"></select>
</div>

```

Referência: https://stackoverflow.com/questions/34624034/select-tag-helper-in-asp-net-core-mvc

Asp-items constroí os itens da caixa de seleção com base em uma coleção.

Há 3 argumentos.

Model.Departments (Model)

Qual o atributo deste Model vai ser a chave

Qual o atributo deste Model vai aparecer nesta caixa de seleção

O método _Create()_ **POST* do _SellersController_ não vamos precisar atualizar

Pois o próprio framework identifica que o *ViewModel* tem os dados necessários para criar um objeto Seller,
Isso ocorre por causa da propriedade **DepartmentId** em _Sellers_, então ele sabe que vai ter que pegar esse
Dado para usar na instanciação do _Seller_ e gravar no BD

Deletar o método _First()_ do método _Insert()_ da classe _SellerService_ 


