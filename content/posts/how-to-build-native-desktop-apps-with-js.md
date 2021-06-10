---
title: How to build native desktop apps with JavaScript
date: "2018-05-05"
description: any application that can be written in JavaScript, will eventually be written in JavaScript
published: true
tags: javascript, react, webdev, node
cover_image: https://thepracticaldev.s3.amazonaws.com/i/rk3k0rcgppj3g20uqr2z.png
---

> Atwood’s Law: any application that can be written in JavaScript, will eventually be written in JavaScript. -[Jeff Atwood](https://en.wikipedia.org/wiki/Jeff_Atwood)


Today we are going to take a look at [Proton Native](https://proton-native.js.org) and make a simple hashing app with it.

Unlike **Electron** apps, apps built with **Proton Native** are actually **native** (hence the name) and not web based on chromium.

**Proton Native** is like **React Native** but for desktop, It compiles to native platform code so it looks, and performs like native apps.

## Windows
install the build tools by running
`npm install --global --production windows-build-tools`

## Linux
you’ll need these libraries:

- libgtk-3-dev
- build-essential

## Mac 
You don’t need anything.

Now run `npm i -g create-proton-app`, and `create-proton-app my-app` to make a new project.

Open the project directory with your favorite code editor, the directory should look like this:

```powershell
└───node\_modules
├───.babelrc
├───index.js
├───package.json
└───package-lock.json
```

`index.js` should look like this:

![](https://cdn-images-1.medium.com/max/1024/1*BUgjpvWtCCZNPJ__qrQxig.png)<figcaption><em>As you can see it look like React/React Native File</em></figcaption>

Just like any React or React Native Project, we import the react library and make a class component.

The `App` element is just a container that holds the Window and Menu, the `Window` has three props; `title` (the window title), `size` (takes an object that contains the width and height of the window), `menuBar` (set to false because we don’t want a menu bar).

Before we start coding let’s install `crypto` using `npm`:

`npm i crypto`

_We will use_ crypto _to hash the text to md5._

### index.js


```jsx
import React, { Component } from "react";
import { render, Window, App, Box, Text, TextInput } from "proton-native";
import crypto from "crypto";

class Example extends Component {
state = { text: "", md5: "" };

hash = text => {
    this.setState({ text });
    
    let md5 = crypto
    .createHash("md5")
    .update(text, "utf8")
    .digest("hex");

    this.setState({ md5 });
};
render() {
    return (
    <App>
        <Window
        title="Proton Native Rocks!"
        size={{ w: 300, h: 300 }}
        menuBar={false}
        >
        <Box>
            <TextInput onChange={text => this.hash(text)} />
            <Text>{this.state.md5}</Text>
        </Box>
        </Window>
    </App>
    );
}
}

render(<Example />);
```


I first imported `Text` and `TextInput` so that I could use them later, and then in the `class` after setting the `text` and `md5` to empty strings in the state object I created a function `hash` that takes a text argument.

In the hash function, we set the `state` to `text` and declare `md5` to store the hashed text


```jsx
this.setState({ md5});
let md5 = crypto.createHash("md5")
.update(text, "utf8").digest("hex");
```


and set the state object to the updated md5.

```jsx
this.setState({ md5 });
```

The `render` method return some jsx element, the `Box` element is just like `div` in React or `View` in React Native which hold the `TextInput` and `Text` because the window element doesn’t allow having more than one child (what is this china … sorry).

`TextInput` has an `onChange` prop that will be called every time the text changes, so we set it to a fat arrow function that takes a `text` argument and returns the `hash` function we created earlier.

So now every time the text changes `text` is hashed using `md5`.

Now if we run it with

`npm run start`

this window should pop up <br />

![](https://cdn-images-1.medium.com/max/375/1*D_fBTxyGSpUbIVPcyt3Kzw.png)

and if we enter some text it gets hashed to md5 <br />

![](https://cdn-images-1.medium.com/max/375/1*azNLC0SBkJs85SK-fj15fw.png)

You might say “It looks ugly let’s add some styling to it” well…at the time of writing this article, Proton Native is still in its infancy, very buggy and it doesn’t support styling (yet) but it’s a fun project to play with.

If you want to contribute to the project check out the [repo](https://github.com/kusti8/proton-native)

If you have any questions or suggestions feel free to comment or reach me on [Twitter](https://twitter.com/4msal4) and don’t forget to hit that clap button :)

Check out the previous article

[How to build a news app with React Native](/how-to-build-a-news-app-with-react-native)
