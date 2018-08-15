# python-aog

A simple and easy to use python client library for Actions on google webhook.
A complete demo actions on google project built with aog python library is availble [here](https://github.com/DeveloperBibin/Demo-python-aog).



## Installation

You can install using ``` pip install aog ``` *or* ``` pip3 install aog```.



## Code sample


```python
import json
import os
from flask import Flask
from flask import request
from flask import make_response
from aog import conv

# Flask app should start in global layout
app = Flask(__name__)

@app.route('/', methods=['POST'])
def fullfillment():
    req = request.get_json(silent=True, force=True)

    print("Request:")
    print(json.dumps(req, indent=4))

    res = makeFullfillment(req)
    res = json.dumps(res, indent=4)
    #print(res)
    final = make_response(res)
    final.headers['Content-Type'] = 'application/json'
    return final

def makeFullfillment(req):
  # Wrte your python code here
  #isfrom function is used to check from which dialogflow intent request came from.
  if conv.isfrom(req,'ask_intent'): 
    # ask method expect a reply from user. after the message mic will be open for user to reply.
    res=conv.ask("You made me ask this","You made me print this")
    return res

```    

## Simple Responses

![sample image](https://firebasestorage.googleapis.com/v0/b/kochi-metro-f6942.appspot.com/o/image5.png?alt=media&token=13426d26-95d0-45f9-9917-22aadb8488c3) 

Simple responses can appear on audio-only, screen-only, or both surfaces. They take the form of a chat bubble visually, and TTS/SSML sound.

### Sample Code

```python
# ask method expect a reply from user. after the message mic will be open for user to reply.
res=conv.ask(speech="You made me ask this",displayText="You made me display this")
return res

```
 
```python
# close method terminates conversation with the message. 
res=conv.close("Good bye")
return res

```
 

## Rich responses

Rich responses can appear on screen-only or audio and screen experiences. They can contain the following components:

- One or two simple responses (chat bubbles)
- An optional basic card
- Optional suggestion chips
- An optional link-out chip
- An option interface (list or carousel)

Requirements : ```actions.capability.SCREEN_OUTPUT``` you can check the requirement using ```conv.has_capability(req,actions.capability.SCREEN_OUTPUT)```

### Basic card

![sample image](https://firebasestorage.googleapis.com/v0/b/kochi-metro-f6942.appspot.com/o/image4.png?alt=media&token=233e4196-a0a3-4616-af89-df36c30ab1eb)

A basic card displays information that can include the following:

- Image
- Title
- Sub-title
- Text body
- Link button
- Border

*Supported on ```actions.capability.SCREEN_OUTPUT```*.
*Required components : formatted text or image.*
*Optional components : Titile, Sub-title, link button, Border (Image scale)*

### Sample code

```python 
if conv.has_capability(req,'actions.capability.SCREEN_OUTPUT'):  
      res=conv.basic_card(formatted_text="Content in the basic card",speech="You made me say this",\
                        title="Title of the Card",subtitle="subtitle",card_image=url,link_title="Link Title",link_url=url,image_scale="CROPPED")
      return res

 ```



### List

![sample image](https://firebasestorage.googleapis.com/v0/b/kochi-metro-f6942.appspot.com/o/image3.png?alt=media&token=24683e37-da6f-476a-b715-73aac202a346)

The single-select list presents the user with a vertical list of multiple items and allows the user to select a single one. Selecting an item from the list generates a user query (chat bubble) containing the title of the list item.

*Minimum 2 items are required.*
*Supported on ```actions.capability.SCREEN_OUTPUT```*
*Required components : speech,items.*
*Optional components : titile, description, image_url, image_scale, image_text,key*

### Sample code

```python
 url="https://developers.google.com/actions/assistant.png"
    res=conv.list(speech="this is a list",title="List title",items=
                            [{'title':"item title",'description':'item description','image_url':url,'image_text':'image here','key':'your_key','synonyms':['one1','two1']},\
                            {'title':"item title1",'description':'item description1','image_url':url,'image_text':'image here1','key':'your_key1','synonyms':['one2','two2']},\
                            {'title':"item title2",'description':'item description1','image_url':url,'image_text':'image here1','key':'your_key2','synonyms':['one3','two3']}])
    return res

```


### Handling a selected item

Once user select an item, intent with event ```actions_intent_OPTION``` will be invoked. from this event you can get selected item's key using ```key=conv.list_item_selectd(req)``` method.


## Carousel

![sample image](https://firebasestorage.googleapis.com/v0/b/kochi-metro-f6942.appspot.com/o/image2.png?alt=media&token=8a4e5b8a-d169-401b-b876-d4335f9b04ce)

The carousel scrolls horizontally and allows for selecting one item.


*Minimum 2 items are required.*
*Supported on ```actions.capability.SCREEN_OUTPUT```*
*Required components : speech,items.*
*Optional components : titile, description, image_url, image_scale, image_text,key*


### Sample code

```python
 url="https://developers.google.com/actions/assistant.png"
    res=conv.carousels(speech="this is a list",title="List title",items=
                            [{'title':"item title",'description':'item description','image_url':url,'image_text':'image here','key':'your_key','synonyms':['one1','two1']},\
                            {'title':"item title1",'description':'item description1','image_url':url,'image_text':'image here1','key':'your_key1','synonyms':['one2','two2']},\
                            {'title':"item title2",'description':'item description1','image_url':url,'image_text':'image here1','key':'your_key2','synonyms':['one3','two3']}])
    return res

```


### Handling a selected item

Once user select an item, intent with event ```actions_intent_OPTION``` will be invoked. from this event you can get selected item's key using ```key=conv.list_item_selectd(req)``` method.


## Suggestion Chip

![image sample](https://firebasestorage.googleapis.com/v0/b/kochi-metro-f6942.appspot.com/o/image1.png?alt=media&token=cac5908f-47d1-4824-90cd-376b2de52fdd)

Use suggestion chips to hint at responses to continue or pivot the conversation.

### Sample code

```python
res=conv.suggestion_chips(speech="Here are some suggestions for you.",suggestions=['suggestion1','suggestion2','suggestion3'])
    return res
```

*Required components : speech,suggestions.*


## Helper Intents for taking information from user

You can obtain these values from user.

- Name
  - Display name
  - Given name
  - Family name
- Device location (Coordinates)
- Confirmation ( Generic confirmation from the user (yes/no question) )
- Date Time
- Last seen ( lastSeen property to determine whether the user has interacted with your Action before.)
- User id ( An unique string identifier )

### Sample code

```python
res=conv.ask_permission(['NAME','DEVICE_PRECISE_LOCATION','DEVICE_COARSE_LOCATION'],context="To get you know better")
    return res
```

*Minimum one permission is required.*
*context could contain explaination of obtaining permission.*

### Handling permission 

User can either accept or reject permission requests. bothways, an intent with event ```actions_intent_PERMISSION``` will be invoked. from this event you can handle the permissions.

### Sample code

```python
if conv.permission_granted(req): #Checking weather permission is granted or not.
      name=conv.get_username(req) # return type is a dictionary contains {displayName,givenName,familyName}
      location=conv.get_userlocation(req) # return type is a dictionary contains {latitude,longitude}
``` 

## Date and Time

You can obtain a date and time from users by using ```conv.ask_datetime()```. You can specify custom prompts when asking the user for a date and time.

### Sample code

```python
res=conv.ask_datetime(\
      initial="When do you want to come in?",\
      date="What day was that?",\
      time="What time"
      )
    return res
```

### Handling Date and Time

After user responds to date and time intent, an intent with event ```actions_intent_DATETIME``` will be invoked. from this intent you can handle the result using conv.get_datetime(req).
```python
date,time=conv.get_datetime(req) #date dictionary= {'year','date','month'} ,time dictionary ={hours and time} 
```

## Confirmation

You can ask a generic confirmation from the user (yes/no question) and get the resulting answer. The grammar for "yes" and "no" naturally expands to things like "Yea" or "Nope", making it usable in many situations.

```python
res=conv.ask_confirmation("Do you like python ?")
return res
```

### Handling Confirmation

Aftter user responds to confirmation message, and intent with event ``` actions_intent_CONFIRMATION ``` will be invoked. from this event we can obtain user reponse using ```boolValue=conv.get_confirmation(req) ```

## Last seen

You can use ```conv.get_lastseen(req)``` to determine whether the user has interacted with your Action before. method returns ```None``` if it's a first time user. else last seen time will be returned 

## User Id

You can obtain a unique string id to identify user from conv object using ```conv.get_userid(req)```.

## More Info

> For more information visit actions on google [documentation](https://developers.google.com/actions/assistant/responses). 


    
