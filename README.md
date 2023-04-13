# ai-price-monitoring
Samples and code snippets for the AI price monitoring article

//////////////////////////////
//////////////////////////////
CODE1

const scrapingbee = require('scrapingbee');
var sbApiKey = 'your ScrapingBee API key goes here';

async function getHTML(url) {
	var client = new scrapingbee.ScrapingBeeClient(sbApiKey);
	var response = await client.get({
		url: url,
		params: {
			// No parameters to use
		},
	});

	//the HTML code is here
	console.log(response.data);
	return response
}

getHTML('https://htmlpreview.github.io/?https://github.com/rochesterj/ai-price-monitoring/blob/main/01.html')

//////////////////////////////
//////////////////////////////
CODE 2

const { Configuration, OpenAIApi } = require("openai");

const configuration = new Configuration({
  apiKey: 'your OpenAI API key goes here',
});
const openai = new OpenAIApi(configuration);

async function getPath( html ) {
	const completion = await openai.createChatCompletion({
	  model: "gpt-3.5-turbo",
	  messages: [
	  	{ role: "system", content: "You are a helpful assistant that finds the full xpath (from html to the element) for the product price in the prompt and returns only the complete xpath   ( no further explanations, no notes or additional text ). You can give the same answer multiple times."},
	  	{ role: "user", content: html }
	  ],
	});
	console.log(completion.data.choices[0].message);

	return completion.data.choices[0].message.content
}


////////////////////////////
////////////////////////////
Code 3

getHTML('https://htmlpreview.github.io/?https://github.com/rochesterj/ai-price-monitoring/blob/main/01.html').then(async function (response) {
    var decoder = new TextDecoder();
    var html = decoder.decode(response.data);

    var path = await getPath(html);
    console.log(path);
    
}).catch((e) => console.log('Oops : ' + e.response.data));
////////////////////////////
////////////////////////////

Code 4
/** 001.html **/
/html/body/main/div[2]/p[@class='price']

/** 002.html **/
/html/body/main/div[2]/p[1]

/** 003.html **/
/html/body/main/div[1]/div[2]/p[1]

///////////////////////
///////////////////////
Code 5

const scrapingbee = require('scrapingbee');

var sbApiKey = 'your ScrapingBee API key goes here';

async function getPrice(url, xPath) {
  var client = new scrapingbee.ScrapingBeeClient(sbApiKey);
  var response = await client.get({
    url: url,
    params: { 
        extract_rules: { "price": xPath}, 
    },  
  })
  return response
}


getPrice('https://htmlpreview.github.io/?https://github.com/rochesterj/ai-price-monitoring/blob/main/01.html', "/html/body/main/div[2]/p[@class='price']").then(function (response) {
    var decoder = new TextDecoder();
    var text = decoder.decode(response.data);
    console.log(text);
}).catch((e) => console.log('A problem occurs : ' + e.response.data));
