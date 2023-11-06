**Объектно-Реляционное Отображение (Object-Relational Mapping)** - технология, связывающая модель базы данных и концепции ООП.

Позволяет работать с данными как с объектами.

---
### Преимущества ORM

- Представление модели данных в ORM независимо от СУБД  
- Упрощенное моделирование базы данных
- ORM предоставляет больше механизмов обеспечения целостности данных
- Возможность использовать наследование моделей

---
### Недостатки ORM

- Медленее, чем “голый” SQL 
- Возможны проблемы с комплексными запросами
- Идея всех ORM схожа, но реализации различны: нужно дополнительное обучение

---
### Пример модели Sequelize ORM

```js
const { Model, Sequelize } = require('sequelize');  
  
module.exports = (sequelize, DataTypes) =>  
{  
    class Role extends Model  
    {  
        static associate({ User })  
        {  
            Role.hasMany(User, {  
                sourceKey: 'id',  
                foreignKey: 'roleId',  
            });  
        }  
    }  
    Role.init({  
        id: {  
            type: DataTypes.UUID,  
            defaultValue: Sequelize.UUIDV4,  
            primaryKey: true,  
            allowNull: false,  
        },  
        role: {  
            type: DataTypes.STRING(10),  
            unique: true,  
        },  
    }, {  
        sequelize,  
        modelName: 'Role',  
        tableName: 'roles',  
        timestamps: false,  
        underscored: true,  
    });  
  
    return Role;  
};
```

---
### Миграции

- Упрощают работу со схемой базы
- Позволяют быстро переключаться между разными вариантами схемы (в том числе откатывать изменения) или отслеживать историю версий (своего рода *GIT для БД*)

---
### Пример миграции (node-pg-migrate)

```js
exports.up = (pgm) =>  
{  
    pgm.createExtension("uuid-ossp", {  
        ifNotExists: true  
    });  
    /** Роли, профили пользователей */    
    pgm.createTable('roles', {  
        id: {  
            type: "uuid",  
            default: new PgLiteral("uuid_generate_v4()"),  
            notNull: true,  
            primaryKey: true  
        },  
        role: {  
            type: 'varchar(10)',  
            notNull: true,  
        },  
        created_at: {  
            type: 'timestamp',  
            default: pgm.func('NOW()'),  
            notNull: true,  
        },  
    });
}
```

```sh
npm run migrate up
```

---
