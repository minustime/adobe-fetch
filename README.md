[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0) 
[![Version](https://img.shields.io/npm/v/@adobe/fetch.svg)](https://npmjs.org/package/@adobe/fetch)
[![Downloads/week](https://img.shields.io/npm/dw/@adobe/fetch.svg)](https://npmjs.org/package/@adobe/fetch)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Build Status](https://travis-ci.com/adobe/adobe-fetch.svg?branch=master)](https://travis-ci.com/adobe/adobe-fetch)
[![codecov](https://codecov.io/gh/adobe/adobe-fetch/branch/master/graph/badge.svg)](https://codecov.io/gh/adobe/adobe-fetch)
[![Greenkeeper badge](https://badges.greenkeeper.io/adobe/adobe-fetch.svg)](https://greenkeeper.io/)

# adobe-fetch

Call Adobe APIs

## Goals

Make calling Adobe APIs a breeze!  

This package will handle JWT authentication, token caching and storage.  
Otherwise it works exactly as [fetch](https://github.com/bitinn/node-fetch)

### Installation

```
npm install --save @adobe/fetch
```

### Common Usage

```javascript

    const fs = require('fs');
    
    const config = { 
      auth: {
          clientId: 'asasdfasf',
          clientSecret: 'aslfjasljf-=asdfalasjdf==asdfa',
          technicalAccountId: 'asdfasdfas@techacct.adobe.com',
          orgId: 'asdfasdfasdf@AdobeOrg',
          metaScopes: ['ent_dataservices_sdk']
      }
    };
    
    config.auth.privateKey = fs.readFileSync('private.key');

    const adobefetch = require('@adobe/fetch').config(config);

    adobefetch("https://platform.adobe.io/some/adobe/api", { method: 'get'})
      .then(response => response.json())
      .then(json => console.log('Result: ',json));

```

#### Config object

The `config` object is where you pass in all the required and optional parameters to authenticate API calls.

| parameter          | integration name     | required | type                              | default                        |
| ------------------ | -------------------- | -------- | --------------------------------- | ------------------------------ |
| clientId           | API Key (Client ID)  | true     | String                            |                                |
| technicalAccountId | Technical account ID | true     | String                            |                                |
| orgId              | Organization ID      | true     | String                            |                                |
| clientSecret       | Client secret        | true     | String                            |                                |
| privateKey         |                      | true     | String                            |                                |
| passphrase         |                      | false    | String                            |                                |
| metaScopes         |                      | true     | Comma separated Sting or an Array |                                |
| ims                |                      | false    | String                            | <https://ims-na1.adobelogin.com> |

In order to determine which **metaScopes** you need to register for you can look them up by product in this [handy table](https://www.adobe.io/authentication/auth-methods.html#!AdobeDocs/adobeio-auth/master/JWT/Scopes.md).

For instance, if you need to be authenticated to call API's for both GDPR and User Management you would [look them up](https://www.adobe.io/authentication/auth-methods.html#!AdobeDocs/adobeio-auth/master/JWT/Scopes.md) and find that they are:

- GDPR: <https://ims-na1.adobelogin.com/s/ent_gdpr_sdk>
- User Management: <https://ims-na1.adobelogin.com/s/ent_user_sdk>

Then you would create an array of **metaScopes** as part of the `config` object. For instance:

```javascript
const config = {
  auth: {
      clientId: 'asasdfasf',
      clientSecret: 'aslfjasljf-=asdfalasjdf==asdfa',
      technicalAccountId: 'asdfasdfas@techacct.adobe.com',
      orgId: 'asdfasdfasdf@AdobeOrg',
      metaScopes: [
        'https://ims-na1.adobelogin.com/s/ent_gdpr_sdk',
        'https://ims-na1.adobelogin.com/s/ent_user_sdk'
      ]
  }
};
```

However, if you omit the IMS URL, the package will automatically add it for you when making the call to generate the JWT. 

For example:

```javascript
const config = {
  auth: {
      clientId: 'asasdfasf',
      clientSecret: 'aslfjasljf-=asdfalasjdf==asdfa',
      technicalAccountId: 'asdfasdfas@techacct.adobe.com',
      orgId: 'asdfasdfasdf@AdobeOrg',
      metaScopes: ['ent_gdpr_sdk', 'ent_user_sdk']
  }
};
```

This is the recommended approach.

#### Custom Storage

By default, [node-persist](https://github.com/bitinn/node-persist) is used to store all the active tokens locally.  
Tokens will be stored under **/.node-perist/storage**

It is possible to use any other storage for token persistence. This is done by providing **read** and **write** methods as follows:  

```javascript
const config = {
  auth: {
      clientId: 'asasdfasf',
      
      ...
      
      storage: {
        read: function() {
          return new Promise(function(resolve, reject) {
            let tokens;
            
            // .. Some logic to read the tokens ..
            
            resolve(tokens);
          });
        },
        write: function(tokens) {
          return new Promise(function(resolve, reject) {
            
            // .. Some logic to save the tokens ..
            
            resolve();
          });
        }
      }
  }
};
```

Alternatively, use async/await:

```javascript
const config = {
  auth: {
      clientId: 'asasdfasf',
      
      ...
      
      storage: {
        read: async function() {
          return await myGetTokensImplementation();            
        },
        write: async function(tokens) {
          await myStoreTokensImplementation(tokens);
        }
      }
  }
};
```

## Logging

Every request will include a unique request identifier sent via the **x-request-id**.   
The request identifier can be overriden by providing it through the headers:
```javascript
fetch(url, {
  headers: { 'x-request-id': myRequestID }
});
```

We use [debug](https://github.com/visionmedia/debug) to log requests. In order to see all the debug output, including the request identifiers, run your app with the **DEBUG** environment variable including the **@adobe/fetch** scope as follows:
```
DEBUG=@adobe/fetch
```

### Contributing

Contributions are welcomed! Read the [Contributing Guide](.github/CONTRIBUTING.MD) for more information.

### Licensing

This project is licensed under the Apache V2 License. See [LICENSE](LICENSE) for more information.
