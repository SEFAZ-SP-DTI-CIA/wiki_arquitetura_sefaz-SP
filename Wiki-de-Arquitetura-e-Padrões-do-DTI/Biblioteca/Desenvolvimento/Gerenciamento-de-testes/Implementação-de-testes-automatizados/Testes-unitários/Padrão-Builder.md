O padrão Builder é um _design pattern_ que flexibiliza a criação de objetos através da introdução de um passo-a-passo para a sua criação.

No contexto de testes unitários, é comum precisar de objetos em estados diferentes para diferentes cenários. Aplicando o padrão Builder, é possível reduzir a duplicidade do código que cria os objetos e manter a flexibilização para que cada teste tenha seu objeto no estado desejado. 

O exemplo a seguir ilusta a aplicação do padrão para a criação de objetos da classe Fatura.

classe Fatura:
````
public class Fatura
{
    public long IdCliente { get; private set; }
    public decimal Valor { get; private set; }
    public bool Paga { get; private set; }

    public Fatura(long idCliente, decimal valor, bool paga)
    {
        IdCliente = idCliente;
        Valor = valor;
        Paga = paga;
    }
}
````
classe FaturaBuilder:
````
public class FaturaBuilder
{
    private long _idCliente;
    private decimal _valor;
    private bool _paga;

    public FaturaBuilder()
    {
        //inicia o builder com valores padrão
        _idCliente = 12345L;
        _valor = 1000.25M;
        _paga = true;
    }

    public FaturaBuilder ComValor(decimal valor)
    {
        _valor = valor;
        return this;
    }

    public FaturaBuilder NaoPaga()
    {
        _paga = false;
        return this;
    }

    public Fatura Build()
    {
        return new Fatura(_idCliente, _valor, _paga);
    }
}
````
Dado o exemplo acima, para criar uma instância de uma fatura com os dados padrões, o código deve ser:
````
Fatura faturaTeste = new FaturaBuilder().Build();
````
Caso no cenário de teste seja necessária uma fatura não paga:
````
Fatura faturaTeste = new FaturaBuilder().NaoPga().Build();
````
Como pode se verificar, não é preciso replicar os dados para inicializar uma instância em todos os testes, e uma customização dos dados pode ser feita através dos métodos do Builder.

O fato do método do buider retornar a própria instância (ex: FaturaBuilder.ComValor) permite o encadeamento de métodos, o que torna o código mais limpo.

O padrão Builder, aplicado para criação de objetos de testes também é chamado de Test Data Builder.

# Objetos complexos
Quando um objeto é composto de outros objetos, consegue-se simplificar a sintaxe para criação dos objetos interiores através do uso de delegates (vide método ClienteBuilder.ComFatura)
````
public class Cliente
{
    public long Id { get; private set; }
    public List<Fatura> Faturas { get; private set; }

    public Cliente(long id, List<Fatura> faturas)
    {
        Id = id;
        Faturas = faturas;
    }
}

public class ClienteBuilder
{
    private long _id;
    private List<Fatura> _faturas;

    public ClienteBuilder()
    {
        _id = 12345L;
        _faturas = new List<Fatura>();
    }

    public ClienteBuilder ComFatura(Action<FaturaBuilder> action)
    {
        var faturaBuilder = new FaturaBuilder();

        action(faturaBuilder);

        var fatura = faturaBuilder.Build();
        _faturas.Add(fatura);
        return this;
    }

    public Cliente Build()
    {
        return new Cliente(_id, _faturas);
    }
}
````
Criando um cliente com duas faturas:
````
Cliente cliente = new ClienteBuilder()
    .ComFatura(a => a.NaoPaga())
    .ComFatura(a => a.ComValor(100M))
    .Build();
````

# Informações adicionais
- [Combining Object Mother and Fluent Builder for the Ultimate Test Data Factory](https://reflectoring.io/objectmother-fluent-builder/) - exemplo incrementando o Test Data Builder com Object Mother, flexibilizando a criação de dados pre-definidos.