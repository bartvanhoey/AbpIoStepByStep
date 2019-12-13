## BooksState

```bash
yarn ng generate ngxs-schematic:state books
```

Modify  `books.ts`

```typescript
export namespace Books {
  export interface State {
    books: Response;
  }

  export interface Response {
    items: Book[];
    totalCount: number;
  }

  export interface Book {
    name: string;
    type: BookType;
    publishDate: string;
    price: number;
    lastModificationTime: string;
    lastModifierId: string;
    creationTime: string;
    creatorId: string;
    id: string;
  }

  export enum BookType {
    Undefined,
    Adventure,
    Biography,
    Dystopia,
    Fantastic,
    Horror,
    Science,
    ScienceFiction,
    Poetry,
  }
}
```

### Generate BooksService

```bash
yarn ng generate service books/shared/books
```

Modify `books.service.ts`

```typescript
import { Injectable } from '@angular/core';
import { RestService } from '@abp/ng.core';
import { Books } from '../../store/models';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root',
})
export class BooksService {
  constructor(private restService: RestService) {}

  get(): Observable<Books.Response> {
    return this.restService.request<void, Books.Response>({
      method: 'GET',
      url: '/api/app/book'
    });
  }
}
```

Modify books.action.ts

```typescript
export class GetBooks {
  static readonly type = '[Books] Get';
}
```

Implement the BooksState in `books.state.ts`

```typescript
import { State, Action, StateContext, Selector } from '@ngxs/store';
import { GetBooks } from '../actions/books.actions';
import { Books } from '../models/books';
import { BooksService } from '../../books/shared/books.service';
import { tap } from 'rxjs/operators';

@State<Books.State>({
  name: 'BooksState',
  defaults: { books: {} } as Books.State,
})
export class BooksState {
  @Selector()
  static getBooks(state: Books.State) {
    return state.books.items || [];
  }

  constructor(private booksService: BooksService) {}

  @Action(GetBooks)
  get(ctx: StateContext<Books.State>) {
    return this.booksService.get().pipe(
      tap(booksResponse => {
        ctx.patchState({
          books: booksResponse,
        });
      }),
    );
  }
}
```

Modify `book-list.component.ts`

```typescript
import { Component, OnInit } from '@angular/core';
import { Store, Select } from '@ngxs/store';
import { BooksState } from '../../store/states';
import { Observable } from 'rxjs';
import { Books } from '../../store/models';
import { GetBooks } from '../../store/actions';

@Component({
  selector: 'app-book-list',
  templateUrl: './book-list.component.html',
  styleUrls: ['./book-list.component.scss'],
})
export class BookListComponent implements OnInit {
  @Select(BooksState.getBooks) books$: Observable<Books.Book[]>;

  booksType = Books.BookType;

  loading = false;

  constructor(private store: Store) {}

  ngOnInit() {
    this.loading = true;
    this.store.dispatch(new GetBooks()).subscribe(() => {
      this.loading = false;
    });
  }
}

```

Modify `book-list.component.html`

```html
<div id="wrapper" class="card">
  <div class="card-header">
    <div class="row">
      <div class="col col-md-6">
        <h5 class="card-title">
          Books
        </h5>
      </div>
    </div>
  </div>
  <div class="card-body">
    <p-table [value]="books$ | async" [loading]="loading" [paginator]="true" [rows]="10">
      <ng-template pTemplate="header">
        <tr>
          <th>Book name</th>
          <th>Book type</th>
          <th>Publish date</th>
          <th>Price</th>
        </tr>
      </ng-template>
      <ng-template pTemplate="body" let-data>
        <tr>
          <td>{{ data.name }}</td>
          <td>{{ booksType[data.type] }}</td>
          <td>{{ data.publishDate | date }}</td>
          <td>{{ data.price }}</td>
        </tr>
      </ng-template>
    </p-table>
  </div>
</div>
```

[Home](../../README.md) | [Previous Step](StepByStep/../../Step6/Step6.md) | [Next Step](StepByStep/../../Step8/Step8.md)
