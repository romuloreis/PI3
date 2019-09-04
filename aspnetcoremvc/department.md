# Super Application - Criando department

## Nota sobre rotas

As rotas são definidas no método _Configure_ da classe _Startup_. Ao criar o projeto, é criado uma aplicação de exemplo. 
Por isso temos a pasta _Home_ dentro da pasta _Views_, essa pasta é controlada pelo controlador base HomeController que 
controla as ações da minha aplicação básica.

Note que no aquivo _HomeController_ cada método corresponde à uma ação (action), que eu posso passar por parâmetro 
por meio da URL no navegador. Note que o parâmetro **Id** da rota PODE ser opcional. Na rota padrão, observe que o 
Id está acompanhado de um ponto de interrogação, indicando que o mesmo é opcional.

 > Padrão das rotas:
 > http://[servidor]:[porta]/NomeController/Ação/Id
 > http://[servidor]:[porta]/NomeController/Ação/
 > https://localhost:44396/Home/About
 
  ```C#
 //Arquivo Startup.cs
     app.UseMvc(routes =>
     {
         routes.MapRoute(
             name: "default",
             template: "{controller=Home}/{action=Index}/{id?}");
     });
 
 ```
 
 ## Controller
  Todos os controladores herdam da classe _Controller_, conforme pode ser observado no arquivo **HomeController.cs**. 
  Todo o framework é fortemente baseado em padões de nomeclatura, sendo esse o principal motivo de usarmos a língua inglesa no projeto.
  É importante reforçar que o nome do controlador deve ser seguido da palavra "Controller" e que o nome dos seus métodos são mapeados 
  para ações do mesmo nome.
  
  A classe _HomeController_ atualmente possue o conjunto de métodos Index(), About(), Contact(), Privacy() e Error(), sendo que todos 
esses métodos retornam um objeto do tipo _IActionResult_.
 Então, ao acessar a url https://localhost:44396/Home/About, estamos acessando o controlador _Home_ ação _About_. O método _About_  
 pode acessar um objeto _ViewData_ (dicionário chave-valor) que possue chaves com valores. Nesse caso, o objeto _ViewData_ está 
 recebendo um valor na chave "message". Após isso ele retorna _View()_, que é um método Builder, o qual retorna um objeto do tipo 
 _IActionResult_ (já vamos falar desse cara), nesse caso, uma _View_.
 
 Sendo assim, o Framework vai procurar na pasta _Views/Home_ uma view/página com nome _About.cshtml_ para processar e gerar o arquivo 
 html que será retornado ao usuário. Esse processamento é feito pelo sistema de template Razor. Ao abrir o aquivo  _About.cshtml_ 
 é possível verificar que essa página está acessando o objeto ViewData, para resgatar os valores encaminhados pelo Controller.

Note que é possível utilizar código C# dentro da marccação de bloco @{}

## IActionResult

É uma interface, todo resultado de uma ação deve retornar esse tipo (por questões de boas práticas), que tem vários subtipos

#Notas Professor
 - Incluir tabela IActionResult (Nome da Classe - nome do método Builder a ser retornado no Controller)
 - Incluir explicação Natural Templates (Diferente do JSP)
 - Incluir inclusão de novas chaves valores no controlador e acessar na página web.
 
 
 ## Organizando Views - Criando ViewModels
 
 **Primeiramente** 
 
 Adicione a pasta **ViewModels** dentro da pasta **Models** e arraste o arquivo _ErrorViewModel.cs_ para essa pasta e altere o nome da namespace desse model para NomeProjeto.Models.ViewModels para organizar também os namespaces. CTRL_SHIFT+B para compilar. Corrija os erros. Provavelmente seja apenas ajustar o namespace nos outros aquivos que referênciam o modelo _ErrorViewModel.cs_.
 
 **fig-primeirocontroller01**
 
 ```C# 
namespace SuperApplication.Models.ViewModels
{
    public class ErrorViewModel
    {
        public string RequestId { get; set; }

        public bool ShowRequestId => !string.IsNullOrEmpty(RequestId);
    }
}
``` 
 
## Criando Model Department

Na pasta **Models** Adicione uma nova classe chamada _Department_ e adicione as propriedades Id e Name.
 
 ```C# 
 namespace SuperApplication.Models
{
    public class Department
    {
        public int Id { get; set; }
        public string Name { get; set; }
    }
}
 ```
 
 ## CRUD Scaffolding
 
 - Botão direito do mouse na pasta controllers... (Controllers -> Add -> New Scaffolded Item)
   - Selecionar opção: MVC controllers with views, using Entity Framework
 - Model class: Department
 - Clique no símbolo + para criar um Data context class (DbContext) (aceita sugestão de nome)
 - Em Views, seleciona às três opções
 - Nome do controlador: DepartamentsController
 
  **Conforme ilustrado nas imagens:**
 
 ![First_img](/aspnetcoremvc/assets/img/scaf01.png)
 
  ![First_img](/aspnetcoremvc/assets/img/scaf02.png)
  
  ![First_img](/aspnetcoremvc/assets/img/scaf03.png)

 
 ## Migration
 
Tools -> packte manager -> nuget console

_Add-Migration Initial_

 > Note que a palavra _initial_ pode ser um nome qualquer
 
Vai criar uma pasta “migrations” com o primeiro script de migration com ano-mes-dia como nome de arquivo
PARA manter a ordem cronológica das alterações -> esse arquivo herda de migration.

Vamos executar a migration agora

_Update-Database_

output mostra o script usado para criar o banco

Acessa /Departaments e teste.

## Atualizando Bootstrap

 - bootswtch.com/3
 - www/lib/bootstrap/dist/css
 - Salva como bootstrap-nometema.css
 - Copia o arquivo e cola pelo visual studio
 - shared/_Layout -> atualiza ref do bootstrap
	- Environment development




 
