# Fazendo o update dos dados do vendedor

## Criando serviço de exceções personalizadas (criando as classes necessárias)

No mundo real, vamos desejar ter erros específicos da nossa camada de serviço e não da aplicação.
Além disso, posso tratar as exeções de maneira especifíca para o contexto do projeto, conforme as regras de negócio.
Primeiramente vamos criar a pasta **Exceptions** dentro da pasta **Services**.
 > Botão direito em cima da pasta Services -> add new folder -> Exceptions
 
Dentro da pasta **Exceptions**, vamos criar duas classes exceções personalizadas, uma para cada exceção personalizada.
Sendo assim, adicione a classe _DbConcurrencyException_ e _NotFoundException_.
Essas classes vão extender _ApplicationException_ e devem ter um método construtor  que receba uma string como 
parâmetro de entrada.

**DbConcurrencyException.cs**

```cs

    public class DbConcurrencyException : ApplicationException
    {
        //Visto que essa classe extende/herda de ApplicationException, 
        //devemos criar o método construtor compatível
        //Método construtor que recebe como parâmetro uma mensagem
        public DbConcurrencyException(string message) : base(message) {

        }
    }
    
```


 **NotFoundException.cs**

```cs

    //Essa classe extende/herda de ApplicationException
    public class NotFoundException : ApplicationException
    {
        //Visto que essa classe extende/herda de ApplicationException, 
        //devemos criar o método construtor compatível
        //Método construtor que recebe como parâmetro uma mensagem
        public NotFoundException(string message) : base(message)
        {

        }
    }
    
```

## Implementando o método update para os dados de vendedor

Em **ServiceSeller.cs**, vamos criar o método _update_ para atualizar os dados de um vendedor.

```cs

        public void Update(Seller obj) {
            //Se não houver nenhum vendedor com esse ID no banco de dados
            if (!_context.Seller.Any(s => s.Id == obj.Id)){
                throw new NotFoundException("Id not found");
            }
            //Se houver um vendedor no banco de dados com esse ID, vou atualizar seus dados
            //com base no objeto Seller que recebi como parâmetro do método
            _context.Update(obj);
            /*Porém, sempre que executarmos uma operação de update no banco de dados, pode ocorrer um erro de concorrência*/
            _context.SaveChanges();

        }

```


Visto que pode ocorrer o erro "DbUpdateConcurrencyException" do EntityFramework por causa da concorrência do banco de dados ao executarmos a operação de update, vamos tentar capturar esse erro para poder relançar ele como uma exceção da camada de serviço.


```cs

          public void Update(Seller obj) {
             //Se não houver nenhum vendedor com esse ID no banco de dados
             if (!_context.Seller.Any(s => s.Id == obj.Id)){
                 throw new NotFoundException("Id not found");
             }
             /*Porém, sempre que executarmos uma operação de update no banco de dados, 
              * pode ocorrer um erro de concorrência "DbUpdateConcurrencyException"*/
             try
             {
                 _context.Update(obj);
                 _context.SaveChanges();
             }
             /*Esse erro é do EntityFramework, ou seja, da camada de acesso aos dados*/
             catch (DbUpdateConcurrencyException e) {
                 /*então vamos interceptar esse erro e tratá - lo na camada mais adequada, 
                  * relançando a exceção na camada de serviços, não propagando o erro, assim, 
                  o SellersController vai precisar tratar apenas exceções da camada de serviço*/
                 throw new DbConcurrencyException(e.Message);
             }
            }

```


# Ciando a tela de Editar do vendedor


Cada vendedor, listado pela view View/Sellers/Index, vai ter um link para a ação "Edit", que ainda não existe, sendo assim...

No contolador de vendedor SellersController, vamos criar a ação GET "Edit".

```cs

     public IActionResult Edit(int? id)
        {
            /*Primeiro vou testar se o id está nulo*/
            if (id == null)
            {
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
            if (obj == null)
            {
                return NotFound();
            }
            //Abre a tela de edição

            List<Department> departments = _departmentService.FindAll();
            SellerFormViewModel viewModel = new SellerFormViewModel{ Seller = obj, Departments = departments }

            return View(viewModel);
        }

```

## Criando a View Edit

```html

@model Wallmart.Models.ViewModels.SellerFormViewModel

@{
    ViewData["Title"] = "Updating Sellers";
}

<h2>@ViewData["Title"]</h2>
<hr />
<div class="row">
    <div class="col-md-4">
        <form asp-action="Edit">
          <input type="hidden" asp-for="Seller.Id" />
            <div class="form-group">
                <label asp-for="Seller.Name" class="control-label"></label>
                <input asp-for="Seller.Name" class="form-control" />
            </div>
            <div class="form-group">
                <label asp-for="Seller.Email" class="control-label"></label>
                <input asp-for="Seller.Email" class="form-control" />
            </div>
            <div class="form-group">
                <label asp-for="Seller.BirthDate" class="control-label"></label>
                <input asp-for="Seller.BirthDate" class="form-control" />
            </div>
            <div class="form-group">
                <label asp-for="Seller.Salary" class="control-label"></label>
                <input asp-for="Seller.Salary" class="form-control" />
            </div>

            <div class="form-group">
                <label asp-for="Seller.DepartmentId" class="control-label"></label>
                <select asp-for="Seller.DepartmentId" 
                    asp-items="@(new SelectList(Model.Departments, "Id", "Name"))"
                     class="form-control"></select>
                <!--Lista de Objetos, Propriedade chave, Valor que vai aparecer na lista--> 
            </div>

            <div class="form-group">
                <input type="submit" value="Create" class="btn btn-default" />
            </div>
        </form>
    </div>

</div>
```

Agora vamos criar a action _Edit_ com o método post no SellersController

```html

        [HttpPost]
        public IActionResult Edit(int? id, SellerFormViewModel obj)
        {
            if (obj.Seller.Id != id) {
                return BadRequest();
            }
            try
            {
                _sellerService.Update(obj.Seller);
                return RedirectToAction(nameof(Index));
            }
            catch (NotFoundException) {
                return NotFound();
            }
            catch (DbConcurrencyException) {
                return BadRequest();
            }
        }

```
