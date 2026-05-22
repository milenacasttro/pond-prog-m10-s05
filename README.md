# Aplicando Testes com .NET 5

Repositório criado para a atividade prática de testes de software. Fiz fork dos três repositórios indicados, rodei cada um no GitHub Codespaces e documentei os resultados aqui.

Tutorial de referência: [Testes de Software com .NET 5 — Renato Groffe](https://renatogroffe.medium.com/testes-de-software-com-net-5-exemplos-de-utiliza%C3%A7%C3%A3o-9b5514119ba2)

## Forks

| Repositório | Link |
|---|---|
| DotNet5-xUnit | [fork](https://github.com/milenacasttro/DotNet5-xUnit#) |
| DotNet5-Moq-xUnit-FluentAssertions | [fork](https://github.com/milenacasttro/DotNet5-Moq-xUnit-FluentAssertions) |
| ASPNETCore5-REST_API-xUnit-SpecFlow-Swagger-Docker_JurosCompostos | [fork](https://github.com/milenacasttro/ASPNETCore5-REST_API-xUnit-SpecFlow-Swagger-Docker_JurosCompostos) |

---

## Configuração do ambiente

O Codespaces não tem o .NET 5 instalado por padrão, então precisei instalar antes de rodar qualquer coisa:

```bash
wget https://dot.net/v1/dotnet-install.sh -O dotnet-install.sh
bash ./dotnet-install.sh --channel 5.0
```

Depois, dentro da pasta de testes de cada projeto:

```bash
dotnet test
```

---

## 1. Testes de Unidade com xUnit

Testes de unidade verificam se uma parte específica do código funciona corretamente de forma isolada. A ideia é simples: você passa uma entrada, define qual é a saída esperada, e o teste confirma se o método se comporta como deveria.

Nesse repositório, o método testado converte temperaturas de Fahrenheit para Celsius. O xUnit usa os atributos `[Theory]` e `[InlineData]` para passar vários valores diferentes para o mesmo teste sem precisar escrever um teste novo para cada caso.

### Cenários de exemplo

**Cenário 1 — 32°F → 0°C**
Ponto de congelamento da água. O teste confirma que a função retorna exatamente 0 ao receber 32 como entrada.

**Cenário 2 — 98.6°F → 37°C**
Temperatura do corpo humano. Valida que a conversão lida corretamente com casas decimais.

### Execução

```bash
cd Temperatura.Testes
dotnet test
```

### Resultado

![Testes de Unidade com xUnit](/temperatura.png)

> 6 testes executados, 0 falhas, duração: 5.0s.

---

## 2. Mock Objects com Moq e Fluent Assertions

Quando o código depende de algo externo — como um serviço de consulta de crédito — não faz sentido chamar esse serviço de verdade nos testes. Para isso existem os mocks: objetos falsos que simulam o comportamento da dependência com respostas controladas.

Aqui usei o **Moq** para criar um mock da interface `IServicoConsultaCredito` e o **FluentAssertions** para deixar as verificações mais legíveis. Em vez de `Assert.Equal(x, y)`, escreve-se `resultado.Should().Be(x)`, o que facilita entender o que o teste está verificando.

### Cenários de exemplo

**Cenário 1 — Crédito aprovado**
O mock retorna aprovação com um limite definido. O teste verifica se a lógica da aplicação segue o fluxo correto para esse caso.

**Cenário 2 — Crédito negado**
O mock retorna negação. O teste garante que a aplicação trata esse caso sem avançar para etapas que não deveriam ser executadas.

### Execução

```bash
cd ConsultaCredito.Testes
dotnet test
```

### Resultado

![Testes com Moq e FluentAssertions](/credito.png)

> 4 testes executados, 0 falhas, duração: 3.3s.

---

## 3. Testes de Comportamento com SpecFlow (BDD)

O SpecFlow permite escrever testes em linguagem natural usando a sintaxe Gherkin (`Given / When / Then`). Cada cenário descreve um comportamento esperado da aplicação e o SpecFlow mapeia essas frases para código C# que de fato executa o teste.

Nesse repositório, os testes cobrem uma API REST de cálculo de juros compostos. O cenário é descrito em um arquivo `.feature` e a classe de Step Definition faz a ponte entre o texto e as chamadas reais à API.

### Cenários de exemplo

**Cenário 1 — Cálculo básico de juros compostos**
- **Given** capital de R$ 1000, taxa de 1% ao mês e prazo de 12 meses
- **When** a API é chamada
- **Then** o valor retornado deve ser aproximadamente R$ 1126,83

**Cenário 2 — Prazo estendido**
- **Given** capital de R$ 5000, taxa de 0,5% ao mês e prazo de 24 meses
- **When** a API é chamada
- **Then** o resultado deve refletir o crescimento composto ao longo dos 24 meses

### Execução

```bash
cd APIFinancas.Especificacoes
dotnet test
```

### Resultado

![Testes com SpecFlow](/api-financas.png)

> 7 testes executados, 0 falhas, duração: 6.2s.