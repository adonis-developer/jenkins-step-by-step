<p align="center">
  <a href="http://nestjs.com/" target="blank"><img src="https://nestjs.com/img/logo-small.svg" width="120" alt="Nest Logo" /></a>
</p>

[circleci-image]: https://img.shields.io/circleci/build/github/nestjs/nest/master?token=abc123def456
[circleci-url]: https://circleci.com/gh/nestjs/nest

  <p align="center">A progressive <a href="http://nodejs.org" target="_blank">Node.js</a> framework for building efficient and scalable server-side applications.</p>
    <p align="center">
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/v/@nestjs/core.svg" alt="NPM Version" /></a>
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/l/@nestjs/core.svg" alt="Package License" /></a>
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/dm/@nestjs/common.svg" alt="NPM Downloads" /></a>
<a href="https://circleci.com/gh/nestjs/nest" target="_blank"><img src="https://img.shields.io/circleci/build/github/nestjs/nest/master" alt="CircleCI" /></a>
<a href="https://coveralls.io/github/nestjs/nest?branch=master" target="_blank"><img src="https://coveralls.io/repos/github/nestjs/nest/badge.svg?branch=master#9" alt="Coverage" /></a>
<a href="https://discord.gg/G7Qnnhy" target="_blank"><img src="https://img.shields.io/badge/discord-online-brightgreen.svg" alt="Discord"/></a>
<a href="https://opencollective.com/nest#backer" target="_blank"><img src="https://opencollective.com/nest/backers/badge.svg" alt="Backers on Open Collective" /></a>
<a href="https://opencollective.com/nest#sponsor" target="_blank"><img src="https://opencollective.com/nest/sponsors/badge.svg" alt="Sponsors on Open Collective" /></a>
</p>
  <!--[![Backers on Open Collective](https://opencollective.com/nest/backers/badge.svg)](https://opencollective.com/nest#backer)
  [![Sponsors on Open Collective](https://opencollective.com/nest/sponsors/badge.svg)](https://opencollective.com/nest#sponsor)-->

## Description

<!-- [Nest](https://github.com/nestjs/nest) framework TypeScript starter repository. -->

Đây là 1 dự án triển khai cơ bản của 1 mono repo của nestjs:

1. Triển khai các pattern:

   - Repository Pattern -> di chuyển linh hoạt giữa MongoDB vs Postgres or ..., abstract các function chung như find, create, save, bulkSave, update...

2. Triển khai sẵn migration cho mono repo

## Project setup

```bash
$ npm install
```

## Compile and run the project

```bash
# watch mode
$ npm run start:dev $service
$ npm run start:dev auth-svc

# production mode
$ npm run start:prod $service
$ npm run start:prod auth-svc
```

## Run migration DB

```bash
# Create migration by entity
$  npm run build && npx typeorm migration:generate -d dist/apps/$service/src/configs/data-source.js ./apps/$service/src/migrations/InitEntity
$  npm run build && npx typeorm migration:generate -d dist/apps/auth-svc/src/configs/data-source.js ./apps/auth-svc/src/migrations/InitEntity

# Apply migration by entity
$  npm run build && npx typeorm migration:run -d dist/apps/$service/src/configs/data-source.js
$  npm run build && npx typeorm migration:run -d dist/apps/auth-svc/src/configs/data-source.js

# Revert migration by entity
$  npm run build && npx typeorm migration:revert -d dist/apps/$service/src/configs/data-source.js
$  npm run build && npx typeorm migration:revert -d dist/apps/auth-svc/src/configs/data-source.js
```

## Generate resource in monorepo

```bash
$  nest g res $name $path
$  nest g app identity /modules
$  nest g lib identity /modules
```

## License

MIT licensed
