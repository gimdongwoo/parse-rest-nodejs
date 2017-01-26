Parse Rest NodeJS
=================

[![npm version](https://badge.fury.io/js/parse-rest-nodejs.svg)](https://badge.fury.io/js/parse-rest-nodejs)

Install
-------

```
npm install --save parse-rest-nodejs
```

How to Use
----------

It's used as the basis of [Parse Rest API](http://parseplatform.github.io/docs/rest/guide/).

### Setup up process.env

```javascript
// Recommend to use 'better-npm-run'.
process.env.SERVER_URL = "http://__host__/parse"
process.env.APP_ID = "__app_id__";
process.env.MASTER_KEY = "__master_key__";
```

### Methods
'get', 'post', 'put', 'patch', 'del'

* parameter: (url, data, headers, formData)

* All methods are returned Promise.

```javascript
parseRest.get('/classes/__className__/').then((success) => {
  console.log(success);
}, (error) => {
  console.error(error);
});
```

### Initialize

```javascript
// es6
import ParseRest from 'parse-rest-nodejs';

// req is express request
const parseRest = new ParseRest(req);
```

### insert an object

```javascript
parseRest.post('/classes/__className__', { ... })
```

### insert a User

```javascript
parseRest.post('/users', { ... })
```

### insert with a Pointer

```javascript
const user = { __type: 'Pointer', className: '_User', objectId }
parseRest.post('/classes/__className__', {..., user})
```

### insert with GeoPoints

```javascript
const location = { __type: 'GeoPoint', latitude, longitude };
parseRest.post('/classes/__className__', {..., location})
```

### user login

```javascript
parseRest.get('/login', { username, password }, { 'X-Parse-Revocable-Session': 1 })
```

### me

* It need to 'sessionToken' value in request header.
* Alternatively, 'sessionToken' or 'user.sessionToken' value in request session is possible.

```javascript
parseRest.get('/users/me')
```

### insert a file

* Use the property 'fileData'.
* It is recommended to use [multer](https://github.com/expressjs/multer).

```javascript
// Express side
import express from 'express';
import multer from 'multer';

const app = express();
const upload = multer({ storage: multer.diskStorage({}) });
...
// file upload
app.post('/file/upload', upload.array('files'), function (req, res, next) {
  // console.log('upload complete / req.files :', req.files);
  return next();
});
...
```

```javascript
// Action side
import fs from 'fs';

const _file = req.files[0];
const _fileInfo = { filename: _file.originalname, mimetype: _file.mimetype, size: _file.size, encoding: _file.encoding };
const fileData = { file: fs.createReadStream(_file.path), mimetype: _file.mimetype };

parseRest.post('/files/' + _file.filename, { fileData }).then((_fileResult) => {
  console.log('file result:', _fileResult);
  // Store where you need it
  parseRest.post('/classes/__className__', { ..._fileResult, ..._fileInfo })
});
```

### get object

```javascript
parseRest.get('/classes/Files', { objectId })
```

### get list

```javascript
parseRest.get('/classes/Files', { where: { ... }, limit, skip, order, ... })
```

### get user

```javascript
parseRest.get('/users', { objectId }, { useMasterKey: true })
```

### get users

```javascript
parseRest.get('/users', { where: { ... }, limit, skip, order, ... }, { useMasterKey: true })
```

### count of objects

```javascript
parseRest.get('/users', { limit: 0, count: 1 }, { useMasterKey: true });
```

### update an object

```javascript
parseRest.put('/classes/__className__/' + objectId, { ... })
```

### delete an object

```javascript
parseRest.del('/classes/__className__', { objectId })
```

### delete user

```javascript
parseRest.del('/users', { objectId }, { useMasterKey: true })
```

### edit user

```javascript
parseRest.put('/users/' + objectId, { ... }, { useMasterKey: true })
```

### insert installation data

```javascript
parseRest.post('/installations', { deviceToken, ... })
```

### run cloud function

```javascript
parseRest.post('/functions/__functionName__', { ... })
```
