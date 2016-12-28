+++
date = "2016-08-28T11:46:25-08:00"
title = "About Nodes implementation and Angular2"
draft = false

tags = [
  "C#",
  "TypeScript",
  "Angular2",
  "SlopFlow",
  ]

+++

[Last time]({{< ref "post/2016-04/visual-programming-engine-prototype.md" >}}) I talked about general design ideas of SlopFlow. Today I wanted to share with you some implementation details of flow nodes in engine part and my struggling with UI part.  

Node is a building block that has Inputs and Outputs. Node has to be implemented manually (outside of the scope of application) but declared via the application.  

Declaration of Sum2:
[![Sum2](/post/2016-08/about-nodes-implementation-and-angular2/Sum2.png)](/post/2016-08/about-nodes-implementation-and-angular2/Sum2.png)


At the moment data flows only in one direction: Signal -> Input -> Output. Which means if you want to get an output you have to trigger all inputs first using Signals. Also, output nodes can be connected to input nodes to form a flow.  

Input:

```csharp
public abstract class InputBase
{
 public event Action<InputBase> ValueRecieved;

 protected void InvokeValueRecieved()
 {
   ValueRecieved?.Invoke(this);
 }
 }

 public class Input<T> : InputBase
 {
 public T Value { get; private set; }

 public void SetValueDirectly (T value)
 {
   Value = value;
   InvokeValueRecieved();
 }

 public void SetDependency(Output<T> output )
 {
   output.Complete += ValueRecievedFromOutuput;
 }

 private void ValueRecievedFromOutuput(T value)
 {
   Value = value;
   InvokeValueRecieved();
 }
}
```

Output:

```csharp
public class Output<T>
{
 public event Action<T> Complete;

 public T Value { get; private set; }

 public void SetValue(T value)
 {
   Value = value;
   FireComplete();
 }

 private void FireComplete()
 {
   Complete?.Invoke(Value);
 }
}
```

Node is just a collection of inputs and outputs:

```csharp
public abstract class Node
{
 private readonly Dictionary<InputBase, bool> _inputs = new Dictionary<InputBase, bool>();

 protected void AddInput(InputBase input)
 {
   input.ValueRecieved += OnInputValueRecieved;
   _inputs[input] = false;
 }

 private void OnInputValueRecieved(InputBase firedInput)
 {
   _inputs[firedInput] = true;
   if (_inputs.Values.Any(fired => fired != true))
  return;

   Execute();
 }

 protected abstract void Execute();
}
```

Node class only has logic for collecting all values from the inputs and calling abstract Execute method.

That is currently generated code for Sum2:

```csharp
// ------------------------------------------------------------------------------
// Auto-generated
// ------------------------------------------------------------------------------
partial class Sum: Node
{
 public readonly Input<int> Val1 = new Input<int>();
 public readonly Input<int> Val2 = new Input<int>();

 public readonly Output<int> Result = new Output<int>();

 public Sum()
 {
  AddInput(Val1);
  AddInput(Val2);
 }
}
```

Note: generated class is partial. Placeholder for Execute implementation is generated once and then has to be manually maintained in a separate file.

Implementation:

```csharp
partial class Sum
{
 protected override void Execute()
 {
  Result.SetValue(Val1.Value + Val2.Value);
 }
}
```

Looks pretty simple to me. And this "Result.SetValue(Val1.Value + Val2.Value);" line of code is the only thing a developer has to write manually.

Another concept of implementing inputs is variables. Instead of waiting for a signal the node _will ask_ for it.  

Reverse direction:
[![Sum2-reverse](/post/2016-08/about-nodes-implementation-and-angular2/Sum2-reverse.png)](/post/2016-08/about-nodes-implementation-and-angular2/Sum2-reverse.png)

In this example when the node receives a signal from Input1 it will immediately ask Input2 to give a value from the object attached to it (hero.health).

This is definitely something I want  to explore in the future but only after the first working prototype.

Back to UI. I have no experience in either JavaScript or HTML/CSS, and one of my goals was to make a web version. That was intentional. In order to grow as professional, you have to put yourself out of comfort zone from time to time. And I can't stress enough how far is web development is out of my comfy zone.

I decided to transition smoothly with [TypeScript](http://www.typescriptlang.org/index.html) and [Angular2](https://angular.io/). I have nothing but good words for TypeScript itself but the environment of JavaScript libraries and Node.js kept me wondering how all these people actually operate. Endless configs, version incompatibilities (I tried to update some libraries a couple of times) and no UI/Wizards to help to go through the process. I tried to develop desktop version first using Electron but failed miserably. Threw everything away and started from the very beginning of [Anguar2 tutorial ](https://angular.io/docs/ts/latest/tutorial/) (which is actually great).

Now I'm here:  

[![UI-prototype](/post/2016-08/about-nodes-implementation-and-angular2/UI-prototype.png)](/post/2016-08/about-nodes-implementation-and-angular2/UI-prototype.png)

Wish me luck.