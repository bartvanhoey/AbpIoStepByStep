## Delete Book

Add `delete` method to `books.service.ts`

```typescript
delete(id: string): Observable<void> {
  return this.restService.request<void, void>({
    method: 'DELETE',
    url: `/api/app/book/${id}`
  });
}
```

Add `DeleteBook` action to `books.actions.ts`

```typescript
export class DeleteBook {
  static readonly type = '[Books] Delete';
  constructor(public id: string) {}
}
```

Add `delete` method to `books.state.ts`

```typescript
import { ... , DeleteBook } from '../actions/books.actions';
//...
@Action(DeleteBook)
delete(ctx: StateContext<Books.State>, action: DeleteBook) {
  return this.booksService.delete(action.id).pipe(switchMap(() => ctx.dispatch(new GetBooks())));
}
```

Add `delete` button to `ngbDropdownMenu` in `book-list.component.html`

```html
<div ngbDropdownMenu>
  ...
  <button ngbDropdownItem (click)="delete(data.id, data.name)">Delete</button>
</div>
```

Inject `ConfirmationService` in `book-list.component.ts`

```typescript
import { ConfirmationService } from '@abp/ng.theme.shared';
//...
constructor(
  //...
  private confirmationService: ConfirmationService
)
```

Add `delete` method to `book-list.component.ts`

```typescript
import { ... , DeleteBook } from '../../store/actions';
import { ... , Toaster } from '@abp/ng.theme.shared';
//...
delete(id: string, name: string) {
  this.confirmationService
    .error(`${name} will be deleted. Do you confirm that?`, 'Are you sure?')
    .subscribe(status => {
      if (status === Toaster.Status.confirm) {
        this.store.dispatch(new DeleteBook(id));
      }
    });
}
```

[Home](./../../../README.md) | [Previous Step](StepByStep/../../Step9/Step9.md)
