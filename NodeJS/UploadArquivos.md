# Upload de Arquivos NodeJS

Vamos considerar que na tabela `Users` existe um campo `avatar` do tipo `varchar`.

Para podermos fazer o upload de arquivos com o Express utilizando SQL, usamos uma lib chamada _multer_, então, vamos instalá-la com `yarn add multer` e `yarn add @types/multer -D`.

Criamos então um middleware `src/middewares/upload.ts`:

```typescript
import multer from 'multer';
import path from 'path';
import crypto from 'crypto';

const tmpFolder = path.resolve(__dirname, '..', '..', 'tmp');

export default {
    directory: tmpFolder,

    storage: multer.diskStorage({
        destination: tmpFolder,
        filename(request, file, callback) {
            const fileHash = crypto.randomBytes(10).toString('hex');
            const fileName = `${fileHash}-${file.originalname}`;
            return callback(null, fileName);
        },
    }),
};
```

* Criamos uma pasta `tmp` fora da raiz do projeto para armazenar os uploads, pois conforme a aplicação cresce será necessário utilizar serviços específicos para armazenar arquivos.
* Por hora usamos o storage como `multer.diskStorage` passando o destination e o filename
* Usamos o módulo `path` pois precisamos garantir que irá rodar em todos os SOs compatíveis.
* Criamos uma variável `tmpFolder` pois vamos usar ela em dois lugares, no directory e no destination
* o directory será acessível no nosso service para alterar a imagem do avatar
* Usamos o módulo `crypto` para gerar uma sequência aleatória de caracteres e garantir que os arquivos não sejam inseridos com o mesmo nome.
* Atribuimos os caracteres gerados com o nome do arquivo.
* retornamos o seu nome no `callback`, o primeiro parâmetro sendo o erro, como não tivemos erro, passamos como `Null`.

Criamos uma rota em `users.routes.ts` chamada `/avatar` do tipo `PATCH`, além de importar o `multer` e o `middleware criado`.

```typescript
usersRoutes.patch(
    '/avatar',
    ensureAuthenticated,
    upload.single('avatar'),
    async (request, response) => {
        try {
            const updateUserAvatarService = new UpdateUserAvatarService();

            const user = await updateUserAvatarService.execute({
                user_id: request.user.id,
                avatarFilename: request.file.filename,
            });

            delete user.password;

            return response.json(user);
        } catch (err) {
            return response.status(400).json({ error: err.message });
        }
    },
);
```

* Instanciamos o multer a partir das configurações criadas no middleware
* Temos métodos como `single` (para upload de apenas um arquivo) e `array` (upload de vários arquivos). Tem o `any` (tanto um quanto vários, qualquer quantidade)
* Inserimos o middleware logo após o middleware de autenticação com o método `upload.single('avatar')` contendo o nome do campo que conterá a imagem quando a rota for chamada.
* PS: O service que estamos utilizando acima serve para alterar a imagem caso exista, passando como parãmetro o ID do usuário e o nome do arquivo salvo.

Para testar no Insomnia, no corpo da requisição, ao invés de JSON, usamos o tipo `Multipart Form`, escolhendo o tipo como sendo `file` e o nome `avatar`

## Atualizando o avatar

Até então estamos inserindo as imagens corretamente, porém, precisamos linkar ela com o user no banco de dados, assim como precisamos atualizar toda vez que o mesmo usuário usar a rota para alterar o seu próprio avatar, removendo o arquivo existente caso exista, e inserindo o nome do outro no banco.

Ok, para isso, criamos um service `UpdateUserAvatarService`:

```typescript
import { getRepository } from 'typeorm';
import path from 'path';
import fs from 'fs';
import User from '../models/User';

import uploadConfig from '../middlewares/upload';

interface Request {
    user_id: string;
    avatarFilename: string;
}

class UpdateUserAvatarService {
    public async execute({ user_id, avatarFilename }: Request): Promise<User> {
        const userRepository = getRepository(User);

        const user = await userRepository.findOne({
            where: { id: user_id },
        });

        if (!user) {
            throw new Error('Only authenticated users can change avatar');
        }

        if (user.avatar) {
            const userAvatarFilePath = path.join(
                uploadConfig.directory,
                user.avatar,
            );

            const userAvatarFileExists = await fs.promises.stat(
                userAvatarFilePath,
            );

            if (userAvatarFileExists) {
                await fs.promises.unlink(userAvatarFilePath);
            }
        }
        user.avatar = avatarFilename;
        await userRepository.save(user);
        return user;
    }
}

export default UpdateUserAvatarService;
```

* O service recebe o ID do usuário (que inserimos no middleware de autenticação) e do nome do arquivo que fizemos o upload.
* Capturamos o repository dos usuários
* Capturamos o usuário pelo ID
* Verificamos se o campo `avatar` contém algum conteúdo (ou seja, se temos uma imagem no banco associada a ele).
* Caso um avatar do usuário já esteja no banco, nós buscamos o arquivo com o `path.join`, passando o diretório da pasta e o nome do arquivo
  * Verificamos o status do arquivo (retornará true se existir)
  * Removemos ele do banco
* no final, indepentende de ter excluído ou não a imagem, nós inserimos o nome da nova imagem no registro do usuário, salvamos e retornamos o usuário

## Rotas de arquivos estáticos

Vamos criar uma rota para mostrar todos os avatars de forma estática.

Em `server.ts`,  antes do `app.use(routes);`, inserimos:

```typescript
app.use('/files', express.static(uploadConfig.directory));
```

* express.static serve uma pasta de forma estática
* informamaos o diretório de acordo com o diretório do middleware de uploads

Rodando `yarn dev:server` conseguimos acessar pelo browser com a url `localhost:3333/files/<nomedaimagem>` para acessarmos o diretório.