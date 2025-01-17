# Criando um CRUD com NestJS e Testes Unitários

Neste tutorial, vamos criar um CRUD (Create, Read, Update, Delete) simples usando NestJS e implementar testes unitários. Usaremos como exemplo um sistema de gerenciamento de livros.

## Configuração do Projeto

Primeiro, vamos criar um novo projeto NestJS:

```bash
npm i -g @nestjs/cli
nest new nest-crud-example
cd nest-crud-example
```

## Estrutura do Projeto

Vamos criar os seguintes arquivos:

```
src/
  books/
    dto/
      create-book.dto.ts
      update-book.dto.ts
    entities/
      book.entity.ts
    books.controller.ts
    books.service.ts
    books.module.ts
    tests/
      books.controller.spec.ts
      books.service.spec.ts
```

## Implementação

### 1. Entity

```typescript
// src/books/entities/book.entity.ts
export class Book {
  id: number;
  title: string;
  author: string;
  price: number;
  createdAt: Date;
}
```

### 2. DTOs

```typescript
// src/books/dto/create-book.dto.ts
export class CreateBookDto {
  title: string;
  author: string;
  price: number;
}

// src/books/dto/update-book.dto.ts
export class UpdateBookDto {
  title?: string;
  author?: string;
  price?: number;
}
```

### 3. Service

```typescript
// src/books/books.service.ts
import { Injectable, NotFoundException } from '@nestjs/common';
import { Book } from './entities/book.entity';
import { CreateBookDto } from './dto/create-book.dto';
import { UpdateBookDto } from './dto/update-book.dto';

@Injectable()
export class BooksService {
  private books: Book[] = [];
  private idCounter = 1;

  create(createBookDto: CreateBookDto): Book {
    const book: Book = {
      id: this.idCounter++,
      ...createBookDto,
      createdAt: new Date(),
    };
    this.books.push(book);
    return book;
  }

  findAll(): Book[] {
    return this.books;
  }

  findOne(id: number): Book {
    const book = this.books.find(book => book.id === id);
    if (!book) {
      throw new NotFoundException(`Book with ID ${id} not found`);
    }
    return book;
  }

  update(id: number, updateBookDto: UpdateBookDto): Book {
    const bookIndex = this.books.findIndex(book => book.id === id);
    if (bookIndex === -1) {
      throw new NotFoundException(`Book with ID ${id} not found`);
    }

    const updatedBook = {
      ...this.books[bookIndex],
      ...updateBookDto,
    };

    this.books[bookIndex] = updatedBook;
    return updatedBook;
  }

  remove(id: number): void {
    const bookIndex = this.books.findIndex(book => book.id === id);
    if (bookIndex === -1) {
      throw new NotFoundException(`Book with ID ${id} not found`);
    }
    this.books.splice(bookIndex, 1);
  }
}
```

### 4. Controller

```typescript
// src/books/books.controller.ts
import { Controller, Get, Post, Body, Put, Param, Delete } from '@nestjs/common';
import { BooksService } from './books.service';
import { CreateBookDto } from './dto/create-book.dto';
import { UpdateBookDto } from './dto/update-book.dto';

@Controller('books')
export class BooksController {
  constructor(private readonly booksService: BooksService) {}

  @Post()
  create(@Body() createBookDto: CreateBookDto) {
    return this.booksService.create(createBookDto);
  }

  @Get()
  findAll() {
    return this.booksService.findAll();
  }

  @Get(':id')
  findOne(@Param('id') id: string) {
    return this.booksService.findOne(+id);
  }

  @Put(':id')
  update(@Param('id') id: string, @Body() updateBookDto: UpdateBookDto) {
    return this.booksService.update(+id, updateBookDto);
  }

  @Delete(':id')
  remove(@Param('id') id: string) {
    return this.booksService.remove(+id);
  }
}
```

### 5. Module

```typescript
// src/books/books.module.ts
import { Module } from '@nestjs/common';
import { BooksService } from './books.service';
import { BooksController } from './books.controller';

@Module({
  controllers: [BooksController],
  providers: [BooksService],
})
export class BooksModule {}
```

## Testes Unitários

Vamos implementar alguns testes unitários para o service e o controller. Por questões didáticas, implementaremos apenas alguns testes e deixaremos outros como exercício.

### Testes do Service

```typescript
// src/books/tests/books.service.spec.ts
import { Test, TestingModule } from '@nestjs/testing';
import { NotFoundException } from '@nestjs/common';
import { BooksService } from '../books.service';
import { CreateBookDto } from '../dto/create-book.dto';

describe('BooksService', () => {
  let service: BooksService;

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      providers: [BooksService],
    }).compile();

    service = module.get<BooksService>(BooksService);
  });

  it('should be defined', () => {
    expect(service).toBeDefined();
  });

  describe('create', () => {
    it('should create a book', () => {
      const createBookDto: CreateBookDto = {
        title: 'Test Book',
        author: 'Test Author',
        price: 29.99,
      };

      const book = service.create(createBookDto);

      expect(book).toEqual({
        id: expect.any(Number),
        ...createBookDto,
        createdAt: expect.any(Date),
      });
    });
  });

  describe('findOne', () => {
    it('should return a book if it exists', () => {
      const createBookDto: CreateBookDto = {
        title: 'Test Book',
        author: 'Test Author',
        price: 29.99,
      };

      const createdBook = service.create(createBookDto);
      const foundBook = service.findOne(createdBook.id);

      expect(foundBook).toEqual(createdBook);
    });

    it('should throw NotFoundException if book does not exist', () => {
      expect(() => service.findOne(999)).toThrow(NotFoundException);
    });
  });
});
```

### Testes do Controller

```typescript
// src/books/tests/books.controller.spec.ts
import { Test, TestingModule } from '@nestjs/testing';
import { BooksController } from '../books.controller';
import { BooksService } from '../books.service';
import { CreateBookDto } from '../dto/create-book.dto';

describe('BooksController', () => {
  let controller: BooksController;
  let service: BooksService;

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      controllers: [BooksController],
      providers: [BooksService],
    }).compile();

    controller = module.get<BooksController>(BooksController);
    service = module.get<BooksService>(BooksService);
  });

  it('should be defined', () => {
    expect(controller).toBeDefined();
  });

  describe('create', () => {
    it('should create a book', () => {
      const createBookDto: CreateBookDto = {
        title: 'Test Book',
        author: 'Test Author',
        price: 29.99,
      };

      const result = controller.create(createBookDto);

      expect(result).toEqual({
        id: expect.any(Number),
        ...createBookDto,
        createdAt: expect.any(Date),
      });
    });
  });
});
```

## Exercícios Propostos

Para praticar, implemente os seguintes testes:

1. Para o BooksService:
   - Teste do método `findAll()`
   - Teste do método `update()`
   - Teste do método `remove()`
   - Teste de atualização com dados parciais
   - Teste de remoção de livro inexistente

2. Para o BooksController:
   - Teste do método `findAll()`
   - Teste do método `findOne()`
   - Teste do método `update()`
   - Teste do método `remove()`
   - Teste de tratamento de erros

## Como Executar os Testes

Para executar os testes, use os seguintes comandos:

```bash
# Executar todos os testes
npm run test

# Executar testes com coverage
npm run test:cov

# Executar testes em modo watch
npm run test:watch
```
