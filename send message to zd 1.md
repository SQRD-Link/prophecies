{{ JSON.stringify({
  "author": {
    "type": "business",
    "avatarUrl": "https://cloud86.io/noa-avatar.png",
    "displayName": "Noa"
  },
  "content": {
    "type": "text",
    "markdownText": $json.output 
  }
}) }}


message 2

{{ JSON.stringify({
  "author": {
    "type": "business",
    "avatarUrl": "https://cloud86.io/noa-avatar.png",
    "displayName": "Noa"
  },
  "content": {
    "type": "text",
    "markdownText": $json.content ? $json.content : $json.output 
  }
}) }}