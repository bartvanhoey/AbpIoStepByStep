## Books Page

Open a terminal window in `angular` folder and run `yarn` command to install NPM packages

```bash
yarn
```

### BooksModule

```bash
yarn ng generate module books --route books --module app.module`
```

`yarn start` to run the application and open [http://localhost:4200/books](http://localhost:4200/books)

### Routing

Open app-routing.module.ts and replace books as shown below:

```typescript
{
    path: 'books',
    component: ApplicationLayoutComponent,
    loadChildren: () => import('./books/books.module').then(m => m.BooksModule),
    data: {
      routes: {
        name: 'Books'
      } as ABP.Route
    }
  }
```

Replace content books.component.html with `<router-outlet></router-outlet>`

### BookListComponent

```bash
  yarn ng generate component books/book-list
```

Import `SharedModule` to the `books.module.ts`

```typescript
import { SharedModule } from '../shared/shared.module';

@NgModule({
  
  imports: [
    
    SharedModule,
  ],
})
export class BooksModule {}

```

Update `routes` in `books-routing-module`

```typescript
import { BookListComponent } from './book-list/book-list.component';

const routes: Routes = [
  {
    path: '',
    component: BooksComponent,
    children: [{ path: '', component: BookListComponent }],
  },
];

@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule],
})
export class BooksRoutingModule {}

```  

[Home](./../../../README.md) | [Previous Step](StepByStep/../../Step5/Step5.md) | [Next Step](StepByStep/../../Step7/Step7.md)
