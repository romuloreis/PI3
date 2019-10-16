# Projetos desenvolvidos na cadeira de Programação para Internet III

# Tópicos
## Definição de localidade (_Locale_) e Formatação de datas & números
### Globalização e localização (_Locale_) no ASP.NET Core
Se você pretende suportar multiplas línguas na sua aplicação, você deve começar por esse [ARTIGO](https://docs.microsoft.com/pt-br/aspnet/core/fundamentals/localization?view=aspnetcore-2.2). Aqui também entra questões culturais, como ordem pradrão das datas dd/mm/aaaa ou mm/dd/aaaa, marcador de casa decimal _,_ ou _._ e símbolo monetário da moeda vigente no país do usuário.

As opções de localidade podem ser definidas dentro método _Configure()_ do arquivo _Startup.cs_, 
conforme o exemplo abaixo que define a localidade padrão baseada em english-United States

            > Não esqueça de importar
            > using System.Globalization;
            > using Microsoft.AspNetCore.Localization;

```cs

            //Instância um objeto CultureInfo
            var enUS = new CultureInfo("en-US");
            //Instância a opção de localização, com um conjunto de definições
            var localizationOption = new RequestLocalizationOptions
            {
                //Definição da Cultura padrão
                DefaultRequestCulture = new RequestCulture(enUS),
                SupportedCultures = new List<CultureInfo> { enUS },
                SupportedUICultures = new List<CultureInfo> { enUS }
            };

            //Define o Locale padrão
            app.UseRequestLocalization(localizationOption);
```

### Labels Customizados - [Display]
 
 Podemos personalizar os labels das propriedades dos _models_ por meio da _annotation_ Display. 
 Assim, serão exibidos termos mais adequados nas views.
 
 ```cs
            [Display(Name = "Data de Nascimento")]
            public DateTime BirthDate { get; set; }
 ```
 
### Semântica da data - [DataType]

Por padrão o ASP.NET Core vai tratar propriedades do tipo _DataTime_ como sendo dia, mês, ano, hora, minutos.
Mas, nem sempre queremos ambas informações (data e hora).
Então, podemos personalizar os labels das propriedades dos _models_ por meio da _annotation_ DataType. 
Assim, serão exibidos termos mais adequados nas views.

No exemplo abaixo definimos que na view essa propriedade vai ser tratada apenas como Data.
Note que também podemos usar a _annotation_ DataType para definirmos o comportamento nas views para outros 
tipos de propriedades, como uma propriedade que armazena um e-mail pode ter o DataType.Email, fazendo com que 
a view se comporte de maneira mais adequada para esse tipo de dado. Nesse caso do e-mail, o valor da propriedade 
vai ser mostrado como um link _mailto_ para abrir o seu cliente de e-mail padrão.
 
 ```cs
            [Display(Name = "Data de Nascimento")]
            [DataType(DataType.Date)]
            public DateTime BirthDate { get; set; }
 ```
 
 ### Formato de exebição - [DisplayFormat]
Podemos personalizar os padrões de formatação da exibição dos dados por meio da _annotation_ DisplayFormat. 
Como por exemplo definir o número de casas decimais que você deseja mostrar ou o formato da data.

 ```cs
        /*O valor 0-zero indica o valor do atributo, o qual vai ter a formatação F2, 
         * a qual é a formatação que define que serão exibidas duas casas decimais*/
        [DisplayFormat(DataFormatString ="{0:F2}")]
        public double Salary { get; set; }
        
         /*O valor 0-zero indica o valor do atributo, o qual vai ter a formatação dia, mês e ano, 
         * a qual é a formatação que define que serão exibidas duas casas decimais*/
        [DisplayFormat(DataFormatString ="{0:dd/MM/yyyy}")]
        public DateTime BirthDate { get; set; }
 ```



# Material 
 - [Ambiente](aspnetcoremvc/ambiente.md)
 - [Criando Projeto](aspnetcoremvc/criandoprojeto.md)
 - [Criando Department](aspnetcoremvc/department.md)
 - [Criando Demais Modelos](aspnetcoremvc/criandomodels.md) 
 - [Seeding Service](seedingservice.md)
 - [Criando Controlador de Vendedores](aspnetcoremvc/controladores-seller.md)
 - [View Models](aspnetcoremvc/viewmodels.md)
 - [Data Annotations - Code First - Documentação](https://docs.microsoft.com/pt-br/ef/ef6/modeling/code-first/data-annotations)
 - [Tipos de notações (resumo)](https://github.com/romuloreis/P4I/blob/master/aspnetcoremvc/annotations.md)
