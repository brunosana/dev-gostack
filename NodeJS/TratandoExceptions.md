# Tratando Exceções com NodeJS

Precisamos criar nossa própria classe de erros. Para isso, criamos uma pasta `src/errors` e criar a primeira class de Error `AppError.ts`:

```typescript
class AppError {
    public readonly message: string;

    public readonly statusCode: number;

    constructor(message: string, statusCode = 400) {
        this.message = message;
        this.statusCode = statusCode;
    }
}

export default AppError;
```

A vantagem de usar uma classe de erro é que ela é customizável. Agora, importamos o AppError com `import AppError from '../errors/AppError';` em todos os services, e onde tiver o trecho `throw new Error` podemos alterar para `throw new AppError(message, statusCode)`. Agora podemos personalizar o statusCode.

## Global Exception Handler

Ao invés de usar try catch em várias rotas, podemos criar um middleware que capta todos os erros da aplicação independente de onde eles vieram.

Para isso, podemos retirar todas os blocos `try{}catch(err){}` das rotas.

Agora, em `server.ts` **depois das rotas**:

```typescript
app.use(
    (err: Error, request: Request, response: Response, next: NextFunction) => {
        if (err instanceof AppError) {
            return response.status(err.statusCode).json({
                status: 'Error',
                message: err.message,
            });
        }

        console.error(err.message);

        return response.status(500).json({
            status: 'Error',
            message: 'Internal server error',
        });
    },
);
```

* Os middlewares para tratativa de erros são obrigados a receber 4 parâmetros, o primeiro sendo o `err` para o error, e os próximos iguais aos outros middlewares.
* Fazemos então uma verificação para saber se é uma instância da classe criada por nós, pois se for, será um erro lançado pela nossa aplicação, ou seja, um erro conhecido, esperado e tratado.
* Caso não, retornamos um erro genérico

Porém, o express não consegue captar erros em rotas `async`.

Para isso, precisamos instalar uma dependência com `yarn add express-async-errors` e fazermos a importação dele em `server.ts` logo abaixo da importação do express.

O parâmetro `next` irá ficar sublinhado pois não está sendo utilizado. Para isso, podemos substituir `next` por `_`, pois esse será o caractere que o eslint vai ler e considerar que ele não será utilizado.

Então, no `.eslintrc.json` inserimos uma nova `rule`:

```JSON
"@typescript-eslint/no-unused-vars": ["error", {
    "argsIgnorePattern": "_"
}]
```