# **Aplicação CRUD Angular 18 - Tutorial Exemplo**

Neste guia, vamos aprender sobre uma aplicação CRUD com Angular 18. Utilizaremos operações CRUD com web API. Vamos explorar um exemplo de aplicativo CRUD em Angular 18, explicado passo a passo.

Olá! A versão mais recente do Angular, Angular 18, foi lançada há alguns meses, repleta de novos recursos e melhorias. Se você é novo no Angular ou está interessado em aprender a criar aplicações CRUD, está no lugar certo. Esta postagem vai guiá-lo através da construção de operações CRUD no Angular 18 com Bootstrap 5.

Não se preocupe, vou detalhar tudo para você. Siga estes passos simples para criar seu aplicativo CRUD no Angular 18. Após concluir todas as etapas, você verá um layout como a prévia abaixo.

Neste exemplo, vamos nos concentrar na criação de um módulo CRUD para postagens, abrangendo funcionalidades de listagem, visualização, inserção, atualização e exclusão. Para facilitar, usaremos o serviço web JSONPlaceholder API. Eles fornecem todas as APIs necessárias, como listagem, visualização, criação, exclusão e atualização, tornando nosso trabalho mais simples.

## Passos para Operação CRUD no Angular 18

- **Passo 1:** Criar Projeto Angular 18
- **Passo 2:** Instalar Bootstrap
- **Passo 3:** Criar Módulo de Postagem
- **Passo 4:** Criar Componentes para o Módulo
- **Passo 5:** Criar Rotas
- **Passo 6:** Criar Interface
- **Passo 7:** Criar Serviço
- **Passo 8:** Atualizar Lógica e Template do Componente
- **Passo 9:** Exportar provideHttpClient()
- **Executar Aplicativo Angular**

### Passo 1: Criar Projeto Angular 18

Você pode criar seu aplicativo Angular usando o seguinte comando:

```bash
ng new my-new-app
```

### Passo 2: Instalar Bootstrap

Instale o Bootstrap para sua aplicação CRUD:

```bash
npm install bootstrap --save
```

Importe no arquivo angular.json:

```json
"styles": [
  "node_modules/bootstrap/dist/css/bootstrap.min.css",
  "src/styles.css"
],
```
# **Aplicação CRUD Angular 18 - Tutorial Exemplo**

[Conteúdo inicial já traduzido anteriormente]

### Passo 3: Criar Módulo de Postagem

Após criar o aplicativo com sucesso, precisamos criar o módulo de postagem usando o comando da CLI do Angular:

```bash
ng generate module post
```

Após executar o comando com sucesso, serão criados arquivos no seguinte caminho:
`src/app/post/post.module.ts`

### Passo 4: Criar Componentes para o Módulo

Agora adicionaremos novos componentes ao nosso módulo de postagem:

```bash
ng generate component post/index
ng generate component post/view
ng generate component post/create
ng generate component post/edit
```

Após executar os comandos com sucesso, serão criados arquivos nos seguintes caminhos:
- `src/app/post/index/*`
- `src/app/post/view/*`
- `src/app/post/create/*`
- `src/app/post/edit/*`

### Passo 5: Criar Rotas

Atualize o arquivo de rotas `app.routes.ts`:

```typescript
import { Routes } from '@angular/router';
import { IndexComponent } from './post/index/index.component';
import { ViewComponent } from './post/view/view.component';
import { CreateComponent } from './post/create/create.component';
import { EditComponent } from './post/edit/edit.component';

export const routes: Routes = [
  { path: 'post', redirectTo: 'post/index', pathMatch: 'full'},
  { path: 'post/index', component: IndexComponent },
  { path: 'post/:postId/view', component: ViewComponent },
  { path: 'post/create', component: CreateComponent },
  { path: 'post/:postId/edit', component: EditComponent }
];
```

### Passo 6: Criar Interface

Crie a interface para o módulo de postagem:

```bash
ng generate interface post/post
```

**src/app/post/post.ts**
```typescript
export interface Post {
  id: number;
  title: string;
  body: string;
}
```

### Passo 7: Criar Serviço

Criaremos o arquivo de serviço de postagem e implementaremos os métodos de serviço web getAll(), create(), find(), update() e delete().

Usaremos a API do site https://jsonplaceholder.typicode.com

```bash
ng generate service post/post
```

