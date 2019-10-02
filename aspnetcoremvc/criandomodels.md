# Criando os demais modelos do projeto

Para isso vamos seguir a seguinte ordem:
 - Criar classes
 - Criar propriedades dos tipos básicos (string, int, double, etc)
 - Criar Associações (ICollection)
 - Criar Construtores
 - Implementar métodos customizados
 - Adicionar DB Set’s no DB Context ()
 - Criar Nova Migração
 
 ## Criar classes
 
Primeiramente vamos criar a classe _Seller_ na pasta **models**. 
Agora vamos adicionar as propriedades que sejam de tipos básicos (int, string, etc), 
nesse caso serão as propriedades:

```cs
    public class Seller
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string Email { get; set; }
        public DateTime BirthDate { get; set; }
        public double BaseSalary { get; set; }
    }
```

Antes de criar a classe _SalesRecord_ vamos ter que criar o tipo _Enum_ para usar em _SalesRecord_.
Criar PASTA **Enums** dentro da pasta **models**. 
Então criar a classe _SaleStatus_ na pasta **Enums**

Altere a palavra reservada _class_ para _enum_, então adicione os seguintes valores:

```cs
    public enum SaleStatus : int
    {
        Pending = 0,
        Billed = 1,
        Canceled = 2
    }
```
Agora sim vamos criar a classe _SalesRecord_ na pasta **models**. 

```cs
using SuperApplication.Models.Enums;

namespace SuperApplication.Models
{
    public class SalesRecord
    {
        public int Id { get; set; }
        public DateTime Date { get; set; }
        public double Amount { get; set; }
        public SaleStatus Status{get; set;} /*using SuperApplication.Models.Enums;*/
    }
}
```

## Criando Associação entre as classes

Na classe _Department_ vamos adicionar 
a propriedade do tipo _ICollection <Seller> Sellers {get; set;} = new List<Seller>();_, 
lembre-se de adicionar (using System.Collections.Generic;)

```cs
    public class Department
    {
        public int Id { get; set; }
        public string Name { get; set; }
        
        /*Propriedades para associação*/
        public ICollection<Seller> Sellers { get; set; } = new List<Seller>();
    }
```

Na classe _Seller_ vamos adicionar propriedades:

```cs
    public class Seller
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string Email { get; set; }
        public DateTime BirthDate { get; set; }
        public double BaseSalary { get; set; }

        /*Propriedades para associação*/
        public Department Department { get; set; }
        public ICollection<SalesRecord> Sales { get; set; } = new List<SalesRecord>();
    }
```


Na classe _SalesRecord_ adicionar propriedade:

```cs
    public class SalesRecord
    {
        public int Id { get; set; }
        public DateTime Date { get; set; }
        public double Amount { get; set; }
        public SaleStatus Status{get; set;}

        /*Propriedades para associação*/
        public Seller Seller { get; set; }
    }
 ```


## Criando Construtores
Primeiramente, vamos criar os construtores vazios para **TODAS** às classes.
Após a criação dos métodos construtores vazios, vamos usar o gerador de método contrutor 
do próprio visual studio e vamos criar os contrutores usando apenas as 
propriedades/atributos que não são coleções, ou seja, as propriedades de tipo simples.

```cs
public Department(){}

public Department(int id, string name)
{
    Id = id;
    Name = name;
}
```

```cs
public Seller() { }

public Seller(int id, string name, string email, DateTime birthDate, double baseSalary)
{
    Id = id;
    Name = name;
    Email = email;
    BirthDate = birthDate;
    BaseSalary = baseSalary;
}
```

```cs
public SalesRecord(){}

public SalesRecord(int id, DateTime date, double amount, SaleStatus status)
{
    Id = id;
    Date = date;
    Amount = amount;
    Status = status;
}
```
## Implementando Métodos Customizados

Na classe _Seller_ vamos adicionar o método _AddSales_ e _RemoveSales_

```cs
        public void AddSales(SalesRecord sr)
        {
            Sales.Add(sr);
        }

        public void RemoveSales(SalesRecord sr)
        {
            Sales.Remove(sr);
        }
```

Ainda na classe _Seller_ vamos adicionar o método _TotalSales_

```cs
        public double TotalSales(DateTime initial, DateTime final)
        {
            //Vamos usar o linq -> using System.linq
            //Filtrar lista de vendas com lambda
            return Sales.Where(sr => sr.Date >= initial && sr.Date <= final).Sum(sr => sr.Amount);
        }
 ```

Na classe _Department_ criar o método _AddSeller_

```cs
        public void AddSeller(Seller Seller)
        {
            Sellers.Add(Seller);
        }
        
        //add using system e system.linq
        public double TotalSales(DateTime initial, DateTime final)
        {
            //Vamos usar o linq -> using System.linq
            //Filtrar lista de vendas com lambda
            //chama o método da soma do vendedor de um departamento e depois soma tudo
            return Sellers.Sum(seller => seller.TotalSales(initial, final));
        }        
```




## Adicionando DB Set’s no DB Context

Para esses modelos serem reconhecidos pelo banco de dados, vamos ter que adicionar às classes como DBSets no DBContext

Na pasta **Data** em **NomeProjetoDBContext**

Como o namespace é _ApplicationBla.Models_, já está incluso no using, então pode apagar no método todo o caminho e deixar apenas <Departament>.

```cs
        public DbSet<SuperApplication.Models.Department> Department { get; set; }
        /*Adicionar os outros DBSets*/
        public DbSet<SuperApplication.Models.Seller> Seller { get; set; }
        public DbSet<SuperApplication.Models.SalesRecord> SalesRecord { get; set; }
```

## Nova Migração
No Package Manager Console executar:

 > Add-Migration AddingOtherEntities
 
Deve ser criando de forma automática uma outra migração na pasta **migration**

Para atualizar o Banco execute:

 > Update-Database
 
Abra o banco e veja o resultado.
