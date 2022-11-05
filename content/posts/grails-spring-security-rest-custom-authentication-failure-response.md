---
title: 'Grails Spring Security Rest Custom Authentication Failure Response'
date: Fri, 29 Jun 2018 08:58:14 +0000
draft: false
tags: ['Grails', 'Grails', 'Spring Security']
---

The grails spring security rest plugin only returns a 401 status code for failed authentication. This creates a problem because you do not know the specific reason for failure and are forced to show the user a generic error message. The issue can be solved by overriding [RestAuthenticationFailureHandler](https://github.com/alvarosanchez/grails-spring-security-rest/blob/develop/spring-security-rest/src/main/groovy/grails/plugin/springsecurity/rest/RestAuthenticationFailureHandler.groovy) and registering your own implementation as a bean in `resources.groovy`. Grails plugins compile first allowing you to override classes as needed. In particular you override this method: 

```
void onAuthenticationFailure(HttpServletRequest request, 
								HttpServletResponse response, 
								AuthenticationException exception) 
								throws IOException, ServletException { 
									
	log.debug "Setting status code to ${statusCode}"									
	response.setStatus(statusCode) 
	response.addHeader('WWW-Authenticate', 'Bearer') 
}
```

And generate the response body based on the type of `AuthenticationException` as shwon below:


```
void onAuthenticationFailure(HttpServletRequest request, 
								HttpServletResponse response, 
								AuthenticationException exception) 
								throws IOException, ServletException {

	response.setStatus(statusCode) 
	response.addHeader('WWW-Authenticate', 'Bearer') 
	if (exception instanceof AccountExpiredException) { 
		msg = g.message(code: "springSecurity.errors.login.expired") 
	} else if (exception instanceof CredentialsExpiredException) { 
		msg = g.message(code: "springSecurity.errors.login.passwordExpired") 
	} else if (exception instanceof DisabledException) { 
		msg = g.message(code: "springSecurity.errors.login.disabled") 
	} else if (exception instanceof LockedException) { 
		msg = g.message(code: "springSecurity.errors.login.locked") 
	} else { 
		msg = g.message(code: "springSecurity.errors.login.fail") 
	} 
	PrintWriter out = response.getWriter() 
	response.setContentType("aplication/json") 
	response.setCharacterEncoding("UTF-8") 
	out.print(new JsonBuilder([message: msg]).toString())
	out.flush()
}	
``` 

And there you have it. A more pleasant response for the user.