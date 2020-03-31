```C#
    /*Classe abstrata não pode ser instânciada, 
     mas pode implementar métodos*/
    abstract class Pessoa


/*Virtual indica que meus herdeiros podem
         modificar esse método*/
        public virtual void MostrarDados() {
            Console.WriteLine("Nome: "+Nome);
            Console.WriteLine("Idade: " + Idade);
        }

        /*método abstrato - Obriga meus herdeiros 
         * à implementar esse método*/
        public abstract void Cumprimentar();



        //A palavra "virtual" permite que esse método seja sobrescrito
        public virtual void CalcularSalario() {
            double salarioTotal = Salario * 120;
            Console.WriteLine("Salário Total: "+salarioTotal);
        }





class Professor : Profissional{
        public string Area { get; set; }

        public Professor(string nome, string cpf, 
            double salario, string area) 
            :base(nome, cpf, salario) {
            Area = area;
        }

        //A palavra "override" indica que esse métodos esta
        //sobrescrevendo o método herdado
        //Exemplo de como usar "base"
        //Por ser um método "sealed" selado, os filhos dessa
        //classe não poderão sobreescrever esse método
        public sealed override void CalcularSalario()
        {
            base.CalcularSalario();
            //Poderia chamar outras funções aqui
        }


        }


//Pode sobrescrever sem o método MostrarDados
        //da classe Tecnico ter a palavra "virtual",
        //pois a característica "virtual" foi herdada 
        //de Pessoa.
        /*A palavra "sealed" indica que o método foi
         selado e não pode ser mais sobrescrito pelos 
         herdeiros da classe TecnicoEspecialista*/
        public sealed override void MostrarDados() {
            base.MostrarDados();
            Console.WriteLine("Especialidade: "
                + Especialidade);
        }
        
        
        
 /*Indica que o método herdado esta sendo 
         * sobrescrito*/
        public override void MostrarDados() {
            //Chama o método implementado na classe pai
            base.MostrarDados();
            Console.WriteLine("Função: " + Funcao);
            Console.WriteLine("Salário: " + Salario);
        }

        /*Fui obrigado a implementar esse método,
         por ser um método abstrato no meu pai*/
        public override void Cumprimentar() {
            Console.WriteLine("Estou ocupado");
        }



//A palavra "sealed" indica que essa classe é FINAL, 
    //ou seja, nenhuma classe pode extender Tecnico
    sealed class Tecnico : Profissional
    
    
    
        static class Sistema
    {
        public static double CalcularAumento(
            Assalariado obj, double txAumento) {

            return obj.GetSalario()*txAumento;
        }
    }

```
