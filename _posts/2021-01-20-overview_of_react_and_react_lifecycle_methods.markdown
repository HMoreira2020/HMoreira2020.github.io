---
layout: post
title:      "Overview of React and React Lifecycle Methods"
date:       2021-01-20 18:26:46 +0000
permalink:  overview_of_react_and_react_lifecycle_methods
---


What is React.js?
React.js is a javascript library or framework for creating User Interfaces. It allows us to write our html alongside how we want it to interact with our state and user. React.js is often used as the V or ‘view’ in the MVC pattern. It breaks your application into reusable parts of code called components the same way a website is visually separated into different components. This way you can think of and build your app in pieces according to how each component should present and behave in the browser.
There are Container components that handle the logic/how things work and presentational/function components that handle the presentation/how things look. The components hold html code and javascript logic for listening to user actions (onChange, onSubmit, etc.) and updating the UI without having to reach out to a server to fetch a new view because they can hold state. Normally in javascript everything is event based. You change the state and must hide or show things randomly. React is Declarative. In declarative react you design simple views for each state in your app. Instead of having to select elements on the DOM to hide or show based on certain events, React components have state and will automatically re-render the right components when that state changes or updates. You tell it what to render and when exactly to render it. This notion of reacting and updating in response to user interaction is accomplished using lifecycle hooks or lifecycle methods.
React Component LifeCycles
React components only require the render() method to be valid and already have default behavior to re-render in response to a state change. If you need more control over how your component responds to change there are several methods available that allow you to specifically tell your app to run your code at particular points in your process. These methods are called Lifecycle methods and will only be called if you include definitions for them in your code. They are categorized into four groups: Mounting, updating, error boundaries and un-mounting.
Most commonly used lifecycle methods:
Image for post
The order in which these methods are called
componentDidMount — called only once when the component is constructed and it’s called right after render(). This is a great place to make a network request to a remote endpoint to get data.
componentDidUpdate(prevProps, prevState, snapShot) — called every time the component’s state changes.
Image for post
componentWillUnmount — called immediately before a component is removed from the DOM to perform necessary cleanup of elements created in componentDidMount().
Less commonly used lifecycle methods (think twice before using them and refer to the docs):
Image for post
The same diagram above but including the less common lifecycle methods.
shouldComponentUpdate(nextProps, nextState) — I should start by saying that the docs say to AVOID using shouldComponentUpdate and use a PureComponent instead: https://reactjs.org/docs/react-api.html#reactpurecomponent. shouldComponentUpdate() is used to let React know if render should be triggered or not. Sometimes your props or state changes but the changes don’t need to be rendered on the UI, so this would tell React not to re-render on the change. This is to gain performance if your render method is expensive to compute and you want to avoid the re-render when it’s unnecessary. Use the nextProps and nextState arguments to run a conditional that returns false if you DON’T want to re-render or true if you DO want to re-render.
static getDerivedStateFromProps(props, state) — this static method is called before every other method. The purpose of this method is to transfer over any values from props to state. The value returned from this method will be stored in local state. If you don’t want anything to be stored in state, return null.
getSnapshotBeforeUpdate(prevProps, prevState) — this method allows us to capture some properties that are now stored in the state before re-rendering the component. Whatever value is returned in this method is passed to componentDidUpdate as the snapshot parameter (see componentDidUpdate above). You can capture details like to what location the user had scrolled on a list or where the cursor was located in a row and column so you can handle them later on.
componentDidCatch(error, info) — allows us to handle the errors we get without everything crashing. If you don’t use error catching your component will might not show up at all, which isn’t great for UI. Use some sort of error boundaries to gather info and point yourself in the right direction for debugging.
Why would you use component based React instead of writing out some javascript?
The best way I’ve heard it described is that it’s like building a lego tower vs. one big toy tower that’s prebuilt in one piece. If you want to change a piece of the lego tower you can take out a lego piece and replace it with a different piece. The rest of the pieces all still work the same and nothing is else affected. If you want to change a part of the prebuilt tower you would have to break the tower and try to patch a new piece back into that doesn’t belong there. You’d have to make sure it didn’t affect the integrity of the rest of the structure and use glue and try to make it look the same as the rest of the toy. That’s much harder to do.
Similarly it’s much easier to make small changes to your application without having to worry about breaking things outside of the scope of the single component you are working on. The fact that React is declarative is what makes it easier to debug when the logic and html for a component is encapsulated in one file or a few predictable files rather than scattered throughout your code.
Resources:
React Component Docs — https://reactjs.org/docs/react-component.html
React Component Lifecycles Video — https://www.youtube.com/watch?v=m_mtV4YaI8c. This video gives you an excellent breakdown and code-along that shows you exactly when each lifecycle method is called and what they can be used for.
