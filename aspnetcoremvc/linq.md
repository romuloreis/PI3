## Views/Sellers/Index.cshtml

```html

@model IEnumerable<ProjectName.Models.Seller>

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
               apenas o nome do atributo, não o valor. Até pq @model é uma lista-->
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


# Deleting examples syntaxe

https://docs.microsoft.com/pt-br/ef/core/saving/cascade-delete

```cs
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>()
            .HasOne(p => p.Blog)
            .WithMany(b => b.Posts)
            .OnDelete(DeleteBehavior.Cascade);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; }
    public Blog Blog { get; set; }
}
```

# Many-to-Many example syntaxe

```cs
class MyContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Tag> Tags { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<PostTag>()
            .HasKey(pt => new { pt.PostId, pt.TagId });

        modelBuilder.Entity<PostTag>()
            .HasOne(pt => pt.Post)
            .WithMany(p => p.PostTags)
            .HasForeignKey(pt => pt.PostId);

        modelBuilder.Entity<PostTag>()
            .HasOne(pt => pt.Tag)
            .WithMany(t => t.PostTags)
            .HasForeignKey(pt => pt.TagId);
    }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public List<PostTag> PostTags { get; set; }
}

public class Tag
{
    public string TagId { get; set; }

    public List<PostTag> PostTags { get; set; }
}

public class PostTag
{
    public int PostId { get; set; }
    public Post Post { get; set; }

    public string TagId { get; set; }
    public Tag Tag { get; set; }
}
```

# Consultas

https://docs.microsoft.com/pt-br/ef/core/querying/

# LINQ	E	LAMBDA

**Usar uma classe mais complexa tipo Product e Category**

```cs
    //	inicializa	a	lista
            List<Conta> contas = new List<Conta>();
            //	inserimos	algumas	contas lista.Add(...);	
            Conta c1 = new Conta(1, "Ana", 2000);
            contas.Add(c1);
            Conta c2 = new Conta(2, "Paula", 1200);
            contas.Add(c2);
            Conta c3 = new Conta(3, "Ricardo", 2500);
            contas.Add(c3);
            Conta c4 = new Conta(4, "Bruno", 3000);
            contas.Add(c4);

            //	cria	lista	que	usaremos	para	guardar	os	elementos	filtrados 
            var filtrados	=	new	List<Conta>();


            foreach (Conta c in contas) {
                if (c.Saldo	>	2000)		{
                    filtrados.Add(c);
                }
            }
            //	agora	a	variavel	"filtrados"	tem	as	contas	que	queremos!
            Console.WriteLine("Filtrados");
            foreach (Conta c in filtrados)
            {
                Console.WriteLine("Nome: "+c.Name);
            }

```

## FILTROS	UTILIZANDO	O	LINQ (com Lambda)

Para	filtrar	uma	lista,	seria	muito	mais	interessante	que	a	própria	coleção	tivesse	algum	método	que recebesse	a	condição	que	queremos	aplicar	nesse	filtro	e	já	implementasse	a	lógica	do		foreach	,	algo como:
```cs
List<Conta>	contas	=	//	inicializa	a	lista
var	filtradas	=	contas.Filtra(condição); 
```

### Slides (52)

Mas	como	passar	a	condição	para	esse	filtro?	Teríamos	que	enviar	um	bloco	de	código	que	aceita	ou rejeita	os	valores	da	coleção.	Para	passar	um	bloco	de	código	que	pode	ser	utilizado	por	um	método,	o C#	introduziu	as	funções	anônimas	ou	lambdas.	As	funções	anônimas	funcionam	como	métodos estáticos	da	linguagem	com	uma	declaração	simplificada.	Para	declarar	uma	função	anônima	que	recebe um	argumento	do	tipo		Conta		utilizamos	o	seguinte	código:

```cs
            //	inicializa	a	lista
            List<Conta> contas = new List<Conta>();
            //	inserimos	algumas	contas lista.Add(...);	
            Conta c1 = new Conta(1, "Ana", 2000);
            contas.Add(c1);
            Conta c2 = new Conta(2, "Paula", 1200);
            contas.Add(c2);
            Conta c3 = new Conta(3, "Ricardo", 2500);
            contas.Add(c3);
            Conta c4 = new Conta(4, "Bruno", 3000);
            contas.Add(c4);

            //	cria	lista	que	usaremos	para	guardar	os	elementos	filtrados 

            var filtradas = contas.Where((Conta c) => { return c.Saldo > 2000; });

            //	agora	a	variavel	"filtrados"	tem	as	contas	que	queremos!
            Console.WriteLine("Filtrados");
            foreach (Conta c in filtradas)
            {
                Console.WriteLine("Nome: "+c.Name);
            }

```

## 	SIMPLIFICANDO	A	DECLARAÇÃO	DO	LAMBDA

**Tipo** do retorno é redundante 

**Chaves e Return** desnecessário para uma linha de código

```cs

Conta filtradas = contas.Where(c => c.Saldo	> 2000);

```

**outro exemplo:**
```cs

Conta filtradas = contas.Where(c => c.Saldo % 2 == 0);

