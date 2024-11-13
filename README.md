# ai-read-picture
```
const axios = require('axios');  
const fs=require('fs')





  
const API_KEY = ''; // Replace with your actual API key  
const ENDPOINT = ''
  
// Headers for the request  
const headers = {  
    'Content-Type': 'application/json',  
    'api-key': API_KEY,  
};



const cadLink=JSON.parse(fs.readFileSync('../allCADImages.json','utf-8'))[1]['imageUrl']
console.log(cadLink)


const payload = {
  "messages": [
    {
        "role": "system", 
        "content": "You are a senior mechanical engineer." 
    },
    {
        "role": "user", 
        "content": [
          {
              "type": "text",
              "text": "As an experienced mechanical engineer, you need to analyze the drawings of machined components for a non-standard automated assembly production line. Carefully examine this 2D drawing and describe it in at least 300 words. Additionally, speculate on several possible uses for this component and rate your confidence in each speculation using a number between 1 and 10. Each usage and confidence should be formatted as {usage:'work piece carrier', confidence:9}. Place all speculations into an array. The description of the analysis should be output as 2d_description, and the function speculations should be output as possible_usages."
          },
          {
              "type": "image_url",
              "image_url": {
                    "url": ``
              }
            } 
      ] 
    }
  ],
  "temperature": 0.7,
  "top_p": 0.95,
  "max_tokens": 16384,
  "response_format": {
            "type": "json_schema",
            "json_schema": {
              "name": "2d_drawing_description",
              "schema": {
                "type": "object",
                "properties": {
                  "2d_description": {
                    "type": "string"
                  },
                  "possible_usages": {
                    "type": "array",
                    "items":{
                      "type":"object",
                      "properties":{
                        "usage":{
                          "type":"string"
                        },
                        "confidence":{
                          "type":"number"
                        }
                      },
                      "required": [
                        "usage",
                        "confidence"
                      ]
                    }
                  }
                },
                "required": [
                  "2d_description",
                  "possible_usages"
                ]
              }
            }
    }
}  


  axios.post(ENDPOINT, payload, { headers })  
  .then(response => {  
      console.log(response.data)
      // fs.writeFileSync(`schema.json`, JSON.stringify(response))
      // console.log('hi')

// Split the HTTP response to get the JSON part  
const jsonStartIndex = response.data.indexOf('\r\n\r\n') + 4;  
const jsonString = response.data.substring(jsonStartIndex);  
  
// Parse the JSON  
const jsonData = JSON.parse(jsonString);  
  
// Extract the SQL query  
const sqlString = JSON.parse(jsonData.choices[0].message.content);  
  
console.log({sqlString, image:cadLink}); // Output: SELECT COUNT(*) FROM bmg_palletizer;

fs.writeFileSync('cad_description_1.json',JSON.stringify({sqlString, image:cadLink}))

  })

```
  
