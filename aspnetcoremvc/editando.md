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


# Ciando a tela de Editar do vendedor

Vamos carregar [eager loading](https://docs.microsoft.com/en-us/ef/core/querying/related-data) para carregar objetos relacionados.

Cada vendedor, listado pela view View/Sellers/Index, vai ter um link para a ação "Details", que ainda não existe, sendo assim...

No contolador de vendedor SellersController, vamos criar a ação GET "Details".

```cs



```

# Serviço 

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