```


## 	OUTROS	MÉTODOS	DO	LINQ 

Somar o saldo de todas as contas 
```cs
    List<Conta>	contas	=	//	inicializa	a	lista	de	contas 
    double	total = 0.0;
    foreach(Conta c in contas) {				
        total += c.Saldo;
    } 
 ```
 
 Quando	queremos	fazer	a	soma	dos elementos	de	uma	lista,	podemos	utilizar	o	método	Sum	do	LINQ,	passando	um	lambda	que	fala	qual	é a	propriedade	da	conta	que	queremos	somar:

```cs
double total = contas.Sum(c	=>c.Saldo);
```

Com	essa	linha	de	código	conseguimos	o	mesmo	efeito	do		foreach		anterior.	Além	do		Sum	, também	podemos	utilizar	o	método	Average	para	calcular	a	média	dos	valores,	Count	para	contar	o número	de	valores	que	obedecem	algum	critério,	Min	para	calcular	o	menor	valor	e	Max	para	calcular	o maior	valor:

```cs
List<Conta> contas=	//	inicializa	a	lista
    
//	soma	dos	saldos	de	todas	as	contas
double saldoTotal = contas.Sum(c => c.Saldo);
//	media	do	saldo	das	contas 
double mediaDosSaldos = contas.Average(c => c.Saldo);
//	número	de	contas	que	possuem	Numero	menor	do	que	1000 
int	numero = contas.Count(c => c.Numero < 1000);
int	menorNumero = contas.Min(c=>c.Numero);
double maiorSaldo = contas.Max(c => c.Saldo); 
```

Quando	utilizamos	esses	métodos	de	agregação	em	uma	lista	com	tipos	primitivos,	o	lambda	é	um argumento	opcional.	Por	exemplo,	se	tivéssemos	uma	lista	de		double	,	poderíamos	utilizar	o	seguinte código	para	calcular	a	média	dos	números:

```cs
List<double>	saldos	=	//	inicializa	a	lista
double media = saldos.Average();
```

## PROJETO CATEGORIA E PRODUTO

```cs
            //apenas o nome e não todo o objeto... o Select permite fazer projeções
            var r2 = products.Where(p => p.Category.Name == "Tools").Select(p => p.Name);
            Print("NAMES OF PRODUCTS FROM TOOLS", r2);
            
            //objeto anonimo, cria um objeto de uma classe não existente no projeto
            //Note que categoryName é um alias-apelino para p.category.name, pois daria contradição com o p.name
            var r3 = products.Where(p => p.Name[0] == 'C').Select(p => new { p.Name, p.Price, CategoryName = p.Category.Name });
            Print("NAMES STARTED WITH 'C' AND ANONYMOUS OBJECT", r3);
            
            //como vai dar vazio, pois não há price maior que 3000... 
             var r7 = products.Where(p => p.Price > 3000.0).FirstOrDefault();
             
             //tipo de retorno Product
             var r8 = products.Where(p => p.Id == 3).SingleOrDefault();
             //tipo de retorno IEnumerable
             var r8 = products.Where(p => p.Id == 3);

```

## UTILIZANDO	O	LINQ	COM	OUTROS	TIPOS
Podemos	utilizar	o	LINQ	com	qualquer	objeto	que	implemente	a	interface		IEnumerable

**outro exemplo:**
```cs
//Aplica uma função a cada resultado da função primária
Conta filtradas=contas.Where(c=>c.Saldo % 2 == 0).Select(z => z*10);
```

## OUTROS FILTROS (OrderBy, ThenBy)																				
.ThenBy()

```cs
var	resultado = contas
.Where(c => c.Saldo > 10000)																				
.OrderBy(c => c.Titular.Nome)																				
.ThenBy(c => c.Numero);
```

## MELHORANDO	AS	BUSCAS	UTILIZANDO	A	SINTAXE	DE	QUERIES 

```cs
var	filtradas = from c in contas
where c.Numero < 2000																
select c;
```

## 	PROJEÇÕES	E	OBJETOS	ANÔNIMOS

```cs
var	resultado = from c in contas
where <condição da busca>
select new { c.Numero, c.Titular }; 
```

## 	ORDENANDO	COLEÇÕES	COM	LINQ

```cs
List<Conta>	contas =//inicializa a lista de contas 
    var	resultado = from c in contas
    where c.Saldo	> 10000																
    orderby	c.Titular.Nome	descending,	c.Numero descending																
    select c;
```

## Convertendo

```cs
    IEnumerable<Conta> contas = // inicializa a lista de contas 
    List<Conta>	resultado = from c in contas
    where c.Saldo	> 10000																
    orderby c.Titular.Nome descending,	c.Numero descending																
    select c;
```

## Seleções mais complexas

```cs
//Tendo as classes Product e Category
var result1 = products.Where(x => x.Category.MyType == 1 && x.Price > 900.00 );

var r1 = from p in products
where x.Category.MyType == 1 && x.Price > 900.00
select x;

//Uma propriedade
var result2 = products.Where(x => x.Category.MyType == 1 && x.Price > 900.00 ).Select(x => x.Name);

var r2 = from p in products
where x.Category.MyType == 1 && x.Price > 900.00
select x.Name;

//Objeto Anon (conjunto de propriedades em um objeto "anonimo")
var result3 = products.Where(x => x.Name[0] == 'C').Select(x => new {x.Name, x.Id});

var r3 = from p in products
where x.Name[0] == 'C'
select new{
    x.Name, 
    x.Id
};

//filtrando lista de lista filtrada
var result4 = result3.Skip(1).Take(4);

var r4 = (from p in result3
    select p).Skip(1).Take(4);

//Limitando número
var result4 = result3.FirstOrDefault();

```
