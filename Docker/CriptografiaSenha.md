# Criptografia de Senha

Começamos com `yarn add bcryptjs` e `yarn add @types/bcryptjs -D`.

No `CreateUserService` importamos a função `hash` com `import { hash } from 'bcryptjs';`, e antes de inserir a senha no banco, usamos:

```javascript
const hashedPassword = await hash(password, 8);
```

Passamos então o `hashedPassword` no campo password do User.

Por boas práticas de programação, não informamos o password do usuário ao ser retornado, para isso, em `users.routes.ts`, após capturar o usuário criado, usamos `delete user.password` para excluir a informação.