+++
date = "2016-09-25T11:46:25-08:00"
title = "About my first experience with Angular2 and web development tools"
draft = false
description = "Talking about why it is good to be a web developer, complaining about JS fatigue and picking IDE."

tags = [
  "TypeScript",
  "Angular2",
  "Webstrom",
  "vscode",
  ]
+++

It seems like in our days every platform that has user interface is moving towards the WebDevelopment world. Just to give you a quick overview:  

Desktop: [Electron ](http://electron.atom.io/) (Windows, Mac, Linux)  
Web: myriads of options.  
Porting C/C++ to JS: [Emscripten](http://kripken.github.io/emscripten-site/) (useful for games, examples are [here](https://github.com/kripken/emscripten/wiki/Porting-Examples-and-Demos))  
Mobile: [Ionic framework](http://ionicframework.com/) in particular and [Cordova](http://cordova.apache.org/) in general (iOS, Windows UA, Android)  

Probably it is a good time to be a Web Developer. Although you cannot build an app once and run it on every platform, this is probably as close as you can get in terms of "cross-platformness". While JavaScript is the most popular language according to [stack overflow survey](http://stackoverflow.com/research/developer-survey-2016), the tools and ecosystem itself provide the worst development experience I ever had. The npm packages become outdated next week, way too many packages, breaking changes with each update, no well established UnitTesting frameworks (but there are tons of passable ones) and awful debugging experience on top of that.  

 And I decided to dive deeper into this world willingly. I had a couple of reasons and I stand by my choice:  

*   I believe that it will be web technologies diffusing to other fields, not the other way around. My hope that C# can be a browser language of choice fades every day (especially with the introduction of [TypeScript, ](http://www.typescriptlang.org/)but more about it later).
*   I wanted to place myself out of comfort zone. Basically, I wanted to struggle in order to grow as a professional. And, oh boy, struggle I did.

A small historical background: I have 10+ years of professional experience with C++ and C#, but zero with other languages. Out of 10 years, I spent only one without a static code analysis tool. I started with [Visual Assist](http://www.wholetomato.com/) for C++ and when I switched to C# I immediately fell in love with [ReSharper](https://www.jetbrains.com/resharper/) (JetBrains products are amazing). Basically, I'm spoiled by the amazing toolset of .NET world.

That's why I naturally decided to start with [TypeScript](http://www.typescriptlang.org/) (similar to C# in a way) and explore my options of frameworks that support it. [Angular2](https://angular.io/) was an obvious choice because of media presence. I did consider [Aurelia](http://aurelia.io/) for a second, but after reading tons of [negative comments](https://medium.com/hashnode-insights/rob-eisenberg-on-aurelia-and-how-it-stacks-up-against-angular-2-and-react-82721d714449) from its creator, [Rob Eisenberg](https://twitter.com/eisenbergeffect) about Angular2, I decided to explore a more "positive" option.

So I needed an IDE of some sort, and [Visual Studio Code](https://code.visualstudio.com/) was the first thing that came to my mind. By the way, as a Microsoft employee, I feel extremely proud of both products: [Visual Studio Code](https://code.visualstudio.com/) (which is built on [Electron](http://electron.atom.io/)) and TypeScript. Unfortunately, there is no plugin for TypeScript by JetBrains yet, but the existing capabilities of VS Code are quite good as they are. Also, Angular team did a good job writing a plugin for it. That was enough for completing the [Tour of Heroes](https://angular.io/docs/ts/latest/tutorial/) tutorial, but the lack of deeper code analysis made me look for other options.  

It was missing a couple of key features ReSharper has: "Extract Variable","Extract Method", "Change Signature" etc. And on top of that, I had to deal with endless json configuration files that I edited manually. While other people have a natural talent of remembering all command line options and configuration schemas, I belong to the "what was the argument name again?" developers (aka "prefer things with User Interface"). That's when the Google advertising system finally suggested something useful: [WebStorm](https://www.jetbrains.com/webstorm/) from JetBrains. And that was a game changer for me. Just within a couple of days, I was able to accomplish the work which would have taken me at least a week using VS Code. WebStorm does have "Extract whatever" support, but it also has the auto-import of TypeScript files based on usage. So I'm sticking with that IDE for a while.  

Angular2 was an interesting experience. It implements "Template-Model" pattern, but with one key aspect: mixing "Model" and "Controller" into a single entity named "Component". I have mixed feelings about it: it is simple enough to get started quickly, but is it powerful enough to keep me going? When I was working for [Nival](http://en.nival.com/) on [PrimeWorld](http://en.playpw.com/) project I participated in the creation of two User Interface frameworks that were used internally and had a similar concept. Make a coupling between the visual representation of an entity and the entity itself: HeroButton knows that it shows an instance of Hero and holds a reference to it. And when you do need the flexibility of showing different things with a single View implement a Model<->View pattern manually. Example: scrollable containers, virtual space containers etc. While this might not be the most common way of doing things, it was nice to feel "home" in the alien world of Web Development.  

Of course, TypeScript was the light that kept me moving forward in the shadow land of dynamic typing. I was actually thinking about trying to join the team of TypeScript, but the lack of JavaScript experience kept me out of applying for a position. I do love TypeScript and it seems to be the signal that the tools around JavaScript matured enough to make developers with backend experience move to the Web Development world. Intellisense, "find usages", reliable and powerful refactoring and design patterns from other languages are all available now, thanks to TypeScript. I cannot stress enough how important it is for a developer like me to find a way to apply years of experience to a completely different ecosystem and avoid mastering it from scratch. And again, VS Code and WebStorm were totally amazing at providing a development experience similar to VisualStudio + ReSharper toolset.  

That wraps up my initial experience of developing a web app which currently looks like this:  

[![SlopFlow-v0.01](/post/2016-09/about-my-first-experience-with-angular2/SlopFlow-v0.01.png)](/post/2016-08/about-nodes-implementation-and-angular2/SlopFlow-v0.01.png)

Sources can be found [here](https://github.com/twop/SlopFlow.Editor) (https://github.com/twop/SlopFlow.Editor).  