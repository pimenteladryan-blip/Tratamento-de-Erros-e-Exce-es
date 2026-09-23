# Atividade — Tratamento de Erros e Exceções

## 1. Tratamento de erros e exceções

O tratamento de erros é um conjunto de mecanismos que permite a um software identificar, capturar e responder a anomalias durante sua execução, evitando que o sistema trave ou feche abruptamente.

Uma exceção é um evento incomum ou inesperado que acontece durante a execução do programa e interrompe o fluxo normal de instruções, como tentar ler um arquivo que não existe ou acessar uma propriedade de um objeto nulo.

A diferença entre erro e exceção reside na gravidade e na possibilidade de recuperação. Um erro geralmente refere-se a falhas críticas do sistema ou infraestrutura (como falta de memória ou falha de hardware) onde o programa não pode se recuperar. Uma exceção é uma condição anormal gerada pela lógica da aplicação que pode ser capturada e tratada pelo desenvolvedor para que o programa continue rodando.

É importante tratar erros e exceções para garantir a estabilidade do sistema, oferecer uma boa experiência ao usuário final através de mensagens claras e manter a segurança, impedindo o vazamento de dados técnicos expostos por falhas brutas.

```typescript
class Conversor {
    converterParaNumero(texto: string): number {
        const resultado = parseFloat(texto);
        if (isNaN(resultado)) {
            throw new Error("Texto invalido para conversao");
        }
        return resultado;
    }
}

try {
    const conversor = new Conversor();
    const numero = conversor.converterParaNumero("abc");
    console.log(numero);
} catch (erro) {
    console.log("Nao foi possivel converter o valor fornecido.");
}
```

---

## 2. Tratamento de exceções

A finalidade do tratamento de exceções é desviar o fluxo do programa para um caminho alternativo seguro quando algo dá errado. Isso separa o código que resolve o problema central de negócios do código responsável por lidar com as falhas, mantendo a aplicação estável.

O bloco `try` isola o código que possui risco de falhar. Se uma exceção acontecer ali dentro, o JavaScript interrompe imediatamente a execução daquela função e passa o controle para o bloco `catch`, que recebe o objeto de erro capturado e executa uma ação corretiva.

```typescript
class Repositorio {
    buscarPorId(id: number): string {
        if (id <= 0) {
            throw new Error("Identificador invalido");
        }
        return "Usuario Encontrado";
    }
}

try {
    const repo = new Repositorio();
    const resultado = repo.buscarPorId(-5);
    console.log(resultado);
} catch (error) {
    console.log("Ocorreu uma falha ao buscar o registro solicitado.");
}
```

---

## 3. try, catch e finally

O `try` delimita um bloco de código protegido onde monitoramos a ocorrência de possíveis exceções durante sua execução.

O `catch` define o bloco de código que só será executado se, e somente se, alguma exceção for lançada dentro do bloco `try` correspondente.

O `finally` define um bloco de código que sempre será executado, independentemente de ter ocorrido uma exceção ou se ela foi capturada com sucesso. Ele é usado principalmente para limpeza de recursos, como fechar conexões de banco de dados ou arquivos abertos.

```typescript
class Conexao {
    conectar(): void {
        console.log("Conexao aberta.");
    }
    executar(): void {
        throw new Error("Falha na gravacao dos dados.");
    }
    fechar(): void {
        console.log("Conexao fechada de forma segura.");
    }
}

const conexao = new Conexao();
try {
    conexao.conectar();
    conexao.executar();
} catch (error) {
    console.log("Tratando a falha de execucao encontrada.");
} finally {
    conexao.fechar();
}
```

---

## 4. throw

O `throw` serve para sinalizar explicitamente que uma condição inválida ou erro de regra de negócio aconteceu. Ele cria e lança uma exceção para o sistema, interrompendo o fluxo atual até encontrar o bloco `catch` mais próximo para tratá-la.

```typescript
class ValidadorIdade {
    validar(idade: number): void {
        if (idade < 0 || idade > 150) {
            throw new Error("A idade informada esta fora dos limites aceitaveis.");
        }
        console.log("Idade validada com sucesso.");
    }
}

try {
    const validador = new ValidadorIdade();
    validador.validar(200);
} catch (error: any) {
    console.log(error.message);
}
```

---

## 5. Aplicação prática

Abaixo está o sistema de transferência bancária simulando duas contas e validando regras de negócios fundamentais.

```typescript
class Conta {
    titular: string;
    saldo: number;

    constructor(titular: string, saldoInicial: number) {
        this.titular = titular;
        this.saldo = saldoInicial;
    }
}

class ServicoBancario {
    transferir(origem: Conta, destino: Conta, valor: number): void {
        if (valor <= 0) {
            throw new Error("O valor da transferencia deve ser superior a zero.");
        }
        if (valor > origem.saldo) {
            throw new Error("Saldo insuficiente para concluir a transferencia.");
        }
        origem.saldo -= valor;
        destino.saldo += valor;
        console.log(`Transferencia de R$ ${valor.toFixed(2)} concluida.`);
    }
}

const contaA = new Conta("Carlos", 1000);
const contaB = new Conta("Mariana", 500);
const servico = new ServicoBancario();

console.log("--- TESTE 1: VALOR NEGATIVO ---");
try {
    servico.transferir(contaA, contaB, -150);
} catch (error: any) {
    console.log(`Erro capturado: ${error.message}`);
}

console.log("\n--- TESTE 2: SALDO INSUFICIENTE ---");
try {
    servico.transferir(contaA, contaB, 2500);
} catch (error: any) {
    console.log(`Erro capturado: ${error.message}`);
}
```

### Explicação do Funcionamento do Código para a Oral:
A classe `ServicoBancario` possui o método `transferir` que atua como barreira de segurança. Antes de realizar qualquer movimentação de dinheiro, duas condicionais verificam os dados. Se o valor for negativo ou zero, o primeiro `if` cria uma exceção contendo a mensagem específica e a lança com `throw`. Se o valor for maior do que o saldo atual da conta de origem, o segundo `if` barra a operação da mesma maneira. 

Nos testes práticos executados, as chamadas inválidas foram intencionalmente envelopadas em estruturas `try/catch`. Isso permitiu interceptar os objetos de erro criados pelo `throw`, evitando a quebra da aplicação e imprimindo apenas a mensagem de erro formatada no console de forma controlada.
