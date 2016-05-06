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
Entities provide a method of capturing several variations of an input that can be used across multiple input nodes. They are declared in a entity tag using the syntax:
```
<entities>
  <entity name="PERSON" entityType="GENERIC">
    <value name="Andrew" value="Andrew">
      <grammar>
        <item>Andy</item>
      </grammar>
    </value>
    <value name="Joe" value="Joe">
      <grammar>
        <item>Joseph</item>
      </grammar>
    </value>
    <value name="Max" value="Max"/>
    <value name="Anthony" value="Anthony"/>
    <entityRules></entityRules>
  </entity>
</entities>
```
Here, a single entity is defined named PERSON. This entity contains multiple possible values: Andrew, Joe, Max, Anthony. Andrew and Joe both have possible variations of Andy and Joseph. Unlike concepts which work as synonyms throughout the dialog, to match an entity it must be invoked inside an input node. The following code will look for any matches in the PERSON entity and save it to the profile variable PERSON_NAME (described in next section).
```
<input>
  <grammar>
    <item>$(PERSON)={PERSON_NAME}</item>
  </grammar>
  <action varName="PERSON_NAME" operator="SET_TO">{PERSON_NAME.value:main}</action>
</input>
```

#### Profile Variables
Profile variables allow for storing information gained during a conversation. The variables persist throughout the conversation. A variable is defined using the syntax:
```
<variables>
    <var_folder name="Home">
        <var_folder name="PersonName" type="VAR">
            <var name="PERSON_NAME" type="TEXT"/>
        </var_folder>
    </var_folder>
</variables>
```
Here we are creating a profile variable by the name of PERSON_NAME which holds a TEXT value. Other value options are NUMBER, YES/NO, and DATETIME. Profile variables can be set to an initial value or at any point in the conversation. A common practice is to test a condition using an input node then set the variable. For example:
```
<input>
  <grammar>
    <item>$(PERSON)={PERSON_NAME}</item>
  </grammar>
  <action varName="PERSON_NAME" operator="SET_TO">{PERSON_NAME.value:main}</action>
  <output id="output_19">
    <prompt selectionType="RANDOM">
      <item>Sending an email to {PERSON_NAME}. Please enter the body of your email and I'll send it for you.</item>
    </prompt>
  </output>
</input>
```
This input node tests to see if any values of the PERSON entity exists in the input. If so then that entity is stored in the profile variable PERSON_NAME. The action tag will then set the PERSON_NAME variable to the value currently held in PERSON_NAME.value:main field. The value of the variable is then printed in the output node by surrounding the PERSON_NAME profile variable with curly braces.

## 3. Uploading the xml
Open up the website to the dialog tool that you deployed in step 1. You can upload an xml file to your dialog service by clicking on the + button and selecting the .xml file on your hard drive.
## 4. Walking through a conversation
After uploading the xml file, you can use the same website to interact with the newly uploaded dialog. The first output to the screen should be one of the greetings from:
```
<output>
    <prompt selectionType="RANDOM">
        <item>I'm your virtual personal assistant. Is there anything that I can help you with today? Say abort at any time to cancel.</item>
        <item>How can I be of assistance? Say abort at any time to cancel.</item>
    </prompt>
    <getUserInput id="getUserInput_4">
        ...
    </getUserInput>
</output>
```
After the greeting, the conversation halts at the getUserInput node and waits for the input to be sent. Enter the response "I would like to send an email". This input is tested against the input nodes:
```
<input>
  <grammar>
    <item>$ abort</item>
  </grammar>
  <goto ref="output_22"/>
</input>
<input>
  <grammar>
    <item>$ email</item>
  </grammar>
  ...
</input>
```
The test against the value $ abort fails so the flow proceeds to the sibling input node with the value $ email. This test passes and the flow then goes on into the node.
```
<input>
  <grammar>
    <item>$(PERSON)={PERSON_NAME}</item>
  </grammar>
  ...
</input>
<output>
  <prompt selectionType="RANDOM">
    <item>Okay, who would you like to send an email to?</item>
  </prompt>
  <getUserInput id="getUserInput_16">
    ...
  </getUserInput>
</output>
```
After passing the $email input node, the flow proceeds to it's first child node. Here the test against $(PERSON) fails because there was no known contact name in the user's input. The flow then proceeds to the output node where the next prompt is sent to the application. The flow continues to the getUserInput node where it then halts waiting for input. Enter the response "I would like to send an email to Andy".
```
<input>
  <grammar>
    <item>$ abort</item>
  </grammar>
  <goto ref="output_22"/>
</input>
<input>
  <grammar>
    <item>$(PERSON)={PERSON_NAME}</item>
  </grammar>
  <action varName="PERSON_NAME" operator="SET_TO">{PERSON_NAME.value:main}</action>
  <goto ref="output_19"/>
</input>
```
Similar to before, the user input fails the test against the $abort input node and the flow proceeds to the sibling node. The next input node passes because the word Andy is a variant of the value Andrew in the entity PERSON. The value Andrew is assigned to the variable PERSON_NAME and the goto sends the flow to the node with id "output_19".
```
<output id="output_19">
  <prompt selectionType="RANDOM">
    <item>Sending an email to {PERSON_NAME}. Please enter the body of your email and I'll send it for you.</item>
  </prompt>
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
</output>
```
The flow outputs the sending email prompt and halts at the getUserInput node. Once input is received, it's tested against the $ abort input. This fails so the flow proceeds to the output node then to the goto which sends us to "output_22".
```
<output id="output_22">
    <prompt selectionType="RANDOM">
        <item>Is there anything else that I can help you with?</item>
    </prompt>
    <action varName="PERSON_NAME" operator="SET_TO_BLANK"/>
    ...
</output>
```
  Here the user is asked if there is anything else that they need. If the answer is Yes the flow will begins again from the start. Otherwise the conversation ends. One important thing to note here is the action of resetting the variable "PERSON_NAME".

  Why is resetting the variable necessary?
  Trace the flow that happens when you include a person's name with the first prompt (i.e. I'd like to send an email to Joe).
  What happens if the word abort is in the body of the email provided?
## 5. Adding new features
### Meetings
### New Contacts
