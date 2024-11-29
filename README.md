
# Tutorial de Aplicativo CRUD com Angular 18

Neste guia, você aprenderá a criar um aplicativo CRUD utilizando o Angular 18. Este tutorial irá guiá-lo passo a passo para realizar operações CRUD com uma API web. Também utilizaremos o Bootstrap 5 para estilização.

O foco será construir um módulo CRUD para gerenciar postagens, incluindo as funcionalidades de listar, visualizar, criar, atualizar e excluir. Usaremos a API JSONPlaceholder para simplificar o processo.

---

## Etapas para Criar um CRUD no Angular 18

### Passo 1: Criar um Projeto Angular 18

Execute o comando a seguir para criar um novo projeto Angular:

```bash
ng new meu-novo-projeto
```

---

### Passo 2: Instalar o Bootstrap

Instale o Bootstrap e adicione-o ao projeto:

```bash
npm install bootstrap --save
```

Atualize o arquivo `angular.json` para incluir o CSS do Bootstrap:

```json
"styles": [
  "node_modules/bootstrap/dist/css/bootstrap.min.css",
  "src/styles.css"
]
```

---

### Passo 3: Criar um Módulo para Postagens

Crie um novo módulo chamado "postagens" utilizando o CLI do Angular:

```bash
ng generate module post
```

---

### Passo 4: Criar Componentes para o Módulo

Gere os componentes necessários para listar, visualizar, criar e editar postagens:

```bash
ng generate component post/index
ng generate component post/view
ng generate component post/create
ng generate component post/edit
```

---

### Passo 5: Definir Rotas

Configure as rotas no arquivo `app.routes.ts`:

```typescript
import { Routes } from '@angular/router';
import { IndexComponent } from './post/index/index.component';
import { ViewComponent } from './post/view/view.component';
import { CreateComponent } from './post/create/create.component';
import { EditComponent } from './post/edit/edit.component';

export const routes: Routes = [
  { path: 'post', redirectTo: 'post/index', pathMatch: 'full' },
  { path: 'post/index', component: IndexComponent },
  { path: 'post/:postId/view', component: ViewComponent },
  { path: 'post/create', component: CreateComponent },
  { path: 'post/:postId/edit', component: EditComponent }
];
```

---

### Passo 6: Criar uma Interface

Crie uma interface para modelar os dados das postagens:

```bash
ng generate interface post/post
```

**`src/app/post/post.ts`**:

```typescript
export interface Post {
  id: number;
  title: string;
  body: string;
}
```

---

### Passo 7: Criar um Serviço

Crie um serviço para interagir com a API:

```bash
ng generate service post/post
```

**`src/app/post/post.service.ts`**:

```typescript
import { Injectable } from '@angular/core';
import { HttpClient, HttpHeaders } from '@angular/common/http';
import { Observable, throwError } from 'rxjs';
import { catchError } from 'rxjs/operators';
import { Post } from './post';

@Injectable({
  providedIn: 'root'
})
export class PostService {
  private apiURL = 'https://jsonplaceholder.typicode.com';
  httpOptions = {
    headers: new HttpHeaders({ 'Content-Type': 'application/json' })
  };

  constructor(private httpClient: HttpClient) {}

  getAll(): Observable<Post[]> {
    return this.httpClient.get<Post[]>(`${this.apiURL}/posts/`)
      .pipe(catchError(this.errorHandler));
  }

  create(post: Post): Observable<Post> {
    return this.httpClient.post<Post>(`${this.apiURL}/posts/`, JSON.stringify(post), this.httpOptions)
      .pipe(catchError(this.errorHandler));
  }

  find(id: number): Observable<Post> {
    return this.httpClient.get<Post>(`${this.apiURL}/posts/${id}`)
      .pipe(catchError(this.errorHandler));
  }

  update(id: number, post: Post): Observable<Post> {
    return this.httpClient.put<Post>(`${this.apiURL}/posts/${id}`, JSON.stringify(post), this.httpOptions)
      .pipe(catchError(this.errorHandler));
  }

  delete(id: number): Observable<void> {
    return this.httpClient.delete<void>(`${this.apiURL}/posts/${id}`, this.httpOptions)
      .pipe(catchError(this.errorHandler));
  }

  errorHandler(error: any): Observable<never> {
    const errorMessage = error.error instanceof ErrorEvent
      ? error.error.message
      : `Error Code: ${error.status}
Message: ${error.message}`;
    return throwError(errorMessage);
  }
}
```

---

(Os passos restantes seguem o mesmo padrão.)
