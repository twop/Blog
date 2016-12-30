+++
date = "2016-03-28T11:46:25-08:00"
title = "Visual programming"
draft = false
description = "Why do we even need it?"

tags = [
  "designers vs programmers",
  ]

+++

Let's start with showing two examples which I like a lot:  

[iCanScript](http://www.icanscript.com/)
[![iCanScript](/post/2016-03/visual-programming/iCanScript.png)](/post/2016-03/visual-programming/iCanScript.png)


[FlowCanvas](http://flowcanvas.paradoxnotion.com/)
[![FlowCanvas](/post/2016-03/visual-programming/flowCanvas.png)](/post/2016-03/visual-programming/flowCanvas.png)

Both of them are implemented as Unity3d extensions. Thus they are mainly used in game development. I mention them - because they look nice and provide an excellent understating of the subject. The idea behind visual programming appeals a lot:  

*   Visual representation of complex logic is way easier to support.
*   You can show a representation to non-programmer (like your manager).
*   Ideally, it's less code to maintain. A visual representation is responsible for interaction between the components.
*   Allows people with non-programming experience to create working prototypes of their ideas. 
*   **Potentially allows separation of responsibilities between professions: programmers provide building blocks and game designers use them to build complex logic **(or other guys responsible for "how it should work").

But still nobody screams that this concept is **the** truth and the only way of software development (like OOP back in the days). So let's mention some downsides:

*   **Usually it applies to a specific platform** (like Unity3d).
*   Introduces a new user experience, which is also specific to implementation. Each new tool will have a "unique" set of "features".
*   Has an influence on product architecture: how and when it can be used/reused. 
*   Can perform significantly slower than "manually" repeated logic, especially if it is interpreted instead of compiled.
*   **Usually has no "design first" concept. All nodes have to be initialized/declared before using.**
*   **Costs money**.
*   **Usually requires programming qualification for using the tool,** which directly contradicts the last upside.

*Accidentally I mentioned more downsides than upsides.*

I can't even hope that I covered everything but let's focus on what I think is the most important. I will go through the points and propose a solution for each of them.  

#### Allows separation of responsibilities between professions.
Lets talk about game development. A designer has a neat idea of how a new "Fireball" spell should work, he goes to a programmer:  
- D: "hey can you code a new spell really quick?"  
- P: "What spell?"  
- D: "New fireball logic. It will do this and this and then "kabooom!". When I will be able to test it?"  
- P: "In 2 days after I get a complete design document."  
- D: "Wut!!?? This is nothing! You should be able to code that in 30 mins...."  

Some of you might recognize this pattern in other aspects of your life. It usually happens when somebody cannot directly implement/prototype/taste his/her ideas and relies on others to get the result. This pattern is annoying for both sides **and** it lowers the overall productivity of the team. In order to prevent that, people invent all sorts of tools, which have a common concept: use data for logic/design concept and code to support the building blocks. Examples: [XAML](https://en.wikipedia.org/wiki/Extensible_Application_Markup_Language) + Blend studio, HTML/CSS/JavaScript, almost all game engines.  

What I want to achieve: a designer goes to a web site, drag'n'drop a couple of nodes (or 10), creates new nodes (if needed), sets up the dependencies between them and only then goes to a programmer:  
- D: "Hey, can you take a look. I created a prototype of the new fireball that we talked about."  
- P: "Looks good to me. I will have to implement a couple of nodes though. But you will be able to test it with the next build, hopefully."  

Implementing a couple of nodes is way easier than listening to a designer tell his or her whole story. After finalizing the logic of the nodes, both of them will be able to work in parallel. If all nodes are in place, a designer can try it out without coding at all.  

#### Usually it applies to a specific platform.
The two examples above are specific to Unity3d (at the bottom you will find more). There is no "framework" that allows you to reuse the logic across several game engines. That is because visual programming is usually just a part of existing pipelines. It just isn't self sufficient as a tool. I'm making a game using Unity3d - let's find out what do you have in store for me; Unreal Engine - [Blueprints](https://docs.unrealengine.com/latest/INT/Engine/Blueprints/index.html); Stringray - [Flow](https://knowledge.autodesk.com/search-result/caas/CloudHelp/cloudhelp/ENU/Stingray-Help/stingray-help/creating-gameplay/visual-prog-with-flow-html.html).  

I may be wrong here, but I do believe that it can be unified and standardized, especially if you need to prototype something. Do I really need to buy an asset in Unity Store to visualize my thinking process, and learn how to integrate it with the platform?  

What I want to achieve: provide a unified data format for graph declaration. Then codegen an implementation on different languages (C++, C#), implement missing nodes and integrate it to the platform as pure source files.  
+ Pay attention to the list I'm giving at the end of the post. Do they look similar to you too?  

#### Usually has no "design first" concept. All nodes have to be initialized/declared before usage.
I think this is huge. As I said, when you need somebody else to implement your ideas, when you rely on someone, who isn't really motivated to help you, when it is out of your reach to do your job, you feel miserable. In my experience, designers and developers are usually in a state of semi war: "they have too many fancy ideas all the time and just don't know what they want" vs. "they are too lazy, why does it take them a week to implement a simple thing, it should be just a couple of lines of code." In my thinking, the root of the problem is that both of these groups' working pipelines are tightly coupled, thus responsibilities start to diffuse.  

In order to minimize this effect, the "design first" concept is essential. Let designers iterate through their ideas fast, with no interventions and dependencies on others. They will produce a much higher quality concept, that won't require any major updates and remakes.  
#### Costs money.  
Even Visual Studio is free nowadays. The industry in general is moving towards service model. While I welcome the change, the truth is that I want to share my thinking process on a good opensource material in my blog. So this is a win-win.  

#### Usually requires programming qualification for using the tool.  
A usual entry barrier for the platform, not even mentioning the tool, is high enough. You have to set up an environment, place some game objects, start a visual flow, connect them all together and only then iterate through your ideas. The complexity and iteration speed varies from platform to platform and from tool to tool, but it is usually not good enough for prototyping.  

Another aspect is the tool itself. How deeply do you have to dive into details? iCanScript can provide nodes starting from "compare two numbers". My point is - that the tool should be accessible, intuitive and hide enough implementation details to be easy to use for people without programming experience.  

What I want to achieve: a simple web site. I do believe that opening a link is the lowest entry barrier possible, and sending a link is the easiest way to share.  

So these are things I want to solve in my project. I don't have a name yet. So any suggestions are welcome (my variants are: FluffyNode, UniFlow, SlippyFlow + other combinations of "flow" and "node" )  

P.S. Just a list of tools I found during my research:  

This is a nice one. Works with graphics via graph of transformation nodes.
[PixaFlux](http://www.pixaflux.com/Docs/Ui_Viewport_Node_Graph.html)
[![FlowCanvas](/post/2016-03/visual-programming/PixaFlux.png)](/post/2016-03/visual-programming/PixaFlux.png)

These guys are just next level. They generate web apps using graphs!
[outsystems](https://www.outsystems.com/)
[![FlowCanvas](/post/2016-03/visual-programming/outsystems.png)](/post/2016-03/visual-programming/outsystems.png)

Visualization of state machine.
[playMaker](http://www.hutonggames.com/index.html)
[![FlowCanvas](/post/2016-03/visual-programming/playMaker.png)](/post/2016-03/visual-programming/playMaker.png)

Event based graph.
[flow](https://knowledge.autodesk.com/search-result/caas/CloudHelp/cloudhelp/ENU/Stingray-Help/stingray-help/creating-gameplay/visual-prog-with-flow-html.html)
[![FlowCanvas](/post/2016-03/visual-programming/flow.png)](/post/2016-03/visual-programming/flow.png)


+ Thanks [Shannon](https://www.facebook.com/shannon.mckeon1) for grammar check!  