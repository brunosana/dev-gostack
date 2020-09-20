# Criando Relacionamento

Para esse exemplo, o `type` do `id` foi definido como `uuid`. E o `name` do `provider` foi renomeado para `provider_id`. Todos os IDs agora possuem o `type` = `uuid`.

Para criar um relacionamento (Docker + TypeORM), precisamos criar uma nova migration. Para esse exemplo, usaremos a relação entre Appointments e Users(chamado de provider). Começamos com `yarn typeorm migration:create -n CreateForeignKeyAppointmentsProvider`. A função `UP` ficará:

```typescript
await queryRunner.createForeignKey(
    'appointments',
    new TableForeignKey({
        name: 'AppointmentProvider',
        columnNames: ['provider_id'],
        referencedColumnNames: ['id'],
        referencedTableName: 'users',
        onDelete: 'SET NULL',
        onUpdate: 'CASCADE',
    }),
);
```

* Primeiro parâmetro do método `createForeignKey` é o nome da tabela que irá receber a relação, o segunto é a chave em si.
* name -> Nome da chave (importante para o método `down`).
* columnNames -> Colunas que irão ser relacionadas na tabela.
* referencedColumnNames -> Nome da coluna que irá ser referenciada na tabela estrangeira.
* referencedName -> Nome da tabela users.
* onDelete -> Ao ser excluído o registro estrangeiro, ele irá setar todos os campos com o ID do User que foi excluído como `NULL`.
* onUpdate -> Ao Id ser alterado no registro estrangeiro, todos os registros na tabela `appointments` serão alterados para o novo ID.

No método `down`:

```typescript
await queryRunner.dropTable('appointments');
```

E o arquivo `Appointments.ts`, além de importar o User com `import User from './User';`:

```javascript
@Column()
provider_id: string;

@ManyToOne(() => User)
@JoinColumn({ name: 'provider_id' })
provider: User;
```

* O campo provider agora se trata de um User, que será capturado com os dois decorators acima.
* O `ManyToOne` significa o tipo de relacionamento do `Appointment`, onde vários appointments podem ser registrados para um usuário, enviamos uma função onde capturamos o User.
* Usamos o JoinColumn passando a propriedade name como o campo onde terá o ID do usuário registrado no appointment.