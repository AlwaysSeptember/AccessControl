
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
* action_type - what they are trying to do
* details - an object that contains details specific to the action_type they are trying to do.


e.g. 

{ 
   user_id: "Danack",
   "action_type": 'post_comment',
   "details" : {
   		"reply_to": $previousCommentId,
   		"text": "That is the best thing ever."
   }  
}


That external system should return one of:

* allowed - the user is allowed to do that.
* denied - the user is not allowed to do that.
* request_access_url - the user is not currently allowed to do that, they should go to this url to request access. 


### Requesting access

The flow for requesting access should be similar to the following.

When a user requests access, it should be possible for the platform they are on to include in the access request:

* access_allowed_url - The url to return the user to if access for that action is allowed.
* access_denied_url - The url to return the user to if access is not allowed.

The access_allowed_url can contain arbitrary parameters. A recommended one would be a token to retrieve details of what the user was trying to do, before the access check was done.



TODO - maybe also 'auto_resubmit' ?

TODO - talk to
https://twitter.com/GitHubHelp

https://support.github.com/