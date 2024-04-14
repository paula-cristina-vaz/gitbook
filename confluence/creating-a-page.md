# Creating a Page

Create a page in confluence from a markdown source with an image.

## Before you start

Ensure that you have: 
* An API key.
* A parent page created.
* Writing permissions in the space where you are creating the page. 

## Overview

![](./images/sequence-diagram.png)

## Steps

### 1. Convert markdown to HTML

```md
# Hello World
This is markdown.
![world](images/world.png)
```

```html
<h1>Hello World</h1>
<p>This is markdown.</p>
<img href="world.png" alt="world"/>
```

### 2. Create page

```bash
curl -u my.email@work.com:MyToken \
 -X POST \
 -H 'Content-Type:application/json' \
 -d '{\ 
       "type":"page", \ 
       "title":"Hello World",\  
       "space":{"key":"My Space"},\
       "body":{\
         "storage":{ \
           "value":"", \  
           "representation":"storage"\
         }\
       } \
     }' 
http://my-cloud.atlassian.com/confluence/rest/api/content/
```

```bash
200 OK
{ 
  "id": "123456789",
   …
}
```

### 3. Upload image


```bash
curl -v -S -u my.email@work.com:MyToken \
 -X POST \
 -H "X-Atlassian-Token: no-check" \ 
 -F "file=@world.png" \
 -F "comment=this is the image" 
http://my-cloud.atlassian.com/confluence/rest/api/content/123456789/child/attachment
```

```bash
200 OK

```

## Result

After executing all the steps you should be able to view a page with an image created in your space.
