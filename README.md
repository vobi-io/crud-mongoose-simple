crud-mongoose-simple
====================

#### Forked from our core team member [@Chkhikvadze](https://github.com/Chkhikvadze/crud-mongoose-simple)

[![Build Status](https://travis-ci.org/Chkhikvadze/crud-mongoose-simple.svg?branch=master)](https://github.com/Chkhikvadze/crud-mongoose-simple)


Simple List, Create, Read, Update and Delete requests for a given Mongoose model.
Create Express Route easily.

## Install

```bash
$ npm install crud-mongoose-simple
```

### Plugin Static Functions
* Model.httpGet(req, res);
* Model.httpPost(req, res);
* Model.httpPut(req, res);
* Model.httpDelete(req, res);
* Model.countItems(req, res);
* Model.totalPages(req, res);
* Model.registerRouter(req, res);

## Server Setup With Manual Route

```js

var express = require('express');
var router = express.Router();


var mongoose = require('mongoose');
var crud = require('crud-mongoose-simple');
mongoose.plugin(crud);

var personSchema = new mongoose.Schema({
	name: {
		first: String,
		last: String
	},
	age : Number,
	accupation : String,
	likes : []
});

var personModel = mongoose.model('Person', personSchema);

router.route('/person/list').get(personModel.httpGet()) // Get all items by filter
router.route('/person/').post(personModel.httpPost()); // Create new Item

router.route('/person/:id')
	.get(personModel.httpGet()) // Get Item by Id
	.put(personModel.httpPut()) // Update an Item with a given Id
	.delete(personModel.httpDelete()); // Delete and Item by Id
```

## Server Setup With Auto Route

```js

var express = require('express');
var router = express.Router();

var mongoose = require('mongoose');
var crud = require('crud-mongoose-simple');
mongoose.plugin(crud);

var personSchema = new mongoose.Schema({
	name: String
});

var personModel = mongoose.model('Person', personSchema);

personModel.registerRouter(router, '/api/v1/');

/**
 * It get routes:
 * GET - http://localhost:3000/api/v1/{modelName}/list  - Get all items by filter
 * POST - http://localhost:3000/api/v1/{modelName}/ - Create new Item
 * PUT - http://localhost:3000/api/v1/{modelName}/:id - Update an Item with a given Id
 * DELETE - http://localhost:3000/api/v1/{schemaName}/:id - Delete and Item by Id
 */
```

## Server Custom Route with ApiQuery

```js
var express = require('express');
var router = express.Router();


var mongoose = require('mongoose');
var crud = require('crud-mongoose-simple');
mongoose.plugin(crud);

var personSchema = new Schema({
	fristName: String,
	lastName: String
});

var personModel = mongoose.model('Person', personSchema);

router.route('/person/listbyuser').get(function(req, res, next){
	var query = {
		where : {
			userId : '578d33f2d0920b0db20f8643'
		},
		pageSize : 25,
		sort : '-firstName',
		select : 'firstName lastName',
		populate : ['user']
	};
	req.apiQuery = query;
	next();
}, personModel.httpGet())
```

## Server Schema Query

```js
var express = require('express');
var router = express.Router();


var mongoose = require('mongoose');
var crud = require('crud-mongoose-simple');
mongoose.plugin(crud);

var personSchema = new Schema({
	fristName: String,
	lastName: String
},{
	query : {
		pageSize : 25,
        sort : '-firstName',
        select : 'firstName lastName'
	}
});

var personModel = mongoose.model('Person', personSchema);

 //items filter by Schema Qeury
router.route('/person/list').get(personModel.httpGet())
```


##Example Call From Client Side By jQuery:

## List
```js

Get List with query params. (working all mongoose query)

var query = { where : {},  skip: 10, limit: 20 };

query.where= {
    'occupation': { "$regex": "host", "$options": "i" },
    'name.last': 'Ghost',
    'age': { $gt: 17, $lt: 66 },
    'likes': { $in: ['vaporizing', 'talking'] }
};

query.select = 'name occupation';

query.sort = '-occupation';

$.get('http://localhost:3000/api/person/list', query, function(result, status){
    console.log(result);
});
```


##List Pagination
```js

var query = { where : {},  pageSize : 25, page : 1 };

query.select = 'name occupation';

query.sort = '-occupation';

$.get('http://localhost:3000/api/person/list', query, function(result, status){
    console.log(result);
});
```

##Create
```js

var data = {
    name : {first : "Giga",
            last : "Chkhikvadze" },
    age : 50
}

$.post('http://localhost:3000/api/person/', data, function(result){
    console.log(result);
});
```

##Read
```js

var id = '578d33f2d0920b0db20f8643';

$.get('http://localhost:3000/api/person/' + id, function(result, status){
     console.log(result);
});
```

##Edit
```js

var id = '578d33f2d0920b0db20f8643';

var data = {
    name : {first : "Giga",
            last : "Chkhikvadze" },
    age : 50
}

$.ajax({
    url: 'http://localhost:3000/api/person/' + id,
    type: 'PUT',
    success: function(result) {
        console.log(result);
    }
});
```

##Delete
```js

var id = '578d33f2d0920b0db20f8643';

$.ajax({
    url: 'http://localhost:3000/api/person/' + id,
    type: 'DELETE',
    success: function(result) {
        console.log(result);
    }
});
```

