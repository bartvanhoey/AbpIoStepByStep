## Update Book

Create an interface `UpdateInput` in `books.ts`

```typescript
export interface UpdateInput {
  name: string;
  type: BookType;
  publishDate: string;
  price: number;
}

```

Add `UpdateBook` action to `books.actions.ts`

```typescript
export class UpdateBook {
  static readonly type = '[Books] Update Book';
  constructor(public payload: Books.UpdateInput, public id: string) { }
}
```

Add `getById` and `update` methods to `books.service.ts`

```typescript
getById(id: string): Observable<Books.Book> {
  return this.restService.request<void, Books.Book>({
    method: 'GET',
    url: `/api/app/book/${id}`,
  });
}

update(updateInput: Books.UpdateInput, id: string): Observable<Books.Book> {
  return this.restService.request<Books.UpdateInput, Books.Book>({
    method: 'PUT',
    url: `/api/app/book/${id}`,
    body: updateInput
  });
}
```

Add `update` method to `books.state.ts` that will listen to `UpdateBook` action

```typescript
@Action(UpdateBook)
update(ctx: StateContext<Books.State>, action: UpdateBook) {
  return this.booksService
    .update(action.payload, action.id)
    .pipe(switchMap(() => ctx.dispatch(new GetBooks())));
}
```

Import `BooksService` and add `selectedBook` variable to `book-list.component.ts`

```typescript
import { BooksService } from '../shared/books.service';
//...
selectedBook = {} as Books.Book;

constructor(
  //...
  private booksService: BooksService
)
```

Modify `buildForm` method in `book-list.component.ts`

```typescript
buildForm() {
  this.form = this.fb.group({
    name: [this.selectedBook.name || '', Validators.required],
    type: this.selectedBook.type || null,
    publishDate: this.selectedBook.publishDate ? new Date(this.selectedBook.publishDate) : null,
    price: this.selectedBook.price || null,
  });
}
```

Add `editBook` method to `book-list.component.ts`

```typescript
editBook(id: string) {
  this.booksService.getById(id).subscribe(book => {
    this.selectedBook = book;
    this.buildForm();
    this.isModalOpen = true;
  });
}
```

Modify `createBook` method in `book-list.component.ts`

```typescript
createBook() {
  this.selectedBook = {} as Books.Book;
  //...
}
```

Modify `save` method in `book-list.component.ts`

```typescript
import { ..., UpdateBook } from '../../store/actions';

save() {
  if (this.form.invalid) { return; }
  if (this.selectedBook.id) {
    this.store.dispatch(new UpdateBook(this.form.value, this.selectedBook.id)).subscribe(() => {
      this.isModalOpen = false;
      this.form.reset();
    });
  } else {
    this.store.dispatch(new CreateBook(this.form.value))
      .subscribe(() => {
        this.isModalOpen = false;
        this.form.reset();
      });
  }
}
```

Modify `p-table` in `book-list.component.html`

```html
<p-table [value]="books$ | async" [loading]="loading" [paginator]="true" [rows]="10">
  <ng-template pTemplate="header">
    <tr>
      <th>Actions</th>
      <th>Book name</th>
      <th>Book type</th>
      <th>Publish date</th>
      <th>Price</th>
    </tr>
  </ng-template>
  <ng-template pTemplate="body" let-data>
    <tr>
      <td>
        <div ngbDropdown class="d-inline-block">
          <button
            class="btn btn-primary btn-sm dropdown-toggle"
            data-toggle="dropdown"
            aria-haspopup="true"
            ngbDropdownToggle>
            <i class="fa fa-cog mr-1"></i>Actions
          </button>
          <div ngbDropdownMenu>
            <button ngbDropdownItem (click)="editBook(data.id)">Edit</button>
          </div>
        </div>
      </td>
      <td>{{ data.name }}</td>
      <td>{{ booksType[data.type] }}</td>
      <td>{{ data.publishDate | date }}</td>
      <td>{{ data.price }}</td>
    </tr>
  </ng-template>
</p-table>
```

Update modal header in `book-list.component.html`

```html
<ng-template #abpHeader>
  <h3>{{ selectedBook.id ? 'Edit' : 'New Book' }}</h3>
</ng-template>
```

[Home](./../../../README.md) | [Previous Step](StepByStep/../../Step8/Step8.md) | [Next Step](StepByStep/../../Step10/Step10.md)
