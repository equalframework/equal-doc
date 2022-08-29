# Router 

The Router `equal/route/Router.class.php` class handles routes located inside the `config/routing`folder.

Structure of a route  :	URI -> operation

Reserved parameters :

- get

- do

- show

- route

- announce

  

routing to scripts

	simple scripts 
	
	Main constraint : routing and FS tree



## Creating Routes

Inside the `config/routing` directory, you have the possibility to build your own routes. 

The expected route structure :

```json
	{
 *      method:         string      HTTP method, example: "GET", "POST", "PUT", "DELETE"
 *      path:           string      full path of the requested URI
 *      parts:          array       array of parts composing the path
 *      operation:      string      the operation the URI resolves to 
 *      params:         array       map of parameters names as keys, related to their values
 *  }
```



There are a few default routes built in eQual, to get the users and groups.

**Example**  : 

```JSON
"/user/:ids": {
        "GET": {
            "description": "Retrieve fields values related to a given user",
            "operation": "?get=core_model_read&entity=core\\User"
        },
        "PUT": {
            "description": "Update a user",
            "operation": "?do=core_user_update"
        },
        "DELETE": {
            "description": "Delete a user",
            "operation": "?do=core_model_delete&entity=core\\User"
        }
    },
```

You need the operation and description properties to build your routes. 

The **operation** will be about the "Query" part of the URI/URL. 

And the description is simply a field to indicate what the operation does.

