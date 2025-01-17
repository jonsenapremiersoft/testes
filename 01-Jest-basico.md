# Testes em Javascript com Jest

## O que é o Jest?

O Jest é um framework de testes criado pelo Facebook que se destaca por:

- Velocidade na execução dos testes
- Configuração zero para a maioria dos projetos JavaScript
- Interface intuitiva e feedback detalhado
- Cobertura de código integrada
- Snapshots para testes de UI

## Configurando o Ambiente

### 1. Iniciando um Projeto Node.js

Primeiro, crie uma pasta para seu projeto e inicialize o Node.js:

```bash
mkdir meu-projeto-testes
cd meu-projeto-testes
npm init -y
```

### 2. Instalando o Jest

Instale o Jest como dependência de desenvolvimento:

```bash
npm install --save-dev jest
```

### 3. Configurando o package.json

Adicione o script de teste no seu `package.json`:

```json
{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch"
  }
}
```

## Estrutura de Arquivos

Uma boa estrutura de arquivos para testes segue estas convenções:

```
meu-projeto-testes/
├── src/
│   └── calculadora.js
└── tests/
    └── calculadora.test.js
```

## Escrevendo Seu Primeiro Teste

### 1. Crie o arquivo de implementação (`src/calculadora.js`):

```javascript
function soma(a, b) {
  return a + b;
}

module.exports = { soma };
```

### 2. Crie o arquivo de teste (`tests/calculadora.test.js`):

```javascript
const { soma } = require('../src/calculadora');

describe('Calculadora', () => {
  test('deve somar dois números corretamente', () => {
    // Arrange (Preparação)
    const num1 = 2;
    const num2 = 3;
    
    // Act (Ação)
    const resultado = soma(num1, num2);
    
    // Assert (Verificação)
    expect(resultado).toBe(5);
  });
});
```

## Executando os Testes

Para executar os testes, use o comando:

```bash
npm test
```

Para executar em modo watch (os testes são reexecutados automaticamente quando há mudanças):

```bash
npm run test:watch
```

## Anatomia de um Teste

Em Jest, usamos estas funções principais:

- `describe`: Agrupa testes relacionados
- `test` ou `it`: Define um caso de teste individual
- `expect`: Cria asserções sobre o resultado esperado

O padrão AAA (Arrange-Act-Assert) é uma boa prática para estruturar testes:

1. **Arrange**: Preparar os dados e condições para o teste
2. **Act**: Executar a função ou comportamento que está sendo testado
3. **Assert**: Verificar se o resultado está correto

## Exercício Prático

1. Crie uma função `subtracao(a, b)` na calculadora
2. Escreva testes para verificar:
   - Se a subtração de números positivos funciona
   - Se a subtração com números negativos funciona
   - Se a subtração com zero funciona corretamente

## Dicas Importantes

- Sempre nomeie seus testes de forma descritiva
- Mantenha os testes simples e focados
- Teste tanto casos de sucesso quanto de erro
- Evite dependências entre testes
- Use beforeEach/afterEach quando necessário para setup/cleanup
