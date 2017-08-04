# knorm-soft-delete

[![npm version](https://badge.fury.io/js/knorm-soft-delete.svg)](http://badge.fury.io/js/knorm-soft-delete)
[![build status](https://travis-ci.org/joelmukuthu/knorm-soft-delete.svg?branch=master)](https://travis-ci.org/joelmukuthu/knorm-soft-delete)
[![coverage status](https://coveralls.io/repos/github/joelmukuthu/knorm-soft-delete/badge.svg?branch=master)](https://coveralls.io/github/joelmukuthu/knorm-soft-delete?branch=master)
[![dependency status](https://david-dm.org/joelmukuthu/knorm-soft-delete.svg)](https://david-dm.org/joelmukuthu/kknorm-soft-delete)
[![Greenkeeper badge](https://badges.greenkeeper.io/joelmukuthu/knorm-soft-delete.svg)](https://greenkeeper.io/)

Soft-delete plugin for [knorm](https://www.npmjs.com/package/knorm). It exports
an ES6 class mixin that adds support for soft-deleting rows in the database.

This plugin adds `deleted` and `deletedAt` fields to your models (depending on
the config options) and also updates your query methods so that the `delete`
method does not actually delete a row but rather updates the `deleted` flag. It
also adds a default `where deleted = false` clause to all `fetch`, `update` and
`delete` calls (which can also be overriden if you want to query deleted rows).

## Installation
```bash
npm install --save knorm knorm-soft-delete
```
> knorm-soft-delete has a peer dependency on [knorm](https://www.npmjs.com/package/knorm)

## Usage
### 1. Enhance knorm's Query class

```js
const { Query: KnormQuery } = require('knorm');
const withSoftDelete = require('knorm-soft-delete');

class Query extends withSoftDelete(KnormQuery) {}
```

### 2. Enhance knorm's Model class

Then configure your ORM with the knex instance:

```js
const { Model: KnormModel } = require('knorm');

const options = { deleted: true, deletedAt: true };
class Model extends withSoftDelete(KnormModel, options) {}
Model.Query = Query; // configure Model with the enhanced Query class
```

## Options

### deleted

Can either be `true` or an object for further configuration. If `true`, it adds
a field to the `Model` class with this config:
- field-name: `deleted`
- column-name: `deleted`

If passed as an object, supports these config options:
- `name` *string, default: deleted*: the field name to use instead of
  `deleted`
- `type` *string, default: boolean*: the field type to use instead of
  `boolean`
- `column` *string, default: deleted*: the column name to use instead of
  `deleted`
- `default` *mixed, default: false*: the default value to use for the field.
  this can also be a function.

### deletedAt

Can either be `true` or an object for further configuration. If `true`, it adds
a field to the `Model` class with this config:
- field-name: `deletedAt`
- column-name: `deleted_at`

If passed as an object, supports these config options:
- `name` *string, default: deletedAt*: the field name to use instead of
  `deletedAt`
- `type` *string, default: dateTime*: the field type to use instead of
  `dateTime`
- `column` *string, default: deleted_at*: the column name to use instead of
  `deleted_at`

This field doesn't support a default value, it's value will be set to the
current timestamp (i.e. `new Date()`) when soft-deleting.