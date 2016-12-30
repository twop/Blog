+++
date = "2016-04-15T11:46:25-08:00"
title = "Visual Programming - Engine Prototype and Design thoughts"
draft = false
description = "Talking about Flow usecases."

tags = [
  "C#",
  "SlopFlow",
  ]

+++

In the [last]({{< ref "post/2016-03/visual-programming.md" >}}) post, I talked about the concept of Visual Programming. Today I want to share my design ideas for the tool and post a little bit of code from the prototype. I came up with a name: SlopFlow. While you can find a lot of tools with a "flow" part in them, I think it is not an originality contest, right? You can find it [here](https://github.com/twop/SlopFlow).  

#### Things I want to achieve with the project:  
* JSON data format for describing graphs. It should be universal and simple enough to generate code on any platform while being readable and accessible in notepad.
* Modular. I want to make it possible to rewrite any part of the tool manually. Basically, provide a set of tools that can be combined together to form a "real" application.
* Extendable. I want to provide an implementation of code generation in one language initially (preferably C#), but make it possible to reuse the same data format for other languages as well (C++, JS/TypeScript).
* Provide two versions of graph editing experience: web and desktop. At the moment, I'm toying with [electron](http://electron.atom.io/) platform to reuse the code and make it cross-platform from the start.

#### Prototype
I usually tend to start the design of a system from a final user perspective: who, why and how people are going to use it? For the project, I decided to start from a developer's perspective who is going to integrate a generated result to the system. The task itself is nothing too fancy: calculate a sum of 4 numbers which can be represented by a graph of 3 sum2 elements:  

[![sum4](/post/2016-04/visual-programming-engine-prototype/sum4.png)](/post/2016-04/visual-programming-engine-prototype/sum4.png)

Which can be used like this:  

```csharp
[Test]
public void Run_SetsValueToOutput()
{
  var sum4 = new Sum4();
  sum4.Run(1,1,1,1);

  Assert.AreEqual(4, sum4.ResultNode_Result.Value);
}
```

Or if we are interested in a set of events triggering execution:  

```csharp
[Test]
public void SetValueToAll4Inputs_SetsValueToOutput()
{
  var sum4 = new Sum4();
  sum4.InputNode1_Val1.SetValueDirectly(1);
  sum4.InputNode1_Val2.SetValueDirectly(1);
  sum4.InputNode2_Val1.SetValueDirectly(1);
  sum4.InputNode2_Val2.SetValueDirectly(1);

  Assert.AreEqual(4, sum4.ResultNode_Result.Value);
}
```

*as you can see I'm lacking nickname support for flow ports.*  

These are UnitTests that I came up with while trying to understand the usage from a final user perspective. I have two use cases in mind:  

* We have a complete set of data required to execute a flow. Basically, it is similar to calling a function.
* We have a set of events happening randomly and we want to execute a flow only when we have all inputs filled. Example: inputs are connected to another flow's output.

There is also an interesting point that should be addressed separately: if flow should be considered as a data or code? You can rephrase that question: who is the final consumer of the flow a designer or developer? Let's take a closer look at this example:  

FireBall:
[![sum4](/post/2016-04/visual-programming-engine-prototype/FireBall.png)](/post/2016-04/visual-programming-engine-prototype/FireBall.png)

Clearly, a designer is responsible for declaring the wizard unit as well setting up the numbers. He probably should have designed the flow too. The unit is pure data, while the flow is a generic logic (code) that can be reused for other types of units too. How you do you spawn a wizard and connect a flow to the unit's stats and actually cast a fireball? This is something that I'm also fascinated with, but I want to make it clear: this is outside the scope of the project. A flow is compiled to a pure source file, and it is up to a developer to make sure this fireball gets cast. **That makes a designer the creator of the flow, but it's a developer who should be considered as a flow's consumer**.  

This is how a generated code for Sum4 looks like:  

```csharp
public class Sum4
{
  #region Nodes
  private readonly Sum InputNode1 = new Sum();
  private readonly Sum InputNode2 = new Sum();
  private readonly Sum ResultNode = new Sum();
  #endregion

  #region Input
  public Input<int> InputNode1_Val1 => InputNode1.Val1;
  public Input<int> InputNode1_Val2 => InputNode1.Val2;
  public Input<int> InputNode2_Val1 => InputNode2.Val1;
  public Input<int> InputNode2_Val2 => InputNode2.Val2;
  #endregion

  #region Output
  public Output<int> ResultNode_Result => ResultNode.Result;
  #endregion

  public Sum4()
  {
    ResultNode.Val1.SetDependency(InputNode1.Result);
    ResultNode.Val2.SetDependency(InputNode2.Result);
  }

  public void Run ( int argInputNode1_Val1, int argInputNode1_Val2, int argInputNode2_Val1, int argInputNode2_Val2 )
  {
    InputNode1.Val1.SetValueDirectly(argInputNode1_Val1);
    InputNode1.Val2.SetValueDirectly(argInputNode1_Val2);
    InputNode2.Val1.SetValueDirectly(argInputNode2_Val1);
    InputNode2.Val2.SetValueDirectly(argInputNode2_Val2);
  }
}
```

*I know that I can do a better job at naming flow's ports.*  

The flow can be divided into several parts:  

*   Nodes initialization. Note that they are not exposed to outside world.
*   Publicly exposed inputs of the flow represented by the corresponding node's inputs.
*   Same for output.
*   Constructor that glues nodes together. In other words, it creates a directed graph. 
*   Generated "Run" method. Not 100% sure that it is necessary, though.

*Worth mentioning: currently a generated flow doesn't have a base class, and I want to try to keep it that way for simplicity purpose.*

#### Short summary  
We looked at potential usages of flows: function-like and event-based. Flows should be treated as source code and it is up to developers to execute them: make that code accessible from data editor or call it directly. Flows can be combined with each other to produce a more complex logic.  

In this post, I'm not going to cover Node implementation (Sum class), but I will provide implementation details in the next one, hopefully.  

+  Thanks [Shannon](https://www.facebook.com/shannon.mckeon1) for grammar check!