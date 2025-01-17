# Fundamentos de Testes no Desenvolvimento Web

## 1. O que são testes e por que são importantes?

Os testes de software são procedimentos sistemáticos que verificam se um programa está funcionando conforme o esperado. Imagine que você está construindo uma casa - você não gostaria de verificar se as fundações estão sólidas, se as paredes estão bem construídas e se o telhado não vai vazar, certo? Da mesma forma, testamos nosso código para garantir sua qualidade e confiabilidade.

### Benefícios dos Testes:

- Identificação precoce de problemas: quanto mais cedo encontramos um bug, mais barato e fácil é corrigi-lo
- Documentação viva do código: testes bem escritos servem como documentação de como seu código deve funcionar
- Refatoração segura: você pode melhorar seu código com confiança, sabendo que os testes alertarão sobre possíveis problemas
- Redução de custos: menos bugs em produção significa menos tempo gasto com correções e maior satisfação dos usuários

## 2. A Pirâmide de Testes

A pirâmide de testes é um conceito que nos ajuda a entender os diferentes níveis de testes e sua proporção ideal em um projeto.

![Tests Piramid](https://global-uploads.webflow.com/619e15d781b21202de206fb5/628b0dca3e6eda9219d40a6a_The-Testing-Pyramid-Simplified-for-One-and-All-1280X720%20(1).jpg)

### Testes Unitários (Base da Pirâmide)
São os testes mais básicos e fundamentais, que verificam o comportamento de unidades individuais de código (geralmente funções ou métodos).

```javascript
// Exemplo de teste unitário
function soma(a, b) {
    return a + b;
}

test('soma dois números corretamente', () => {
    expect(soma(2, 3)).toBe(5);
});
```

### Testes de Integração (Meio da Pirâmide)
Verificam como diferentes partes do sistema trabalham juntas, por exemplo, como seu código interage com um banco de dados ou uma API.

```javascript
// Exemplo de teste de integração
test('salva usuário no banco de dados', async () => {
    const usuario = { nome: 'João', email: 'joao@email.com' };
    await cadastrarUsuario(usuario);
    const usuarioSalvo = await buscarUsuario('joao@email.com');
    expect(usuarioSalvo.nome).toBe('João');
});
```

### Testes End-to-End (Topo da Pirâmide)
Simulam o comportamento real do usuário, testando o sistema como um todo.

```javascript
// Exemplo de teste E2E usando Cypress
describe('Página de login', () => {
    it('permite que usuário faça login', () => {
        cy.visit('/login');
        cy.get('#email').type('usuario@teste.com');
        cy.get('#senha').type('senha123');
        cy.get('#btnLogin').click();
        cy.url().should('include', '/dashboard');
    });
});
```

## 3. TDD (Test-Driven Development)

TDD é uma metodologia de desenvolvimento onde escrevemos os testes antes do código de produção. O ciclo do TDD consiste em três fases:

1. **Red**: Escreva um teste que falha
2. **Green**: Escreva o código mais simples possível para fazer o teste passar
3. **Refactor**: Melhore o código mantendo os testes passando

### Exemplo Prático de TDD:

```javascript
// 1. Red - Primeiro escrevemos o teste
test('validaEmail retorna true para email válido', () => {
    expect(validaEmail('usuario@dominio.com')).toBe(true);
});

// 2. Green - Implementamos a função
function validaEmail(email) {
    return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
}

// 3. Refactor - Melhoramos o código se necessário
```

## 4. Conceitos Fundamentais

### Assertions (Afirmações)
São declarações que verificam se um valor atende a uma condição esperada.

```javascript
expect(resultado).toBe(10);
expect(array).toContain('item');
expect(objeto).toHaveProperty('nome');
```

### Mocks
São objetos que simulam o comportamento de objetos reais de forma controlada.

```javascript
// Mock de uma função de API
const apiMock = jest.fn().mockResolvedValue({ data: 'resultado' });
```

### Stubs
São implementações simplificadas que substituem funcionalidades complexas.

```javascript
// Stub de um serviço de pagamento
const pagamentoStub = {
    processar: () => Promise.resolve({ sucesso: true })
};
```

### Spies
São wrappers que nos permitem observar o comportamento de uma função.

```javascript
const spy = jest.spyOn(objeto, 'metodo');
objeto.metodo('argumento');
expect(spy).toHaveBeenCalledWith('argumento');
```

