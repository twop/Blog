+++
date = "2016-12-26T11:46:25-08:00"
title = "Angular2 first steps"
draft = false

tags = [
  "angular2",
  "typescript",
  "slopflow.editor",
  ]

+++

# Doing first steps in angular

In this post, I will share my learning curve of [angular2](https://angular.io/) and how to manage application state. As a small reminder: I have almost zero professional experience in JavaScript and web front-end, in general, so my overall learning curve includes a lot of js ecosystem ~~struggling~~ findings.


## Tour of heroes

The first step for me was the [Tour of heroes](https://angular.io/docs/ts/latest/tutorial/) tutorial. And it was awesome. Unfortunately, I haven't finished the routing chapter because it was not working at the time, it seems that it was in the middle of rework of some sort. So [routing](https://angular.io/docs/ts/latest/guide/router.html) is on my endless list of "things to learn". 

I came from a C#/C++ background of doing things and it was almost natural for me to start with [TypeScript](https://www.typescriptlang.org/). It did make my learning curve way smoother, and it actually was the key factor why I picked angular2 to start my front-end exploration.

I do recommend the tutorial for everybody new to the ecosystem. The [Quick Start guide](https://angular.io/docs/ts/latest/quickstart.html) was super helpful, its [repo](https://github.com/angular/quickstart) also contains unit testing boilerplate which is totally awesome (it took me a couple of evenings to run my first karma/jasmine test). Unfortunately, it uses [systemjs](https://github.com/systemjs/systemjs) instead of [webpack](https://webpack.github.io/) which gave me a lot of extra work to do, since the majority of relevant samples use webpack which doesn't even remotely look close to systemjs in terms of project structure.

One of my goals was to understand how to organize the state of an application in angular:

```js
@Injectable()
export class HeroService {
  getHeroes(): Hero[] {
    return HEROES;
  }
```

There are a couple of really cool concepts built-in that example:
* It is decoupled from a view.
* It takes advantage of [angular DI](https://angular.io/docs/ts/latest/cookbook/dependency-injection.html) to be easily reusable everywhere.

While I do appreciate the flexibility it is not opinionated enough to force me into any architectural decisions right away. And I fall into the trap of coupling state and behavior in my initial version.


## Applying it to [SlopFlow](https://github.com/twop/SlopFlow.Editor)

The project aims to be a web editor for flow based visual programming. Where **Node** is a logic primitive of building **Flows** which can be used in other high order flows. Example: flow that calculates a sum of 4 numbers can be represented as graph that consists of 3 **sum2** nodes.

```
num1
     \
num2 - sum2 \
              sum2 - result
num3 - sum2 /
     / 
num4
```

The editor has to have a rich set of features including undo/redo and drag&drop with a desktop-like user experience. That's why I think angular2 is a good fit.


## Model

I knew I wanted to decouple presentational layer from the logic but where do you draw the line? What if I have a logic of building layouts? Where should Drag&Drop handling go? 

So I decided to build UnitTestable core of the application almost agnostic to angular utilizing only DI and then move to UI. I started with the following model:

A generic port can be described as: 

```js
export interface IPort
{
  name: string;
  dataType: DataType; // int, string, float or MyCustomType
  isInput : boolean;
}
```

Any element can be described as:

```js
export interface INode // Flow and Node will implement it
{
  name: string;
  inputs: IPort[];
  outputs: IPort[];
}
```

Node implementation:

```js
export class Node implements INode
{
  constructor(public name: string) { }

  inputs: IPort[] = [];
  outputs: IPort[] = [];
}
```

With Flow, it is a little bit trickier because it can have nested elements of type Flow and Node. You can also have multiple elements of the same type within Flow, in other words **instances** of templates:

```js
export class NodeInstance implements INode
{
  constructor(public name: string, public node:INode) {}

  inputs: IPort[];
  outputs: IPort[];
}

// Ports should be created from the template using wrapPort:
const wrapPort = (port: IPort) => {name: port.name, dataType: port.dataType, isInput: port.isInput};

export class Flow implements INode
{
  constructor(public name:string) {}

  inputs: IPort[] = [];
  outputs: IPort[] = [];

  nodes: NodeInstance[] = [];
  links: PortLink[] = [];
}

export class PortLink
{
  constructor(
    public fromNode: NodeInstance,
    public fromPort: IPort,
    public toNode: NodeInstance,
    public toPort: IPort)
  {}
}
```

Root object:

```js
export class Scene
{
  activeWorkspace: Workspace = null;

  nodes: Node[] = [];
  nodeWorkspaces: NodeWorkspace[] = [];

  flows:Flow[] = [];
  flowWorkspaces: FlowWorkspace[] = []; 

  // handling scene commands: adding/removing Node/Flow
}
```

Note that neither of them has any sort of id and all relationships are represented by references. Each individual component is sufficient to describe its own state which makes component traversal super easy. After reading about [smart angular2 detection](http://blog.angular-university.io/how-does-angular-2-change-detection-really-work/) I thought that I didn't need any sort of normalization and could get away with just modifying objects directly.

There are several downsides:
* There is no way to build a route to the Flow in such model.
* It is really hard to flatten the state to serialize/deserialize from a DB. 
* When do I rebuild **NodeInstance** if the underlying template changes?

Angular2 change detection worked like magic and for a while I was able to make progress without any obstacles.

```js
@Component()
export class AssetsComponent 
{
  constructor(public scene: Scene) { }
  // implementation of activateWorkspace
}
```

```html
<div class="card">
  <div class="card-block">
    <span class="my-panel-header">Nodes</span>
       <ul class="nodeList">
      <li *ngFor="let workspace of scene.getNodeWorkspaces()" [class.selected]="workspace === scene.activeWorkspace"
          (click)="activateWorkspace(workspace)">
        <span class="type-marker node-marker">&lt;N&gt;</span><span class="vertical-middle">{{workspace.name}}</span>
      </li>
    </ul>
  </div>
</div>
```
```let workspace of scene.getNodeWorkspaces() + {{workspace.name}}``` worked just fine detecting node renaming.

## Modifying state

It is natural to have undo/redo pattern in an editor. So any changes related to a Flow/Node have to be undoable and scoped to an entity (each entity has its own history). There are two general approaches:

* Command pattern. Each command has enough information to change state and revert it back. 
* Keep the full history of object states. It takes more space but makes adding features a breeze: you don't need to think about reverting changes it is essentially "free". 

I decided to implement Command pattern:

```js
export interface IWorkSpaceCommand
{
  Execute(workspace: Workspace, logger: Log): void;
  Revert(workspace: Workspace, logger: Log): void;
}

export abstract class Workspace
{
  private undoCommands: IWorkSpaceCommand[] = [];
  private redoCommands: IWorkSpaceCommand[] = [];

  canRedo = (): boolean => this.redoCommands.length > 0;
  canUndo = (): boolean => this.undoCommands.length > 0;

  undo(): void
  {
    if (!this.canUndo())
      return;

    var command = this.undoCommands.pop();
    this.revertCommandInternal(command);
    this.redoCommands.push(command);
  }

  redo(): void
  {
    if (!this.canRedo())
      return;

    var command = this.redoCommands.pop();
    this.executeCommandInternal(command);
    this.undoCommands.push(command);
  }

  protected abstract onModifiedInternal(): void;

  private executeCommandInternal(command: IWorkSpaceCommand): void
  {
    command.Execute(this, this.log);
    this.onModifiedInternal();
    this.modified.emit(this);
  }

  private revertCommandInternal(command: IWorkSpaceCommand): void
  {
    command.Revert(this, this.log);
    this.onModifiedInternal();
    this.modified.emit(this);
  }
```

It was relatively fast to add basic commands:

```js
export class NewPortCommand implements IWorkSpaceCommand
{
  constructor(private port: IPort) {}

  Execute(workspace: Workspace, logger: Log): void
  {
    workspace.addPort( this.port ); //actual implementation in the workspace.
    logger.debug(`added port ${this.port.name} in ${workspace.name}`);
  }

  Revert(workspace: Workspace, logger: Log): void
  {
    workspace.removePort( this.port);
    logger.debug(`removed port ${this.port.name} in ${workspace.name}`);
  }
}

export class Workspace
{
  addPort(port: NodePort): void
  {
    this.node.ports.push(port);
  }

  removePort(port: NodePort): void
  {
    const index = this.node.ports.indexOf(port, 0);
    if (index > -1)
      ports.splice(index, 1);
  }
}
```

So in order to get a new piece of functionality I need to do the following:
* Write business logic in the workspace class which will modify the model (don't forget to support undo).
* Wrap that into **IWorkSpaceCommand**.
* Think about command validation.
* Think about propagating changes (at what point do I rebuild the node layout?).

So I stopped adding features after I had the following folder Structure:

```
Scene:
| - workspace.ts
| ...
| - Commands:
|| - deletePortCommand.ts
|| - editNodeCommand.ts
|| - editPortCommand.ts
|| - newFlowCommand.ts
|| - newNodeCommand.ts
|| - sceneCommand.ts
|| - workspaceCommand.ts
|| - ...
```

Even though I have just one place to implement business logic and the entire state is represented by a single object (aka "single source of truth") that approach just doesn't scale well. Too much boilerplate. And that's when I decided to rewrite the application using [redux](http://redux.js.org/). It has "free" undo/redo support and it is very opinionated on how the app state should be managed. I'm going to talk about my experience redux+angular2 in the next posts.

## In conclusion

Angular2 was quite easy to start with (tutorial was really good). It doesn't force you into a particular design pattern(I wish it would) but OOP approach seems like a good fit initially which was actually a trap for me. I think if your Component has a lot of logic you are doing it wrong.

