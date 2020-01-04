# Vue Django plugin

Utility to use Django (with Django Rest Framework ) as Backend framework and Vue as a front end framework.

​	VueDjango creates a Django object inside your aplication component, this object has access to the common context variables that injects to its templates such as, the CSRF Token, User, Navigation URLs, nad the  Endpoints to authentication and requests. This  allows Vue interact seamlessly with the Django backend.

## Requierements

You need  to have the VueDjango aplication running on the server side. The plugin also uses Axios as a dependency.

## Instalation

On your **main.js** file

```javascript
Vue.prototype.$django = Django(DJANGO_CONTEXT)
```

​	DJANGO_CONTEXT is the  contaant generated by the VueDjango BackEnd tag, the tag shoul be located before the  Vue definition to use the  package.

## Usage

### The Django Object

All the metadata is storage in this object, it can be accessed in the Vue components through **this.$django** it has several utilities:

#### User

this.$django.user

​	Shows the authenticated user at the time, if is an anonymous user, it will return **false**

​	If there is an authenticated user will return an object with the base user metadata found in the default django user model.

```json
user:{
    id: 1,
	email: "user@email.com",
	first_name: "jon",
    last_name: "doe",
    username: "jdoe",
    //Groups will return aa Array of names of the groups that the user is part of
	groups:[],
    //Permisions will return aa Array of names of the permisions that the user is part of
	permissions:[],
	}
```

#### Auth

this.$django.auth

 Return an object with all the information and variables necessaries to authenticate a user, it has two main methods.

##### Login: 

Used  as **this.$django.auth.login({ user/ password })** 

​	Returns a Promise that is resolved opens a session. It should refresh the browser to update the user object. Its recommended to redirect to a 'on_login' path if defined. 

```javasctript
$django.auth.login({ username:'jon', password:'super47774' }).then(
  resolve(done){
	 window.location = host+'/'+this.$django.autentication.on_login
  }).catch(
  //catch error
  )
```

##### Logout: 

Used  as **this.$django.auth.logout()** 

Returns a Promise that is resolved closes a session. It should refresh the browser to update the user object. 

```
$django.auth.logout() // Automatically redirects to on_logout
```

#### Navigation Paths

​	Returns an Array of objects, identifying the routes that the Django application serves. This is useful for the usage of Vue router. 

#### Models

​	Returns a list of Models found in the application, a Model is an endpoint in the  API application that groups several other, each model has a base path and several methods, each method makes a request to the API following certain specifications, found in the API. 

​	The VueDjango Back end uses Django Rest Framework transforms each ModelViewSet or APIViews in a front end model. If the model is based on a ModelViewSet endpoints, the actions are defined in the back end and will reflect the name of the Viewset method, unless this is edited in the back end.  is is an APIView it will reflect the name given to the endpoint in the router. A Django ModelViewSet with no modifications will be represented as a model with its default methods.

​	So, for example if we have the ModelViewset "Fruit" in the back-end, we will have a it represented as **this.$django.models.fruit** and we can access through it to different actions. And each will return a Promise, that on resolve will return  the result.

1. - A list of Fruits

     ```javascript
     methods(){
     	async listFruits(){
     		// Returns the list
     		let fruits = await this.$django.models.fruit.list()
     		return fruits
     	}
     }
     
     //returns
     //[  
     //	{name: 'apple', id:1},
     //  {name: 'grapes', id:2},
     //  {name: 'banana', id:3},
     //]
     ```

   - create a Fruit

     ```javascript
     methods(){
     	createFruit(){
     		// Returns the list
     		this.$django.models.fruit.create({name:'Pineapple'}).then(res=>{
                 return res // {name: 'Pineapple', id:4},
             },fail=>{
                return fail // Will return a forma error such as
                 // errors [{'name':'the name shuld be all llowercase' }]
             }
             )
     	}
     }
     
     ```

     

   - update a Fruit

     ```javascript
     methods(){
     	async updateFruit(){
     		// Returns the list
             let id =  4
             await this.$django.models.fruit.update(id,{name:'pear'})
     	}
     }
     //calling th list method
     //[  
     //	{name: 'apple', id:1},
     //  {name: 'grapes', id:2},
     //  {name: 'banana', id:3},
     //  {name: 'pear', id:4},
     //]
     
     ```

     

   - delete a Fruit

2. ```javascript
   methods(){
   	async deleteFruit(){
   		// Returns the list
           let id =  4
           await this.$django.models.fruit.destroy(id)
   	}
   }
   //calling th list method
   //[  
   //	{name: 'apple', id:1},
   //  {name: 'grapes', id:2},
   //  {name: 'banana', id:3},
   //]
   ```

3. 

