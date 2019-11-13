# Ciando a tela de detalhes do vendedor

Vamos carregar [eager loading](https://docs.microsoft.com/en-us/ef/core/querying/related-data) para carregar objetos relacionados.

Cada vendedor, listado pela view View/Sellers/Index, vai ter um link para a ação "Details", que ainda não existe, sendo assim...

No contolador de vendedor SellersController, vamos criar a ação GET "Details".

```cs

        /*Note que o parametro id é opcional. Para indicar isso, usamos o ponto de interrogação após o int*/
        public IActionResult Details(int? id)
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
            return View(obj);
        }

```

Agora vamos criar a view "Details" - View/Sellers/Details

Incluir no método FindAll: Include(obj => obj.Department) (namespace: Microsoft.EntityFrameworkCore)
