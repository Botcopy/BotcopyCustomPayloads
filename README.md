# Botcopy Response Payload Format for Dialogflow CX

### Content

[Overview](#overview)

[Actions in Detail](#actions)

[Response Payload Details](#response-payload-structure-details)

- [Text Responses](#text-responses)
- [Suggestions](#suggestions)
- [Basic Cards](#basic-cards)
- [Carousels](#carousels)
- [Lists](#lists)

## Overview

Botcopy responses are designed to be flexible in their role and this is reflected in the payload structure. Components in the payload have an action field that dictates their behavior in the Botcopy chat when the component is selected by an end user. It allows multiple response types with different actions to be grouped together.

Examples:

- Suggestions can be used to send messages or links
- Card Buttons can send messages or links
- Carousels can contain more than one type of action
  - Link cards act as browse carousel cards
  - Message cards act as regular selection key carousel cards

### Actions

[message](#message) and [link](#link) are used for Suggestions, Cards, Carousel Items, and List Items.

Card can also use a [button](#button) action. Each button has its own individual action.

Only one action should be assigned to each payload element. In case of conflicting actions, Botcopy priorities message > link > button.

#### message

```
{
  "action": {
    "message": {
      "command": "Pricing",
      "type": "training"
    }
  }
}
```

Sends a message to the bot on selection

- command (optional): the event name or training phrase sent to the bot. Defaults to title if not provided
- type: either 'event' or 'training'. This corresponds to an event name or training phrase for an intent.

#### link

```
{
  "action": {
    "link": {
      "target": "_blank",
      "url": "https://botcopy.com"
    }
  }
}
```

Sends the user to a destination url within the a new tab, or a Botcopy webview

- url: the destination url
- target (optional): new tab or webview. Defaults to a Botcopy webview in the chat if not defined.

#### button

```
{
  "action": {
    "buttons": [
      {
        "action": {
          "link": {
            "target": "_blank",
            "url": "https://botcopy.com"
          }
        },
        "title": "Linkout Button"
      },
      {
        "action": {
          "message": {
            "command": "Pricing",
            "type": "training"
          }
        },
        "title": "Message Button"
      }
    ]
  }
}
```

An array of buttons, each with an action of their own. exclusively used for Cards.

- title: the title of the button
- action: defines the behavior of the button. Must be exclusively a single action
  - [message](#message)
  - [link](#link)

---

## Response Payload Structure Details

Each custom payload should contian a `botcopy` array with your desired rich responses.

Default CX text responses will render alongside your payload if they are provided.

Custom Rich Responses will be rendered in the chat in their arrayed order.

To add a custom payload to a CX response, select the 'Custom Payload' fulfillment option.

### Text Responses

An array of text responses.

textToSpeech, displayText, and ssml fields handle different aspects of the message.

```
{
  "botcopy": [
    {
      "text": [
        {
          "displayText": "Hello.",
          "ssml": "<speak>Hi!</speak>",
          "textToSpeech": "Hi, how are you doing?"
        }
      ]
    }
  ]
}
```

**text**

- displayText - the message displayed in the chat.
- ssml (optional) - SSML format text for the phrase spoken by a bot.
- textToSpeech (optional) - the phrase spoken by the bot. Takes priority over SSML. If both are provided in the payload, textToSpeech is used for the audio file.

---

### Suggestions

Suggestions prompt users with a clickable button after a series of responses. A suggestion can either continue the conversation with a **message** action, or send the user to an external link with a **link** action.

```
{
  "botcopy": [
    {
      "suggestions": [
        {
          "action": {
            "message": {
              "command": "Pricing",
              "type": "training"
            }
          },
          "title": "Message Suggestion"
        },
        {
          "action": {
            "link": {
              "target": "_blank",
              "url": "https://botcopy.com"
            }
          },
          "title": "Link Suggestion"
        }
      ]
    }
  ]
}
```

**suggestion**

- title: the label displayed in the suggestion chip to the end user
- action: defines the behavior of the suggestion. Must be exclusively a single action
  - [message](#message)
  - [link](#link)

---

### Basic Cards

Basic cards have a title, subtitle, and body containing information. Image media includes, but is not limited to, images, gifs, embedded youtube links.

Cards can also use a [button](#button) action, which is displayed in this example.

```
{
  "botcopy": [
    {
      "card": {
        "action": {
          "buttons": [
            {
              "action": {
                "link": {
                  "target": "_blank",
                  "url": "https://botcopy.com"
                }
              },
              "title": "Linkout Button"
            },
            {
              "action": {
                "message": {
                  "command": "Pricing",
                  "type": "training"
                }
              },
              "title": "Message Button"
            }
          ]
        },
        "body": "Card body",
        "image": {
          "alt": "Image of a cat",
          "url": "https://homepages.cae.wisc.edu/~ece533/images/cat.png"
        },
        "subtitle": "Card subtitle",
        "title": "Card title"
      }
    }
  ]
}
```

**card**

- title: title for the card
- image (optional): object containing media info
  - url: link to media
  - alt (optional): alt text for accessibility
- body (optional): body text for the card
- subtitle (optional): subtitle for the card
- action
  - [message](#message)
  - [link](#link)
  - [button](#button)

---

### Carousels

Carousels are a collection of cards. Each carousel item can have an action of either [message](#message), [link](#link), or [button](#button).

```
{
  "botcopy": [
    {
      "carousel": [
        {
          "action": {
            "link": {
              "target": "_blank",
              "url": "https://botcopy.com"
            }
          },
          "body": "Description One",
          "image": {
            "alt": "Image of a cat",
            "url": "https://homepages.cae.wisc.edu/~ece533/images/cat.png"
          },
          "subtitle": "Subtitle One",
          "title": "Item One"
        },
        {
          "action": {
            "message": {
              "command": "Pricing",
              "type": "training"
            }
          },
          "body": "Description Two",
          "image": {
            "alt": "Image of a cat",
            "url": "https://homepages.cae.wisc.edu/~ece533/images/cat.png"
          },
          "title": "Item Two"
        }
      ]
    }
  ]
}
```

**carousel**

- title: title for the card
- image (optional): object containing media info
  - url: link to media
  - alt: alt text for accessibility
- body (optional): body text for the card
- subtitle (optional): subtitle for the card
- action
  - [message](#message)
  - [link](#link)
  - [button](#button)

---

### Lists

Lists are another way to visualize an assortment of data. Lists do not use a button action.

```
{
  "botcopy": [
    {
      "list": {
        "items": [
          {
            "action": {
              "link": {
                "target": "_blank",
                "url": "https://botcopy.com"
              }
            },
            "body": "Description One",
            "image": {
              "alt": "Image of a cat",
              "url": "https://homepages.cae.wisc.edu/~ece533/images/cat.png"
            },
            "title": "Item One"
          },
          {
            "action": {
              "link": {
                "message": {
                  "command": "Pricing",
                  "type": "training"
                }
              }
            },
            "body": "Description Two",
            "image": {
              "alt": "Image of a cat",
              "url": "https://homepages.cae.wisc.edu/~ece533/images/cat.png"
            },
            "title": "Item Two"
          }
        ],
        "title": "List Title"
      }
    }
  ]
}
```

**list**

- title: list title shown in header
- items: array of list items
  - image (optional)
    - url: link to media
    - alt: alt text for accessibility
  - title: item title
  - description (optional): description of list item
  - action
    - [message](#message)
    - [link](#link)
