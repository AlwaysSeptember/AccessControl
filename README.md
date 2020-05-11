
# Access control

OpenSource projects can have needs for quite complex authentication rules, and those rules need to be used across multiple systems. 

In the PHP project we have a karma system that allows access to various things such as:

* editing the wiki
* voting
* releasing pecl packages
* viewing spam bug reports
* viewing security reports.


Github is a really useful tool for open source projects, but the access control systems it has are nowhere near powerful enough to use. 

Building finer grained controls into Github would be exactly the wrong thing to build. Instead we need tools like github to be able to use external access control systems, so that open source project can use their own access control systems across multiple external tools.

### Delegating authentication

On the github side there should be a setting for an 'access control' that defines for a repo whether to use an external system for access rules.

That access control needs to be a URL that will be called with:

* userid - who is trying to do something
* platform - what platform they are doing it on.
* action_type - what they are trying to do
* details - an object that contains details specific to the action_type they are trying to do.

e.g. 

```
{ 
   "user_id": "Danack",
   "platform": "github",
   "action_type": 'post_comment',
   "details" : {
   		"reply_to": $previousCommentId,
   		"text": "That is the best thing ever."
   }  
}
```

That external system should return one of:

* allowed - the user is allowed to do that.
* denied - the user is not allowed to do that.
* request_access_url - the user is not currently allowed to do that, they should go to this url to request access.

TODO - think about signing requests, to avoid people from being able to probe permissions too easily.

### Flow for requesting access


When a user is on a platform, and they attempt to perform an action, and the result of the access control check is 'request_access_url' the following steps should happen.


#### Plaform initiates access request

The platform they are on makes a request to the `request_access_url` and includes the following parameters

* access_allowed_url - The url to return the user to if access for that action is allowed.The access_allowed_url can contain arbitrary parameters. A recommended one would be a token to retrieve details of what the user was trying to do, before the access check was done.
* access_denied_url - The url to return the user to if access is not allowed.


The access control system must return a unique url, that the platform should redirect the user to.


#### Redirect to access control 

The user should be redirected to url returned by the access control system. The access control system can then ask the user to login or otherwise authenticate their identity, or perform whatever other steps are required to decide whether to give that user access or not.

The user should then be redirected to `access_allowed_url` or `access_denied_url` as appropriate.

### After returning to original platform

If they are redirected to the `access_allowed_url` then either manually or automatically the action they were attempting to do can be retried.

If they were redirected to the `access_denied_url` then the action should not be retried either manually or automatically.

TODO - caching for performance reasons....probably only needed for 'readonly' actions e.g. viewing bugs.

TODO - think about error messages. tbh, they are probably more likely to cause drama than to be helpful.
