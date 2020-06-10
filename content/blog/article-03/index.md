+++
title = "Node.js : Upload binary image \"on-the-fly\" with Fastify and Cloudinary"
description = ""

type = ["blog","post"]
tags = [
    "java",
    "kotlin",
    "spring boot",
    "development",
]
date = "2020-05-07"
categories = [
    "development"
]
[ author ]
  name = "Jérémy Basso"
+++
In this article I will describe "on-the-fly" upload of a binary image, using Fastify and Cloudinary in 	an Node.js environnement.

<p align="center">
<img src="/fastify_cdn.jpg" style="width:900px"/>
</p>


## Context

[Fastify](https://fastify.io) is a high performance web framework built for Node.js. In my opinion it is the best web framework nowadays (as I like to call it "Express 2.0") for backend purposes.

[Cloudinary](http://cloudinary.com) is a Content Delivery Network, which allow us to perform file upload and storage in a very efficient way. I like it a lot because of all the features around pictures manipulations, and because it provide very good package for free users.

The whole reason when using a CDN is to **avoid storing files in your backend storage** (wether it is database or file system), because it can lead to performance issues or storage overflow. This is the reason why I implemented a quick way to Upload image from a distant device to a CDN **without storing any file**.

My use case is written in Typescript, but the equivalent can be done with standard Javascript.
## Requirements
First, you will need to install some packages

```
npm install fastify fastify-multer cloudinary
```

You will also need to create an account on [Cloudinary](http://cloudinary.com), you can do it for free.

## The core logic

### Getting the data from outside

We need to initialize the `fastify` router, and bind it `fastify-multer` plugin :

```typescript
import fastify from 'fastify'
import multer from 'fastify-multer'

const server = fastify()
server.register(multer.contentParser)

server.listen(8080, (err, address) => {
  if(err) {
    console.error(err)
    process.exit(1)
  }
  console.log(`Server listening at ${address}`)
})
```
Then, we add a new POST route, `/api/profilePicture` with a specific `preHandler` from `multer` package :
```typescript
import fastify from 'fastify'
import multer from 'fastify-multer'
const storage = multer.memoryStorage()  
const upload = multer({ storage: storage })

... // See above

server.post('/api/profilePicture', {preHandler: upload.single('file')},  
 this.uploadProfilePicture.bind(this))

async uploadProfilePicture(req, res): Promise<string> {  
   const binaryData = req.file.buffer
   // TODO : do stuff
   return ''
}
```

We just set the storage mode for `multer` to memory storage, so it does not store the file in the file system, but as a `Buffer` object.

To send a picture, we consume a content of type multipart/form-data, that provides key-value couples.
With the `preHandler` option, we specify which key provides the data to read from the form data, in our case then name of the key is `file`.

So, what does `multer` does with the uploaded data ? 
This is when the magic happens, `multer`stores the file data into `req.file`. We can access the content of this file with the `buffer` field. 

There is nothing more to do for this part, we already have our file ready to upload on `binaryDate` variable !

### Uploading the data to Cloudinary

First of all, you will need to setup your Cloudinary client using 3 credentials you can find on your account's dashboard : cloud name, api key, api secret.
```typescript
cloudinary.config({  
  cloud_name : process.env.CDN_CLOUD_NAME,  
  api_key: process.env.CDN_API_KEY,  
  api_secret: process.env.CDN_API_SECRET  
})
```
Then, you can use your client to upload the data to Cloudinary, and retrieve the result. To do so, I like to "promisify" the function in order to ease the potential logic around. 

In my example, I perform an eager transformation on my picture, allowing to have a version of the picture cropped and centered in the face of the person (for a profile picture as an example) built by Cloudinary.
 Cloudinary provides tons of options to deal with pictures and it is likely that you find happiness among them.
```typescript
uploadPicture(content: Buffer): Promise<object> {  
    return new Promise((resolve, reject) => {  
        cloudinary.uploader.upload_stream(  
            {  
                folder: 'profile_pictures',  
				eager : [{ width : 400, height : 400, crop : 'crop', gravity : 'face'}]  
		    }, (error, result) => {  
                if (error) {  
                    throw Exception('Upload failed')
				  } else {  
                    resolve(result)  
                 }  
            }  
        ).end(content)  
    })  
}
```
### Wrapping everything up

The final route code looks like this :
``` typescript
import fastify from 'fastify' import multer from 'fastify-multer' const storage = multer.memoryStorage() const upload = multer({ storage: storage })
const storage = multer.memoryStorage()  
const upload = multer({ storage: storage })

const server = fastify()
server.register(multer.contentParser)

server.listen(8080, (err, address) => {
  if(err) {
    console.error(err)
    process.exit(1)
  }
  console.log(`Server listening at ${address}`)
})

server.post('/api/profilePicture', {preHandler: upload.single('file')},  
 this.uploadProfilePicture.bind(this))

async uploadProfilePicture(req, res): Promise<string> {  
   const binaryData = req.file.buffer
   const result = await uploadPicture(binaryData) // Watch below for details
   return result.url
}
```

In this example I only return the URL of the uploaded picture, but in a more real life use, I would store it in my database, in order to send to frontend the URL of the picture when needed.

I really liked this way of working with file uploads, and I highly recommend you to use that kind of strategy for your projects.


**Thanks for reading !**