**src/app/post/post.service.ts**
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
  private apiURL = "https://jsonplaceholder.typicode.com";

  httpOptions = {
    headers: new HttpHeaders({
      'Content-Type': 'application/json'
    })
  }

  constructor(private httpClient: HttpClient) { }

  getAll(): Observable<any> {
    return this.httpClient.get(this.apiURL + '/posts/')
      .pipe(
        catchError(this.errorHandler)
      )
  }

  create(post: Post): Observable<any> {
    return this.httpClient.post(this.apiURL + '/posts/', JSON.stringify(post), this.httpOptions)
      .pipe(
        catchError(this.errorHandler)
      )
  }

  find(id: number): Observable<any> {
    return this.httpClient.get(this.apiURL + '/posts/' + id)
      .pipe(
        catchError(this.errorHandler)
      )
  }

  update(id: number, post: Post): Observable<any> {
    return this.httpClient.put(this.apiURL + '/posts/' + id, JSON.stringify(post), this.httpOptions)
      .pipe(
        catchError(this.errorHandler)
      )
  }

  delete(id: number) {
    return this.httpClient.delete(this.apiURL + '/posts/' + id, this.httpOptions)
      .pipe(
        catchError(this.errorHandler)
      )
  }

  errorHandler(error: any) {
    let errorMessage = '';
    if (error.error instanceof ErrorEvent) {
      errorMessage = error.error.message;
    } else {
      errorMessage = `Error Code: ${error.status}\nMessage: ${error.message}`;
    }
    return throwError(errorMessage);
  }
}
```

### Passo 8: Atualizar Lógica e Template do Componente

#### 1) Template e Componente da Página de Lista

**src/app/post/index/index.component.ts**
```typescript
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterModule } from '@angular/router';
import { PostService } from '../post.service';
import { Post } from '../post';

@Component({
  selector: 'app-index',
  standalone: true,
  imports: [CommonModule, RouterModule],
  templateUrl: './index.component.html',
  styleUrl: './index.component.css'
})
export class IndexComponent {
  posts: Post[] = [];

  constructor(public postService: PostService) { }

  ngOnInit(): void {
    this.postService.getAll().subscribe((data: Post[]) => {
      this.posts = data;
      console.log(this.posts);
    })
  }

  deletePost(id: number) {
    this.postService.delete(id).subscribe(res => {
      this.posts = this.posts.filter(item => item.id !== id);
      console.log('Post deletado com sucesso!');
    })
  }
}
```

**src/app/post/index/index.component.html**
```html
<div class="container">
  <h1>Exemplo CRUD Angular 18</h1>
  <a href="#" routerLink="/post/create/" class="btn btn-success">Criar Nova Postagem</a>

  <table class="table table-striped">
    <thead>
      <tr>
        <th>ID</th>
        <th>Título</th>
        <th>Corpo</th>
        <th width="250px">Ação</th>
      </tr>
    </thead>
    <tbody>
      <tr *ngFor="let post of posts">
        <td>{{ post.id }}</td>
        <td>{{ post.title }}</td>
        <td>{{ post.body }}</td>
        <td>
          <a href="#" [routerLink]="['/post/', post.id, 'view']" class="btn btn-info">Visualizar</a>
          <a href="#" [routerLink]="['/post/', post.id, 'edit']" class="btn btn-primary">Editar</a>
          <button type="button" (click)="deletePost(post.id)" class="btn btn-danger">Deletar</button>
        </td>
      </tr>
    </tbody>
  </table>
</div>
```


### Passo 9: Exportar provideHttpClient()

**src/app/app.config.ts**
```typescript
import { ApplicationConfig } from '@angular/core';
import { provideRouter } from '@angular/router';
import { routes } from './app.routes';
import { provideAnimations } from '@angular/platform-browser/animations';
import { provideHttpClient } from '@angular/common/http';

export const appConfig: ApplicationConfig = {
  providers: [provideRouter(routes), provideAnimations(), provideHttpClient()]
};
```

### Executar Aplicativo Angular

Execute o aplicativo com o comando:

```bash
ng serve
```

Abra no navegador:
http://localhost:4200/post

*Link do Repositório: [https://github.com/savanihd/Angular-18-CRUD-Application-Tutorial-Example](https://github.com/savanihd/Angular-18-CRUD-Application-Tutorial-Example)*

*Link do Repositório: [https://github.com/savanihd/Angular-18-CRUD-Application-Tutorial-Example](https://github.com/savanihd/Angular-18-CRUD-Application-Tutorial-Example)*
