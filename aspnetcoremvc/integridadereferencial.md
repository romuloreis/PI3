# Integridade Referencial

Ao registrar um novo vendedor, o id do departamento vai ficar null, e isso **NÃO PODE**

É necessário criar a relação entre às classes

Na classe _Seller_, vamos adicionar a propriedade **DepartmentId**, 
É importante usar o padrão de nomenclatura, pois ele entende que é uma chave estrangeira

  > Nome da Classe + Nome do Campo Chave

Assim o Framework detecta que você quer armazenar o ID do departamento na classe Seller, 
afinal tipos int são números e não podem ser nulos. 

 - Apagar Base de dados
 - Add-Migration DepartmentKey
 - Update-Database

Crie um novo vendedor e note o ERRO, pois não é mais permitido eu criar um vendedor sem informar o departamento dele.
Como não temos essa opção no formulário, vamos usar uma gambiarra temporária para testar...
Sendo assim, vamos atualizar o método Insert() do nosso SellerService para que ele passe o primeiro departamento como padrão ao 
registrar um novo vendedor, prevenindo que o erro ocorra.

obj.Department = _context.Department.First();

TESTAR!
