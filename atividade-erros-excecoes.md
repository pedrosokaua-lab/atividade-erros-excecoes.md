                    Atividade de Tratamento de Erros e Exceções

O que é o tratamento de erros?
É a lógica programada para interceptar falhas em tempo de execução, permitindo que o sistema reaja de forma controlada em vez de simplesmente travar.

O que é uma exceção?
É um evento indesejado ou inesperado que interrompe o fluxo normal das instruções.

Diferença entre erro e exceção:
Exceção: É uma condição anormal que o programa consegue prever, capturar e recuperar para continuar funcionando.
Erro (Error): Geralmente representa falhas graves no ambiente de execução ou no sistema que costumam ser irrecuperáveis e encerram a aplicação imediatamente.
EX.:
function dividir(a: number, b: number): number {
  if (b === 0) {
    // Lançando uma exceção explicitamente
    throw new Error("Divisão por zero não é permitida.");
  }
  return a / b;
}

function executarDivisao(a: number, b: number): void {
  try {
    // Tenta executar o código que pode gerar uma exceção
    const resultado = dividir(a, b);
    console.log(`Resultado da divisão: ${resultado}`);
  } catch (error) {
    // Captura a exceção caso ela ocorra e trata o problema
    if (error instanceof Error) {
      console.error(`Erro capturado: ${error.message}`);
    } else {
      console.error("Ocorreu um erro desconhecido.");
    }
  } finally {
    // Bloco opcional que sempre será executado, com erro ou não
    console.log("Operação de divisão finalizada.");
  }
}

// Testando o cenário com sucesso
executarDivisao(10, 2); 

// Testando o cenário que gera uma exceção
executarDivisao(10, 0);

2. Tratamento de exceções
garantir que um programa não pare de funcionar abruptamente quando ocorre um erro inesperado durante a sua execução

EX.: 
function converterParaObjeto(textoJson: string): void {
  try {
    // Código que pode gerar um erro
    const resultado = JSON.parse(textoJson);
    console.log("Conversão realizada com sucesso:", resultado);
  } catch (erro) {
    // Código executado apenas se ocorrer um erro no bloco try
    console.error("Não foi possível processar o JSON fornecido.");
    if (erro instanceof Error) {
      console.error(`Detalhes do erro: ${erro.message}`);
    }
  }
}

// Cenário 1: JSON Válido (Funciona normalmente)
converterParaObjeto('{"nome": "Ana", "idade": 28}');

// Cenário 2: JSON Inválido (Ativa o bloco catch)
converterParaObjeto('Texto inválido que não é um JSON');

3. try, catch e finally
try: Define o bloco de código que você deseja testar. O TypeScript tentará executar o código de dentro deste bloco. Se ocorrer qualquer erro ali dentro, a execução é interrompida imediatamente e o controle é passado para o bloco catch.
catch: É o bloco que captura e trata o erro. Ele só é executado se alguma linha de código dentro do bloco try falhar. Você recebe um objeto de erro (geralmente tipado como unknown no TypeScript) para analisar o que deu errado.
finally: Define um bloco de código que sempre será executado, independentemente de ter ocorrido um erro ou não. Ele é ideal para ações de limpeza, como fechar conexões, liberar memória ou resetar o estado de um carregamento (loading).
EX.:
function buscarDadosDoUsuario(id: number): void {
  console.log("Iniciando conexão com o banco de dados...");
  
  try {
    // Código que pode falhar
    if (id <= 0) {
      throw new Error("ID de usuário inválido!"); // Força a criação de um erro
    }
    
    console.log(`Dados do usuário com ID ${id} recuperados com sucesso.`);
  } catch (error: unknown) {
    // Trata o erro de forma segura
    if (error instanceof Error) {
      console.error(`Erro capturado: ${error.message}`);
    } else {
      console.error("Ocorreu um erro desconhecido.");
    }
  } finally {
    // Sempre executa, limpando os recursos
    console.log("Conexão com o banco de dados encerrada.");
  }
}

// Cenário 1: Execução com sucesso (não entra no catch)
buscarDadosDoUsuario(42);

console.log("\n-------------------\n");

// Cenário 2: Execução com erro (entra no catch)
buscarDadosDoUsuario(-1);

4. throw
serve para lançar uma exceção explicitamente quando o código encontra uma situação inválida, erro ou comportamento inesperado

EX.:

function verificarMaioridade(idade: number): void {
  if (idade < 0) {
    // Lança uma exceção se a idade for inválida
    throw new Error("A idade não pode ser um valor negativo.");
  }

  if (idade >= 18) {
    console.log("Acesso permitido: Usuário é maior de idade.");
  } else {
    console.log("Acesso negado: Usuário é menor de idade.");
  }
}

// Tratamento da exceção com try...catch
try {
  verificarMaioridade(-5); // Situação inválida que vai disparar o throw
} catch (error) {
  if (error instanceof Error) {
    console.error(`Erro capturado: ${error.message}`);
  } else {
    console.error("Ocorreu um erro desconhecido.");
  }
}


5. Aplicação prática
// Definição da estrutura de uma conta bancária
interface ContaBancaria {
  titular: string;
  saldo: number;
}

// Função principal de transferência
function realizarTransferencia(origem: ContaBancaria, destino: ContaBancaria, valor: number): void {
  // Regra 1: Rejeitar valores menores ou iguais a zero
  if (valor <= 0) {
    throw new Error("O valor da transferência deve ser maior que zero.");
  }

  // Regra 2: Rejeitar transferências maiores que o saldo disponível
  if (valor > origem.saldo) {
    throw new Error(`Saldo insuficiente. Saldo disponível: R$ ${origem.saldo.toFixed(2)}.`);
  }

  // Se passar pelas validações, executa a transferência
  origem.saldo -= valor;
  destino.saldo += valor;
  console.log(`Transferência de R$ ${valor.toFixed(2)} realizada com sucesso de ${origem.titular} para ${destino.destino}!`);
}

// Função auxiliar para envelopar o tratamento de exceções
function executarOperacao(origem: ContaBancaria, destino: ContaBancaria, valor: number): void {
  try {
    realizarTransferencia(origem, destino, valor);
  } catch (error) {
    // Tratamento das exceções lançadas pelo 'throw'
    if (error instanceof Error) {
      console.error(`[FALHA NA OPERAÇÃO] ${error.message}`);
    } else {
      console.error("[FALHA NA OPERAÇÃO] Erro inesperado no sistema.");
    }
  }
}

// --- Demonstração Prática ---

// Contas iniciais para teste
const contaAlice: ContaBancaria = { titular: "Alice", saldo: 500.00 };
const contaBob: ContaBancaria = { titular: "Bob", saldo: 100.00 };

console.log("--- Iniciando simulações de transferência ---");

// Cenário de Erro 1: Valor menor ou igual a zero
console.log("\nTentativa 1: Valor inválido (R\$ -50.00)");
executarOperacao(contaAlice, contaBob, -50);

// Cenário de Erro 2: Saldo insuficiente
console.log("\nTentativa 2: Valor maior que o saldo (R\$ 600.00)");
executarOperacao(contaAlice, contaBob, 600);

// Cenário de Sucesso: Dados válidos
console.log("\nTentativa 3: Valor válido (R\$ 200.00)");
executarOperacao(contaAlice, contaBob, 200);

console.log("\nSaldos finais:", { contaAlice, contaBob });













