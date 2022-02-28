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
The names of files and directories are rather descriptive. For example, folder src holds the main source files and spec keeps testing code of basically all components. And file names are easy to understand at a glance since they are named as 'component-status' although files that set up a component cannot be put in a folder.
However, it allows programmers to use relative paths since all files that call each other to work are put in the same directory.
And like atom/atom repository, it only contains things to do with core part of basic text editing which means it seperates packages like apm into another and relates by a link to that repository.
It has a detailed README.md for for the mian repository but lack explaination for other important folders.

(TBC...)

## Applied Perspective
In this project, we will be focusing on **Usability Perspective** when analyzing the architecture of Atom. 

The desired quality of Usability Perspective is “the ease with which people who interact with the system can work effectively". (Rozenski)

If you are an UI/UX designer, this may seem crazy to you that software architecture also has to consider Usability, not from a human interaction design perspective that focus on the User Interfaces where people see on the front-end, but mainly focus on the ease of user and developer (including people who maintain the system) to interact with the system architecture effectively that contribute to the usability of the overall system. 

### Concerns
<ul>
<li>The first concern relevant to Atom system architecture is how well the <strong>perceived usability</strong> is. The instances we will be focusing on for this concern is the use of asynchronous operation of the system because for code editor like Atom, we want the use to: 1. receive the transactions returned before it has completed in the database, “providing significant improvements in observed response time”. (Rozenski) 2. operate local systems that continue in offline mode, meaning that users are able to use the editor even if the network is off. </li>
<li>Furthermore, not only for Atom but most software systems need to consider how easy it is for changing the user interfaces because according to Rozenski, “<strong>the difficulty of changing user interfaces</strong> can impact system’s usability”. To make changing the UI easier, we need to consider if the Atom system has a modification friendly architecture so the developers and maintainers could easily make routine modifications based on user feedback, especially Atom is maintained by the Gtihub community, so it is crucial to evaluate how ease for the community to change the UI on Atom system. </li>
<li>The third perspective we will consider in the Atom system is the <strong>simple process flow</strong>. Since Atom involves different types of users, including companies, developers, students, etc. that are doing different tasks, meanwhile each task may involve complex and multiple steps, lile preview, terminal, opening files and folders, coding… Therefore, the “process flow around the system should be simple, understandable, and consistent”. (Rozenski)  By checking the system process flow, we will ensure that Atom is sufficient and straightforward for users to complete the tasks.</li>
</ul>

### Activities To Apply The Perspective
<ol>
<li>Interface evaluation</li>
Identify places where users interact with the system(maybe using our UML diagram) and how users will interact at this point. 
Then focusing on the perceived usability concern, specifically how the system return responses to the user and checking for asynchronous working each point if applicable. 
Understanding the internal system and public interfaces, and how easy it is to modify the user interfaces in the system. 
<li>Particiatory design</li>
Understand different user’s capability of using the system and what their needs are. 
Then based on their needs, find out the simplicity of the process flow for different users.
</ol>

## Styles & Patterns
 1. Singleton: 485  
    Context: As a markdown editor, users will likely open multiple different files at once with Atom, this will mean launching multiple different instances of Atom.
    Problem: Launching multiple separate instances of Atom will be very intensive system resources and doesn't make sense to do once Atom has already been initialized once. Therefore, going through the whole start-up process each time does not make sense.
    Solution: Make the Atom process act as a singleton.
    The function at line 485 creates a local server to listen for other Atom launches on the device, if another instance is launched, they will pass all of their data to the first instance and then close, in effect merging the two. Although not a conventional implementation of the Singleton pattern, which normally uses variables to track if it has already been instantiated. This function in effect makes the Atom instance a singleton by closing all other instances of itself and taking on their data before they have gotten too far in the startup process.


## References:
Rozenski, Nick. "Software Systems Architecture: Working with Stakeholders Using Viewpoints and Perspectives." Second Edition, Chapter 29, Addison-Wesly Professional, 2012, https://learning.oreilly.com/library/view/software-systems-architecture/9780132906135/ch29.html


