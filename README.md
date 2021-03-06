# Protractor Mock
A NodeJS module to be used alongside [Protractor](https://github.com/angular/protractor) to facilitate setting up mocks for HTTP calls for the AngularJS applications under test. 

This allows the developer to isolate the UI and client-side application code in our tests without any dependencies on an API.

**This plugin does not depend on Angular Mocks (ngMockE2E) being loaded into your app; therefore, there is no need to modify anything within your current Angular web application.**

## Installation
	npm install protractor-http-mock --save-dev
## Configuration
In your protractor configuration file, we can set the following options:

### Mocks
We can set a collection of default mocks to load for every test, and the name of the folder where your mocks will reside. More on this later.

  	mocks: {
    	default: ['mock-login'], // default value: []
    	dir: 'my-mocks' // default value: 'mocks'
  	},

### Directories and file names
We can also configure our root directory where the mocks and protractor configuration will be located; as well as, the name of the protractor configuration file.

  	onPrepare: function(){
    	require('protractor-http-mock').config = {
			rootDirectory: __dirname, // default value: process.cwd()
			protractorConfig: 'my-protractor-config.conf' // default value: 'protractor.conf'
    	};
  	}

## Usage
Mocks are defined as JSON objects describing the request and response for a particular HTTP call:

  	  {
		request: {
	      path: '/users/1',
	      method: 'GET'
	    },
	    response: {
	      data: {
	        userName: 'pro-mock',
	        email: 'pro-mock@email.com'
	      }
	    }
	  }

And then set the mock at the beginning of your test before your application loads:

	var mock = require('protractor-http-mock');
	...

	  mock([{
	    request: {
	      path: '/users/1',
	      method: 'GET'
	    },
	    response: {
	      data: {
	        userName: 'pro-mock',
	        email: 'pro-mock@email.com'
	      }
	    }
	  }]);

Make sure to clean up after test execution. This should be typically done in the `afterEach` call to ensure the teardown is executed regardless of what happens in the test execution:

	afterEach(function(){
	  mock.teardown();
	});

### Mock files
Mocks can also be loaded from physical files located in the `mocks.dir` directory that we defined in our configuration: 

  	tests
	    e2e
	      protractor.conf.js
	      mocks
	        users.js
	      specs
	        ...


You could simply load your mocks as follows:

	mock(['users']);

Files are structured as standard node modules. The strings passed are the path of the file relative to the mocks directory - the same as if you would be doing a standard `require()` call.

	module.exports = { ... }; // for a single mock.

or

	module.exports = [ ... ]; // for multiple mocks.


### Schema
The full schema for defining your mocks is as follows:

	  request: {
	    path: '/products/1/items',
	    method: 'GET',
	    params: { // These match the querystring params. This is an optional field.
	      page: 2,
	      status: 'onsale'
	    }
	  },
	  response: {
	    data: {}, // This is the return value for the matched request
	    status: 500 // The HTTP status code for the mocked response. This is an optional field.
	  }

#### Request
Defining `params` will help the plugin match more specific responses but it is not required. If the `params` are not set, then it will not be taken into account when matching a request.

Protractor mock will respond with the **last** matched request in case there are several matches.

#### Response
The default `status` value is 200 if none is specified.

### Examples
Included in the code base are examples on how to use this plugin. Please take a look at the "examples" folder.

To run these tests locally, please follow these steps from the root directory:

1. `npm install`
2. `node_modules/.bin/webdriver-manager update`
3. `npm run example`


### Contributions

* Thanks to `nadersoliman` for his input and pull request on allowing the user to set custom name for the protractor configuration file.
