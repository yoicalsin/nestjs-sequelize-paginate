# NestJs Sequelize Paginate

Pagination helper method for Sequelize models.

<a href="https://github.com/yonycalsin/nestjs-sequelize-paginate"><img src="https://img.shields.io/spiget/stars/1000?color=brightgreen&label=Star&logo=github" /></a>
<a href="https://www.npmjs.com/nestjs-sequelize-paginate" target="_blank">
<img src="https://img.shields.io/npm/v/nestjs-sequelize-paginate" alt="NPM Version" /></a>
<a href="https://www.npmjs.com/nestjs-sequelize-paginate" target="_blank">
<img src="https://img.shields.io/npm/l/nestjs-sequelize-paginate" alt="Package License" /></a>
<a href="https://www.npmjs.com/nestjs-sequelize-paginate" target="_blank">
<img src="https://img.shields.io/npm/dm/nestjs-sequelize-paginate" alt="NPM Downloads" /></a>
<a href="https://github.com/yonycalsin/nestjs-sequelize-paginate" target="_blank">
<img src="https://s3.amazonaws.com/assets.coveralls.io/badges/coveralls_95.svg" alt="Coverage" /></a>
<a href="https://github.com/yonycalsin/nestjs-sequelize-paginate"><img src="https://img.shields.io/badge/Github%20Page-nestjs.sequelize.paginate-yellow?style=flat-square&logo=github" /></a>

## Description

Under the hood, nestjs-sequelize-paginate makes use of the [nest framework](https://nestjs.com/), and you also need to install [nestjs](https://nestjs.com/), and [sequelize](https://docs.nestjs.com/techniques/database#sequelize-integration) !

## Integration

To start using it, we first install the required dependencies. In this chapter we will demonstrate the use of the paginate for nestjs.

You simply need to install the package !

```ts
// We install with npm, but you could use the package manager you prefer !
npm install -save nestjs-sequelize-paginate
```

## Getting started

Once the installation process is complete, we can import the **PaginateModule** into the root **AppModule**

```ts
import { Module } from '@nestjs/common'
import { PaginateModule } from 'nestjs-sequelize-paginate'

@Module({
  imports: [
    PaginateModule.forRoot({
      url: 'http://localhost:3000',
    }),
  ],
})
export class AppModule {}
```

The **forRoot()** method supports all the configuration properties exposed by the paginate constuctor . In addition, there are several extra configuration properties described below.

| Name          | Description                                       | Type                      | Default    |
| ------------- | ------------------------------------------------- | ------------------------- | ---------- |
| url           | If you want a global url                          | _string_                  | `null`     |
| isGlobal      | If you want the module globally                   | _boolean_                 | `true`     |
| showUrl       | If you want the url to be shown in the results    | _boolean_                 | `false`    |
| structure     | Una forma de estructura de respuesta              | 'simple' \| 'segmented'   | `simple`   |
| details       | Una forma de respuesta                            | 'necessary' \| 'complete' | `complete` |
| defaultPage   | Numeros de pagina por defecto globalmente         | _number_                  | `1`        |
| defaultOffset | Numeros de cantidad por pagina globalmente        | _number_                  | `5`        |
| showOffset    | Si quere offset se muestre en las url globalmente | _boolean_                 | `false`    |

### Service

Sequelize implements the Active Record pattern. With this pattern, you use model classes directly to interact with the database. To continue the example, we need at least one model. Let's define the User Model.

```ts
import { Injectable } from '@nestjs/common'
import { PaginateService, PaginateOptions } from 'nestjs-sequelize-paginate'
import { ModelUser } from 'src/models/user.model'

@Injectable()
export class UserService {
  constructor(private paginateService: PaginateService) {}
  async findAll(options: PaginateOptions): Promise<any> {
    const paginate = this.paginateService.findAllPaginate({
      ...options,
      model: ModelUser,
      path: '/user',
    })
    return paginate
  }
}
```

Next, let's look at the **UserModule:**

```ts
import { Controller, Get, Res, HttpStatus } from '@nestjs/common'
import { UserService } from './user.service'
import { Response } from 'express'
import { PaginateQueryInterface, PaginateQuery } from 'nestjs-sequelize-paginate'

@Controller('user')
export class UserController {
  constructor(private readonly userService: UserService) {}

  @Get()
  async getUsers(@Res() res: Response, @PaginateQuery('all') paginateQuery: PaginateQueryInterface): Promise<any> {
    const data = await this.userService.findAll(paginateQuery)
    res.status(HttpStatus.OK).send(data)
  }
}
```

### Decorator

As you saw, we're using a decorator, '@PaginateQuery'.
The decorator receives only one option as a parameter, which is `all`, this allows to add the offset through the url !

This decorator returns the following to you !

```ts
{
   path: '/user',
   page: 2, // http://localhost:3000/user?page=2
   offset: 10, // http://localhost:3000/user?page=2&offset=10
   showOffset: true // if you add 'all' as a parameter
}
```

## License

Sass-colors is [MIT licensed](LICENSE).
