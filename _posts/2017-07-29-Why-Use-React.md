---
published: true
---
The advantages of React are not immediately obvious. Here are a few reasons to use React.

You can always tell how your component will look render by looking at one source file.

Bundling the view and functionality together makes sense. It's easier to articulate the functionality of a self-contained component, rather than switching between HTML and Javascript files.

## Sidenote - What is Flux?

> The concept "Flux" is simply that 
1. your  view triggers an event (say, after user types a name in a text field)
2. that event updates a model
3. then the model triggers an event
4. the view responds to that model's event by re-rendering with the latest data. 
That's it.
This one way data flow / decoupled observer pattern is designed to guarantee that your source of truth always stays in your stores / models. It's a Good Thing™.
