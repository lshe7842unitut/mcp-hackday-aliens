====
Hyrax
====
Web application automation without coding.

Features
--------

* Fast - on average 3X faster than well organized Selenium based automation scripts, thanks to the headless DOM implementation provided by PhantomJS
* Easy - no IDE and coding required
* Flexible - can be plugged into your existing workflow
* Ideal for regression testing - free web application developers from learning and using complex automation tools so that they can focus on developing high quality codes


How it works
------------
Basically, Hyrax takes a test specification file (*.json), combines it with the built-in template to generate a javascript file which CasperJS can run.


Terminology
-----------
#### Template - ```casper-test.tpl```
This file contains the casperjs test file template where test specifications will be injected.
*This file is provided by Hyrax, and should not be modified by end user.

#### Test specification - ```*.json```
This file contains the test specifications in the form of JSON object.


Gulp
----
At the moment, 2 gulp tasks are provided to run Hyrax.

* ```gulp partial``` - generates casper-test.js file which you can then run with CasperJS manually. This is ideal for troubleshooting issues with your test specification
* ```gulp``` - this default task will first generate casper-test.js, and then run it with CasperJS automatically


How To Write Test Specification
-------------------------------
See [Wiki](https://github.com/lshe7842/Hyrax/wiki) for details on configuration items

Below example demonstrates how to automate a series of user interactions:
* click a button to navigate to the next page;
* search for an username on an autocomplete widget;
* add a found user by clicking on one of the search results.

```json
{
	"testCaseName": "Test case 1",

	"testCaseDescription": "This is a regression test of my web application following happy path.",
	
	"url": "http://myurl.com.au",
	
	"signalOk": "header.myHeader",

	"timeout": 30,

	"actions": [
		{
			"type": "click",
			"target": "button#myButton",
			"after": {
				"signalOk": "#page2",
				"print": "Navigated to page 2.",
				"timeoutMsg": "Timeout when navigating to page 2"
			}
		},

		{
			"type": "input(username)",
			"keepFocus": true,
			"target": "input#my_input",
			"after": {
				"signalOk": "#myResult",
				"signalOkWith": "value(userFullname)",
				"print": "Got search result.",
				"timeoutMsg": "Timeout when searching for username."
			}
		},

		{
			"type": "click",
			"target": "#myResult ul > li",
			"after": {
				"signalOk": "#mySuccessMsg",
				"prints": [
					{
						"string": "Added user with fullname: ",
						"query": "#mySuccessMsg #userFullname"
					}
				],
				"timeoutMsg": "Timeout when adding an user."
			}
		}
	]
}
```

Run Hyrax
---------
### Prerequisites
Install

* NodeJS
* Gulp
* Python 2.x
* PhantomJS 1.9.2
* Casper 1.1.0-beta3

To verify installations, open cmd, cd to root dir of this repo, type ```casperjs env-verify.js```, you should be able to see some success outputs.

Store all your test specifications in ```specs``` folder.

```
gulp --spec=[your_test_specification_name_without_dot_json]
```
Don't make yourself a coffee because you won't need to wait long for test to finish :)


Known Issues
------------
#### Phantomjs crashes occasionally when navigating between pages
It seems that this issue is caused by casperjs API function ```waitFor``` pulling element defined by ```signalOk``` in our test specification file. Try to use ```"waitFor": true``` in the test specification to get around this issue.


Contributors
------------
* Shen Lu
* Guonian Xin


Special Thanks
--------------
* Anand Krishnamurthy
* Ludovic Barbier