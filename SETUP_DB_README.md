#Make a DataBase

##(1) ```npm init```

	makes your package.json file

##(2)```npm install```

	make node_modules

##(3) ```npm install gitignore``` or ```npm install gitignore -g``` (if you don't have gitignore installed globally)

		makes a .gitignore file

#ADD DEPENDENCIES knex, pg dotenv

##(4)```npm install knex pg dotenv  --save```

  Installs dotenv module and adds to the dependencies in ```package.json```(this loads the environment variables)Installs ```pg``` and ```knex``` and add them to the dependencies in the ```package.json```

##(5)```knex init```

	Creates ```knexfile.js```

##(6)```git init```

  Initialize git repo

##(7)```echo node_modules > .gitignore```

		moves node modules to a ```.gitignore``` file

##(8)```touch .env``` 	 

	Makes a empty ```.env``` file in the root directory

##(9)```echo .env >> .gitignore```

		adds ```.env``` file to ```.gitignore``` so git doesn't track it

##(10)```atom .```

	opens atom if you're using Atom

##(11)Change knexfile.js

	```
	require('dotenv').config();
  		module.exports = {
    	development: {
      	client: 'pg',
        connection: 		'postgres://localhost/DATABASE_NAME'
        },
      production:{
      	client: 'pg',
      	connection: process.env.DATABASE_URL + '?ssl=true'
      	}
    }
		```
##(12)Create directory called db
	```
	touch db
	```

##(13)Makefile knex.js

	Add to ```knex.js```
		```
		var environment = process.env.NODE_ENV || 'development';
    var config = require('../knexfile')[environment];
    var knex = require('knex')(config);
    module.exports= knex;
		```

#SETTING UP DATABASE POSTGRES DATABASE

##(1)Open Terminal

	If you have postgresql installed continue to step 2, if not visit [here](https://www.moncefbelyamani.com/how-to-install-postgresql-on-a-mac-with-homebrew-and-lunchy/) to see how to install on it with homebrew.

##(2)```createdb db_name```
	###### *db_name should be whatever you want.*

	go into ```knexfile.js``` and add database name to ```development``` connection
		```
		connection: 'postgres://localhost/DATABASE NAME YOU JUST CREATED'
		```

##(3) ```knex migrate:make users```
*we will make a users table for auth, so name it users*

exports.up = function(knex, Promise) {
  return knex.schema.createTable('users', function(table){
    table.increments();
  	table.string('userName');
		table.text('password');
	});
};
exports.down = function(knex, Promise) {
  return knex.schema.dropTableIfExists('users');
};


##(4)Knex migrate:make table_name
(we will make a list table for the list data)

exports.up = function(knex, Promise) {
 return knex.schema.createTable('list',function(table){
   table.increments();
   table.text('list');
   table.integer('users_id').references('users.id');
  });
};
exports.down = function(knex, Promise) {
  return knex.schema.dropTableIfExists('list');
};

##(5)Knex migrate:latest
Adds tables to your database

SEEDING DATA
-Add in a number so the seeds will run in order

##(6)Knex seed:make 0_seed_reset
	-go into new seed file and set up seed data

exports.seed = function(knex, Promise) {
  return knex.raw("TRUNCATE users , list RESTART IDENTITY CASCADE")
  .then(function(){
    return knex('users').del()
  .then(function(){
    return knex('list').del();
    });
 	});
};



##(7)Knex seed:make 1_seed_users
-go into new seed file and set up seed data
	-To make hashed passwords go to
https://www.dailycred.com/article/bcrypt-calculator#

exports.seed = function(knex, Promise) {
// Deletes ALL existing entries
return knex(‘users’).del()
  .then(function () {
    return Promise.all([
    // Inserts seed entries
      knex('users').insert({userName: 'alex', password: 'hashed password'}),

      knex('users').insert({userName: 'bradford', password: 'hashed password'}),

      knex('users').insert({userName: 'west', password : 'hashed password'})
      ]);
  });
};


##(8)Knex seed:make 2_seed_list
-go into new seed file and set up seed data

exports.seed = function(knex, Promise) {
// Deletes ALL existing entries
  return knex('list').del()
    .then(function () {
      return Promise.all([
        // Inserts seed entries
        knex('list').insert({ list: 'do something fun', users_id: 1}),
        knex('list').insert({ list: 'do something sad', users_id: 2}),
        knex('list').insert({ list: 'do something weird', users_id: 3})
      ]);
    });
};

##(9)Knex seed:run
	-adds data to your table

#HEROKU SETUP
##(1)Git add .

##(2)Git commit -m “message”

##(3)heroku create app-name

##(4)heroku addons:create heroku-postgresql

##(5)heroku config
  -Add url to .env
    -DATABASE_URL=heroku url

##(6)knex migrate:latest --env production
	-runs the latest migration to heroku

##(7)knex seed:run --env production
	-runs the latest seed to your production

##(8)git push heroku master
	-push master branch to heroku
