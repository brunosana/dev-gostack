# Autenticação JWT + NodeJS

### Tópicos

* Conceito JWT
* Validando credenciais
* Gerando Token JWT
* Rotas autenticadas

## JWT

> JWT (JSON Web Token) é uma metodologia de autenticação para APIs REST (comunicação através de JSON). Ou seja, é diferente de uma aplicação MVC. É um Token em formato de JSON.

Ex:

Em uma aplicação, para criar uma sessão, temos uma rota `/sessions` e enviamos no corpo da requisição o `email` e `password`. Caso haja o "match" do email e password corretamente, será gerado um Token JWT, gerado a partir de uma biblioteca.

O token normalmente vem com uma sintaxe semelhante a `<characters>.<characters>.<characters>`. Possui 3 partes que são separadas por pontos.

1. A primeira parte contém o **tipo de token gerado**, pois existem vários algoritmos de geração de token
2. A segunda parte contém o **Payload**, que são informações adicionais que podemos incluir no Token (como ID do usuário por exemplo)
3. A terceira parte contém a **Assinatura**, é o que garante que o token não foi modificado externamente por outra pessoa, é o que evita fraudes.

## Validando credenciais

Para validar uma credencial, primeiro precisamos criar o arquivo `routes/sessions.routes.ts` para ter o controle de criar/alterar/excluir sessões. Também precisamos de um service `services/AuthenticateUserService.ts` para executar toda a regra de negócio da aplicação.

No AuthenticateUserService:

```typescript
import { getRepository } from 'typeorm';
import { compare } from 'bcryptjs';
import User from '../models/User';

interface Request {
    email: string;
    password: string;
}

interface Response {
    user: User;
}

class AuthenticateUserService {
    public async execute({ email, password }: Request): Promise<Response> {
        const userRepository = getRepository(User);

        const user = await userRepository.findOne({
            where: { email },
        });

        if (!user) {
            throw Error('Incorrect email/password combination.');
        }

        const passwordMached = await compare(password, user.password);

        if (!passwordMached) {
            throw Error('Incorrect email/password combination.');
        }

        return {
            user,
        };
    }
}

export default AuthenticateUserService;
```

* Capturamos o repositório do User com o método `getRepository` (Não usamos o `getCustomRepository` pois não temos um repoistory personalizado para ele, pois não contém funções específicas como a de `appointments`).
* Verificamos se o email existe e retornamos um erro caso não.
* Comparamos o password com o método `compare` da lib `bcrypt.js` que recebe uma string não criptografada e uma criptografada e retorna `true` caso sejam compativeis. Caso não, um erro é lançado.

Em `sessions.routes.ts` nosso método POST ficará:

```typescript
import { Router } from 'express';
import AuthenticateUserService from '../services/AuthenticateUserService';

const sessionsRouter = Router();

sessionsRouter.post('/', async (request, response) => {
    try {
        const { email, password } = request.body;

        const authenticateUser = new AuthenticateUserService();
        const { user } = await authenticateUser.execute({
            email,
            password,
        });

        delete user.password;

        return response.json(user);
    } catch (err) {
        return response.status(400).json({ error: err.message });
    }
});

export default sessionsRouter;
```

* Importamos e instanciamos o `AuthenticateUserService`.
* Usamos o método execute passando o email e password.
* deletamos o password para não fazer parte do retorno e enviamos o user na response

## Gerando Token JWT

Para gerar tokens JWT precisamos instalar uma lib com `yarn add jsonwebtoken` e `yarn add @types/jsonwebtoken -D`. Logo em seguida, importamos ela no `AuthenticateUserService` com `import { sign } from 'jsonwebtoken';`.

Antes do return, definimos uma variável `token` como sendo:

```typescript
const token = sign({}, '82e3fe08095c73083f0ba280321a12e6', {
    subject: user.id,
    expiresIn: '1d',
});
```

* O primeiro parâmetro dela é o `Payload`. Não podemos colocar informações sensíveis pois é possível descriptografar e descobrir a info.
* O segundo parâmetro é uma chave secreta, dentre suas várias formas, podemos passar como string ([gerar hash para usar na senha](http://www.md5.cz/)).
* O terceiro parâmetro são as configurações do Token, que são várias
  * O `subject` será o ID do usuário, sempre!
  * `expiresIn` é quanto tempo o token irá durar, no caso, 1 dia.
* retornamos o token no service (modificando a interface Response).

Também precisamos capturar o token na rota e retorná-lo:

```typescript
const { user, token } = await authenticateUser.execute({
    email,
    password,
});

delete user.password;

return response.json({ user, token });
```

# Rotas autenticadas

Para enviar o token junto a requisição, precisamos enviá-lo no cabeçalho junto com o prefixo `Bearer ` que é inserido por padrão. No Insomnia temos uma aba específica para isso, abaixo no endereço da rota, na aba `Auth`, podemos mudar para Bearer e colar o token lá. Ou melhor, nas opções de enviroment criamos uma variável token e inserimos direto nela.

Para criarmos rotas autenticadas precisamos criar um middleware de verificação, por isso, criamos um arquivo `src/middlewares/ensureAuthenticated.ts`. Para isso, criamos um arquivo em `src/config/auth.ts` contendo:

```typescript
export default {
    jwt: {
        secret: '82e3fe08095c73083f0ba280321a12e6',
        expiresIn: '1d',
    },
};
```
Esse secret é o mesmo que usamos ao gerar o token, o expiresInd também, isso foi feito para evitar reescrita de informações. Portanto, tanto no `AuthenticateUserService` quanto no `ensureAuthenticated` usaremos esse arquivo!

Portanto em `ensureAuthenticated`, temos:

```typescript
import { Request, Response, NextFunction } from 'express';
import { verify } from 'jsonwebtoken';
import authConfig from '../config/auth';

interface TokenPayload {
    iat: number;
    exp: number;
    sub: string;
}

export default function ensureAuthenticated(
    request: Request,
    response: Response,
    next: NextFunction,
): void {
    const authHeader = request.headers.authorization;
    if (!authHeader) {
        throw new Error('jwt token is missing');
    }
    const [, token] = authHeader.split(' ');

    try {
        const decoded = verify(token, authConfig.jwt.secret);

        const { sub } = decoded as TokenPayload;

        request.user = {
            id: sub,
        };

        return next();
    } catch {
        throw new Error('Invalid JWT token');
    }
}

```

* A tipagem foi feita com a importação das interfaces do Express.
* Capturamos o token dentro do Headers e retornamos um erro caso este não seja enviado
* Desestruturamos o token (por causa do prefixo `Bearer `) e pegamos apenas o Token
* Usamos uma função `verify` de `jsonwebtoken` passando o token e o secret para checar a validade do token
* Caso tenha algum erro este é enviado no Catch

Em `request.user`, precisamos fazer uma alteração na interface `request`, pois não há o parâmetro user. Porém, como precisamos enviá-lo (capturamos o parâmetro `sub` da variável `decoded` que contém o id do usuário) nós sobrescrevemos a interface.

Para sobrescrever uma interface, criamos uma arquivo `src/@types/express.d.ts`:

```typescript
declare namespace Express {
    export interface Request {
        user: {
            id: string;
        };
    }
}
```

Com isso, inserimos o parâmetro `user` dentro de `request`.

Agora, o arquvio `appointments.routes.ts`, além de importar o middleware criado com `import ensureAuthenticated from '../middlewares/ensureAuthenticated';`, inserimos antes de todas as rotas:

```typescript
appointmentsRouter.use(ensureAuthenticated);
```