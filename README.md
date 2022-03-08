# INFO 443 Project - Atom
_by Yilin Chen, Jossie Choi, Debbie Dai and Stephan Rubalcava_

## Brief Description
Atom is a text editor that is unique in that it supports markdown and javascript plugins. These allow for users to do much more than conventional text editors. It also enables user to work directly with Git and Github.

Mainly Developed by GitHub, Atom is a desktop application built using web technologies. But it is also community-built and maintained. This project is not only maintained by GitHub but also their community, with an upwards of 400+ contributors that are active to this day.

You can find more information about the overall structure of Atom at [Atom and Packages](https://github.com/atom/atom/blob/master/CONTRIBUTING.md) and more details at [Atom Flight Manual](https://flight-manual.atom.io/). And [atom/atom](https://github.com/atom/atom) is the repository we claim to analyze.

 ## Development View
atom/atom is the core repository that responsible for basic text editing.

<img src="img/UML_Component_Diagram.jpeg" alt="UML Diagram" width=50% height=50%>
<em>Figure 1: UML Component Diagram</em>
<p>&nbsp;</p>

### Dependency Identity
Begin from the left of Figure (Figure 1: UML Diagram),
Menu component, which handles thing in application menu, requires packageManager that is used to coordinate the lifecycle of Atom packages to set up. Package component contains core packages that help to build the system.
It provides packageManager interface to Menu and Text Editor. And it requires grammarRegistry provided by Grammar component, which contains one or more grammars, and styleManager from Style Component, which can query and observe the set of active styles. Notification is a component that hold notices for instructions and it provide notifivationManager, used to create notifications to be shown to the user, to Package、Pane、and Environment component. The main function of Deserializer component is covert data and paralle interface in both way. Here, it gives out deserializerManager to Package、Pane、and Environment component. Providing paneContainer to Workspce only and requiring viewRegistry from View and applicationDelegate from Delegate, Pane is used to help user spliting the pane for better texting. And paneContainer presents content in the center of the workspace. View deal with the data presentation and interaction from user. And View componet, the viewRegistry handles the connection betweem model and view. It provides viewRegistry to Package、Pane and Workspace. In addtion, Workspace shows the status of window of user interface. Back to View, it receive applicationEnvironment of Environment component that indirectly charge main components like Window, Menu and so on. Except components mentioned before, Environment also provide interface to Window. Window itself presents the open page of Atom. It requires applicationEnvironment of Environment and applicationDelegate of Delegate. Finally Delegate implements events and callbacks like event listener. And it provides applicationDelegate to Pane、Window and Environment. Last but not least, is involves Electron, a framework help to build app, from outside package.

### Codeline Model
(TBC...)


## Styles & Patterns
###Styles
1.  Pipe and filter: main > Start  > application > initialize application window > Atom-environment
  a. The startup process utilizes the pipe and filter style where the filters are the different classes in the sequence. The chain of startup starts with src/main-process/main.js which gets the values of the resourcePath(where everything is located), devResourcePath(where the github repo is), and the startTime. These values are all piped into to start.js which normalizes them and adds the path of the file that the user is editing, it then pipes the values to atom-application.js. This gets information about the current instance of the application and pipes the information about the current window into multiple files, the main one being src/initialize-application-window.js which uses the data to display the application to the user and is the end of the pipe.

2. Model view Controller
  a. The main component of the application can be best described as a Model-View-Controller. The model in this instance is the file that is being viewed/edited by the user which is not a part of the core atom system. The view is the process in atom-environent.js, this file is called on by initialize-application-window.js which uses a combination of other files to display all of the components that the user sees when running atom. Finally, the controller is the text editor (text-editor.js) which modifies the text file directly.
  This isn't a pure MVC application because the model is only viewed through the text editor, but atom-environment.js displays the text editor along with other information, so it could be considered the view, but it could go either way.

###Patterns
1. Singleton: Atom-Application 485  
  Context: As a markdown editor, users will likely open multiple different files with Atom, this will mean launching multiple different instances of Atom.

  Problem: Launching multiple separate instances of Atom will be very intensive system resources, especially for what is achievable in a singular instance of Atom.

  Solution: Make the Atom process act as a singleton.
  The function at line 485 creates a local server to listen for other Atom launches on the device, if another instance is launched, they will pass all of their data to the first instance and then close, in effect merging the two. Although not a conventional implementation of the Singleton pattern, which normally uses variables to track if it has already been instantiated. This function in effect makes the Atom instance a singleton by closing all other instances of itself and taking on their data.

2. Atom Environment 1060 factory
  Context: Atom stores information about the version and other factors related to the program after startup, this information is serialized in JSON format.

  Problem: When sending information to/from API endpoints converting regular data into JSON objects can require a lot of extra code. This is especially the case in large programs such as Atom which often interact with large complex objects that are pulled from the users device. These can be hard to read through to decide what information is needed.

  Solution: Using the factory pattern to create a method which creates JSON objects from an inputted value can help provide an easy method for creating an object with only necessary data. Having a standard format for transmitting this data can be very useful as every time the object that is created is called it will have the same data.

3. Text editor 356 decorator
  Context: In Atom the line where the cursor is, is styled differently than the other lines in order to help the user see where they will be typing more easily.

  Problem: Creating an entirely new object for the highlighted cursor line would require a lot of "smelly" code and would likely decrease run time along with inviting a variety of other problems.

  Solution: The function "decorateCursorLine" applies a variety of decorator objects (both literally and in the design pattern sense) on top of the already existing cursor line object. This is a good example of the decorator pattern because it is not applying all of the styling at once, but rather applying 3 different layers of styling on top of the already existing cursor instance.

4. Text-editor 1232 factory
  Context: The ability to copy text is integral for any text editor, it may seem as simple as just duplicating the text but it is more complex in an application like this.

  Problem: In atom text has more properties than just the ascii value of the text being copied, there has a lot more data being transmitted in order for the formatting to be correct.

  Solution: An implementation of the factory pattern in the copy() method. This creates a new TextEditor object and stores the copied data in there including any formatting and background information that is needed for it to be properly pasted within another location in atom. This helps the copying and cutting operations have a more consistent output so that the pasting option can work better.
