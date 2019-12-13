## New Book

Create an interface `CreateInput` in `books.ts`

```typescript
export interface CreateInput {
  name: string;
  type: BookType;
  publishDate: string;
  price: number;
}
```

Add `create` method in `books.service.ts`

```typescript
create(createInput: Books.CreateInput): Observable<Books.Book> {
  return this.restService.request<Books.CreateInput, Books.Book>({
    method: 'POST',
    url: '/api/app/book',
    body: createInput
  });
}
```

Add `CreateBook` action to `books.action.ts`

```typescript
import { Books } from '../models';

export class CreateBook {
  static readonly type = '[Books] Create Book';
  constructor(public payload: Books.CreateInput) { }
}
```

Add `create` method to `books.state.ts` that will listen to `CreateBook` action

```typescript
import { ..., CreateBook } from '../actions/books.actions';
import { ..., switchMap } from 'rxjs/operators';

@Action(CreateBook)
create(ctx: StateContext<Books.State>, action: CreateBook) {
  return this.booksService
    .create(action.payload)
    .pipe(switchMap(() => ctx.dispatch(new GetBooks())));
}
```

Add `abp-modal` to `book-list.component.html`

```html
<abp-modal [(visible)]="isModalOpen">
  <ng-template #abpHeader>
    <h3>New Book</h3>
  </ng-template>

  <ng-template #abpBody> </ng-template>

  <ng-template #abpFooter>
    <button type="button" class="btn btn-secondary" #abpClose>
      Cancel
    </button>
  </ng-template>
</abp-modal>

```

Add button `New Book` to `book-list.component.html`

```html
<div class="row">
  <div class="col col-md-6">
    <h5 class="card-title">
      Books
    </h5>
  </div>
  <div class="text-right col col-md-6">
    <button id="create-role" class="btn btn-primary" type="button" (click)="createBook()">
      <i class="fa fa-plus mr-1"></i> <span>New book</span>
    </button>
  </div>
</div>
```

Add `isModalOpen` variable and `createBook` method to `book-list.component.ts`

```typescript
isModalOpen = false;



createBook() {
  this.isModalOpen = true;
}

```

Add `form` variable and inject `FormBuilder` service in `book-list.component.ts`

```typescript
import { FormGroup, FormBuilder, Validators } from '@angular/forms';

form: FormGroup;

constructor( ..., private fb: FormBuilder) {}

```

Add `buildForm` method to `book-list.component.ts`

```typescript
import { ..., Validators } from '@angular/forms';

buildForm() {
  this.form = this.fb.group({
    name: ['', Validators.required],
    type: [null, Validators.required],
    publishDate: [null, Validators.required],
    price: [null, Validators.required],
  });
}
```

Modify `createBook` method

```typescript
createBook() {
  this.buildForm();
  this.isModalOpen = true;
}
```

Add `form` in body template of modal in `book-list.component.html`

```html
<ng-template #abpBody>
  <form [formGroup]="form">
    <div class="form-group">
      <label for="book-name">Name</label><span> * </span>
      <input type="text" id="book-name" class="form-control" formControlName="name" autofocus />
    </div>

    <div class="form-group">
      <label for="book-price">Price</label><span> * </span>
      <input type="number" id="book-price" class="form-control" formControlName="price" />
    </div>

    <div class="form-group">
      <label for="book-type">Type</label><span> * </span>
      <select class="form-control" id="book-type" formControlName="type">
        <option [ngValue]="null">Select a book type</option>
        <option [ngValue]="booksType[type]" *ngFor="let type of bookTypeArr"> {{ type }}</option>
      </select>
    </div>

    <div class="form-group">
      <label>Publish date</label><span> * </span>
      <input
        #datepicker="ngbDatepicker"
        class="form-control"
        name="datepicker"
        formControlName="publishDate"
        ngbDatepicker
        (click)="datepicker.toggle()"
      />
    </div>
  </form>
</ng-template>
```

Import `NgbDatePickerModule` to `books.module.ts`

```typescript
import { NgbDatepickerModule } from '@ng-bootstrap/ng-bootstrap';

@NgModule({
  imports: [
    // ...
    NgbDatepickerModule,
  ],
})
export class BooksModule {}
```

Add `providers` to `book-list.component.ts`

```typescript
import { NgbDateAdapter, NgbDateNativeAdapter } from '@ng-bootstrap/ng-bootstrap';

@Component({
  // ...
  providers: [{ provide: NgbDateAdapter, useClass: NgbDateNativeAdapter }]
})
export class BookListComponent implements OnInit {
// ...

```

Create `bookTypeArr` array in `book-list.component.ts`

```typescript

booksType = Books.BookType;
bookTypeArr = Object.keys(Books.BookType).filter(bookType => typeof this.booksType[bookType] === 'number');
```

Add `save button` to `abpFooter` of modal in `book-list.component.html`

```html
<ng-template #abpFooter>
  <button type="button" class="btn btn-secondary" #abpClose>
    Cancel
  </button>
  <button class="btn btn-primary" (click)="save()">
    <i class="fa fa-check mr-1"></i>
    Save
  </button>
</ng-template>
```

Define a `save` method in `book-list.component.ts`

```typescript

import { ..., CreateBook } from '../../store/actions';

save() {
  if (this.form.invalid) { return; }
  this.store.dispatch(new CreateBook(this.form.value)).subscribe(() => {
    this.isModalOpen = false;
    this.form.reset();
  });
}
```

[Home](./../../../README.md) | [Previous Step](StepByStep/../../Step7/Step7.md) | [Next Step](StepByStep/../../Step9/Step9.md)
