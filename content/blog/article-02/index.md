+++
title = "Kotlin Spring Boot use-case: dependency injection of a list of services"
description = ""
featured = "/kotlin-springboot.png"

type = ["blog","post"]
tags = [
    "java",
    "kotlin",
    "spring boot",
    "development",
]
date = "2020-04-28"
categories = [
    "development"
]
[ author ]
  name = "Jérémy Basso"
+++

## Context

With Spring boot framework, it is possible to inject a list of beans as a dependency. I always found the use of this feature useful and elegant, so I am sharing my take on this subject.
My example will be using Kotlin, but we can assume that it works in Java just as well.

<p align="center">
<img src="/kotlin-springboot.png" style="width:600px"/>
</p>

Our example will solve the problem of authentication with multiple providers : standard auth, Facebook,  Google or anything else. To keep it simple, we will focus only on the login feature.


## Setting up the providers

When injecting dependencies, we will create an interface that will define the beans to inject. Here, we will call this interface **ILoginProvider** :
```kotlin
interface ILoginProvider {  
  
    fun login(request: AuthRequest): Account 
    
    fun supports(request: AuthRequest): Boolean  
  
}
```
The `login` function is where the login logic happens, the `supports` function is in charge of returning if the AuthRequest object is supported by each provider or not.
We pass an object **AuthRequest** as an argument for `login` and `supports`function:
```kotlin
data class AuthRequest(
    val type: AuthType = AuthType.OTHER // enum : [EMAIL,FACEBOOK,OTHER]

    val email: String = ""
    
    val credentials: String? = null // only filled with email/password
    
    val socialToken: String? = null // only filled with FB
)
```

This interface will be implemented by a new class for each source. For our example we will create **EmailLoginProvider** and **FacebookLoginProvider** (example below), that will both implement **ILoginProvider**.
```kotlin
@Service  
class FacebookLoginProvider() : ILoginProvider {

override fun login(request: AuthRequest): Account {
	// TODO : implement login function
	}

override fun supports(request: AuthRequest): Boolean {
	return request.type == AuthType.FACEBOOK
	}
}
```



## Setting up the service


Now it is time to inject those providers, and it is definitely the cool part. When using Spring Boot, you can use the magical `@Autowired`annotation to perform injection. In our case we want to inject all beans implementing **ILoginProvider** in our LoginService :
```kotlin
@Service 
class LoginService() {

@Autowired
lateinit var loginProviders: List<ILoginProvider>

}
```
The last thing to do is to make good use of this list of providers, by correctly wiring each **AuthRequest** to the accurate provider: You can do the following: 
```kotlin
@Service 
class LoginService() {

@Autowired
lateinit var loginProviders: List<ILoginProvider>

fun login(request: AuthRequest): Account {
	val provider = loginProviders.firstOrNull{it.supports(request)} 
		?: throw Exception("Provider not found")
	return provider.login(request)
    }
}
```

## Conclusion

By injecting lists of providers, we are able to maintain genericity between different sources of providers. In our example, it is very easy to disable a source (by making `supports`return false), add a new one (by adding new **AuthType** and creating a new provider).
 It is very useful when the context is a constantly changing product

You can scale this approach to any number of providers, and add more logic for provider selection through `supports` function..

Some common features benefits a lot from this approach, such as authentication, payment. This approach can be adapted to any language or framework, but my point was to highlight how elegant it was using Spring boot framework.

**Thanks for reading !**