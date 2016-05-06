## Watson Dialog Tutorial
This guide goes through the basics of using the Watson dialog service.

### 1. Install Dialog Tool
The dialog tool will be used to interface with the dialog service. This tool simplifies the process of adding the XML and conversing with the service. To install, go to the projects github page at https://github.com/watson-developer-cloud/dialog-tool, scroll down, and click the "Deploy to Bluemix" button. Continue on to step 2 while waiting on the project to deploy.

### 2. Understanding the XML

The full guide for the dialog xml is located at https://www.ibm.com/smarterplanet/us/en/ibmwatson/developercloud/doc/dialog/tutorial_tutorials.shtml. Here we present a brief overview of the most commonly used nodes to quickly get you started. Open the dialog.xml file using your preferred xml editor.

#### Basic Structure
An empty dialog xml file will has the structure of:
```
<?xml version="1.0" encoding="UTF-8"?>
<dialog xsi:noNamespaceSchemaLocation="WatsonDialogDocument_1.1.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <flow>
        <folder label="Main"/>
        <folder label="Library"/>
        <folder label="Global"/>
        <folder label="Concepts"/>
    </flow>
    <constants>
        <var_folder name="Home"/>
    </constants>
    <variables>
        <var_folder name="Home"/>
    </variables>
</dialog>
```
Dialog nodes follow the standard xml syntax of having an opening and closing tag. Between these tags, more nodes can be defined as children of the outer node. The conversation flow will proceed from a parent node and into the children nodes. Once the flow enters a node it does not fall back out to the children nodes. This means that once a node is executed the sibling nodes will not be processed.

#### getUserInput node
The getUserInput node will halt the conversation and wait for a response from the user. An example in the xml is:

```
<getUserInput>
    <input>
    <grammar>
    <item>$ abort</item>
    </grammar>
    <goto ref="output_22"/>
    </input>
    <output>
    <prompt selectionType="RANDOM">
    <item>Okay. Email has been sent.</item>
    </prompt>
    <goto ref="output_22"/>
    </output>
</getUserInput>
```
The conversational flow will halt at the getUserInput node waiting for the user input. Once received, the flow will continue into the children and which in this case is the input node.

#### Output node
The output node will respond to the calling application with text then conversation will continue into the children. An example in the xml is:

```
<output>
    <prompt selectionType="RANDOM">
        <item>I'm your virtual personal assistant. Is there anything that I can help you with today? Say abort at any time to cancel.</item>
        <item>How can I be of assistance? Say abort at any time to cancel.</item>
    </prompt>
    ...
</output>        
```
The item tags define the possible outputs which can be sent. The prompt tag is the required parent of the item tags and has an optional parameter named selectionType. This defines the method with which the response is chosen. Possible options are RANDOM and SEQUENTIAL.

#### Input node

The input node tests the user's input to determine whether or not the node will be executed. If the test does not succeed then the flow will continue to the node's sibling.

```
<input>
    <grammar>
        <item>$ abort</item>
    </grammar>
    <goto ref="output_22"/>
</input>
```
The item tags contain the text against which the input will be tested. There can be multiple ones and passing any will result in success. They must be wrapped in a grammar tag. Here, if the input passes the "abort" test, the goto node will be executed.

##### Wildcards
In the input example above, the wildcard $ is used. This wildcard means that the word abort must be in the text but it can be proceeded or following by anything. There is also the * wildcard. This wildcard matches any set of words but there must at least be one word present. Examples:
"I have to * my dog" will be matched by "I have to feed my dog" or "I have to walk my dog".
"I have to walk * my dog" will not be match "I have to walk my dog"
"$ dog" will match any sentence with the word dog
"$ walk * my dog" will match "I have to walk and feed my dog" or "Will you walk and feed my dog for me?" but will not be matched by "I have to walk my dog"

#### Concepts
Concepts work as synonyms throughout the dialog. The code contains the example:
```
<grammar>
    <item>email</item>
    <item>e-mail</item>
    <item>e mail</item>
</grammar>
</concept>
```
This makes it so that any input checking for the word email will also be matched by e mail or e-mail. A common practice is to add a concept for yes and no so that words like yup, yea, and nope are easily handled.

#### Entities

#### Profile Variables

## 3. Uploading the xml
## 4. Walking through a conversation
## 5. Adding new features
### Meetings
### New Contacts
