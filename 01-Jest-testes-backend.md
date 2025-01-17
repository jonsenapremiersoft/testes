# Testes no Backend - Node/Nest.js

Os testes unitários são a base da nossa pirâmide de testes. No backend, eles são essenciais para validar o comportamento de funções e métodos isoladamente.

### Configuração Inicial

Em um projeto Nest.js, começamos instalando as dependências necessárias:

```bash
npm install --save-dev @nestjs/testing jest @types/jest
```

### Estrutura Básica de um Teste

```typescript
import { Test, TestingModule } from '@nestjs/testing';
import { UserService } from './user.service';

describe('UserService', () => {
  let service: UserService;

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      providers: [UserService],
    }).compile();

    service = module.get<UserService>(UserService);
  });

  it('should be defined', () => {
    expect(service).toBeDefined();
  });
});
```

## Testes de Controllers e Services

### Testando Controllers

Controllers são responsáveis por gerenciar as requisições HTTP. Veja como testar um controller:

```typescript
describe('UserController', () => {
  let controller: UserController;
  let service: UserService;

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      controllers: [UserController],
      providers: [UserService],
    }).compile();

    controller = module.get<UserController>(UserController);
    service = module.get<UserService>(UserService);
  });

  it('should return all users', async () => {
    const result = [{ id: 1, name: 'John' }];
    jest.spyOn(service, 'findAll').mockImplementation(() => Promise.resolve(result));

    expect(await controller.findAll()).toBe(result);
  });
});
```

### Testando Services

Services contêm a lógica de negócio. Exemplo de teste de service:

```typescript
describe('UserService', () => {
  let service: UserService;
  let repository: Repository<User>;

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      providers: [
        UserService,
        {
          provide: getRepositoryToken(User),
          useValue: mockRepository,
        },
      ],
    }).compile();

    service = module.get<UserService>(UserService);
    repository = module.get<Repository<User>>(getRepositoryToken(User));
  });

  it('should create a new user', async () => {
    const user = { name: 'John', email: 'john@example.com' };
    jest.spyOn(repository, 'save').mockResolvedValue(user as User);

    const result = await service.create(user);
    expect(result).toEqual(user);
  });
});
```

## Mocking de Dependências

O mock é essencial para isolar o código que estamos testando. No Nest.js, podemos fazer mock de:

### Serviços

```typescript
const mockUserService = {
  findAll: jest.fn(),
  findOne: jest.fn(),
  create: jest.fn(),
};
```

### Repositórios

```typescript
const mockRepository = {
  find: jest.fn(),
  findOne: jest.fn(),
  save: jest.fn(),
  create: jest.fn(),
};
```

## Testes de Validação e Transformação de Dados

### Testando DTOs e Pipes

```typescript
describe('CreateUserDto', () => {
  it('should validate user input', async () => {
    const dto = new CreateUserDto();
    dto.email = 'invalid-email';
    
    const errors = await validate(dto);
    expect(errors.length).toBeGreaterThan(0);
  });
});
```

## Cobertura de Testes

Para verificar a cobertura de testes, adicione no package.json:

```json
{
  "scripts": {
    "test:cov": "jest --coverage"
  }
}
```

### Interpretando o Relatório de Cobertura

O relatório mostra quatro métricas principais:
- Statements: percentual de declarações executadas
- Branches: percentual de caminhos condicionais testados
- Functions: percentual de funções testadas
- Lines: percentual de linhas de código executadas

## Boas Práticas

1. Mantenha os testes simples e focados
2. Use descrições claras para os casos de teste
3. Organize os testes seguindo a estrutura do código
4. Faça mock apenas do necessário
5. Mantenha a cobertura de testes acima de 80%
