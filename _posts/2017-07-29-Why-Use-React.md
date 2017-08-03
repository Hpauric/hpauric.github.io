---
published: true
---
To a beginner, the advantages of React are not immediately obvious. Here are a few reasons to use React.

1. You can always tell how your component will look render by looking at one source file.

	> If you know the state, you know the rendered output. You don't have to trace program flow. When working on complex applications, especially in teams, this is critically important.

2. Bundling the view and functionality together makes sense. It's easier to articulate the functionality of a self-contained component, rather than switching between HTML and Javascript files.

	> UI code is readable and maintainable.
	> Componentized UI is the future of web development, and you need to start doing it now.

## Sidenote - What is Flux?

> The concept "Flux" is simply that 
1. your  view triggers an event (say, after user types a name in a text field)
2. that event updates a model
3. then the model triggers an event
4. the view responds to that model's event by re-rendering with the latest data. 
That's it.
This one way data flow / decoupled observer pattern is designed to guarantee that your source of truth always stays in your stores / models. It's a Good Thing™.
