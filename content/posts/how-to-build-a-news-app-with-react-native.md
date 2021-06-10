---
title: How to build a news app with JavaScript and React Native
published: true
date: "2018-05-07"
description: For my first post on my blog, and I wanted to share with you how I made a news app with React Native.
tags: javascript, react native, mobile, react
cover_image: https://thepracticaldev.s3.amazonaws.com/i/ja4wigu0v68r4clmoaw2.png
---


Requirements for building the app:

* A basic understanding of the JavaScript language.
* [Node.js](https://nodejs.org), and [react native](https://facebook.github.io/react-native).
* Libraries used: moment, react-native, react-native-elements.

If you’re not familiar with these resources, don’t worry — they are quite easy to use.

The topics we will cover in the post are:

* News API
* Fetch API
* FlatList
* Pull down to refresh
* Linking

And more…so let’s get started!
***You can find the full project repo [HERE](https://github.com/msal4/royal_news).***

# News API
> A simple and easy-to-use API that returns JSON metadata for headlines and articles live all over the web right now. —[ NewsAPI.org](https://newsapi.org/)

First, you should go ahead and sign up for News Api to get your free apiKey (your authentication key).

Create a new React Native project, and call it `news_app` (or whatever you want). In the project directory, make a new folder and call it `src` . In `src` create a folder an name it `components` . So your project directory should look something like this:
![screenshot](https://thepracticaldev.s3.amazonaws.com/i/vgonq8enqsbp69zqesfc.png)
In the src folder, create a new file called news.js . In this file we are going to fetch the JSON that contains the headlines from the News API.

# news.js
```jsx
const url =
  "https://newsapi.org/v2/top-headlines?country=us&apiKey=YOUR_API_KEY_HERE";

export async function getNews() {
  let result = await fetch(url).then(response => response.json());
  return result.articles;
}
```
Make sure you replace `YOUR_API_KEY_HERE` with your own API key. For more information about the News API, go to [newsapi docs](https://newsapi.org).

Now we declare the `getNews` function, which is going to fetch the articles for us. Export the function so we can use it in our `App.js` file.

# App.js
```jsx
import React from 'react';
import { FlatList } from 'react-native';

// Import getNews function from news.js
import { getNews } from './src/news';
// We'll get to this one later
import Article from './src/components/Article';

export default class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = { articles: [], refreshing: true };
    this.fetchNews = this.fetchNews.bind(this);
  }
  // Called after a component is mounted
  componentDidMount() {
    this.fetchNews();
   }

  fetchNews() {
    getNews()
      .then(articles => this.setState({ articles, refreshing: false }))
      .catch(() => this.setState({ refreshing: false }));
  }

  handleRefresh() {
    this.setState(
      {
        refreshing: true
    },
      () => this.fetchNews()
    );
  }

  render() {
    return (
      <FlatList
        data={this.state.articles}
        renderItem={({ item }) => <Article article={item} />}
        keyExtractor={item => item.url}
        refreshing={this.state.refreshing}
        onRefresh={this.handleRefresh.bind(this)}
      />
  );
  }
}
```
In the constructor, we define the initial state. `articles` will store our articles after we fetch them, and `refreshing` will help us in refresh animation. Notice that I set`refreshing` to `true`, because **when we start the app, we want the animation to start while we load the articles.**
`componentDidMount` is invoked immediately after a component is mounted. Inside it we call the `fetchNews` method.
```jsx
componentDidMount() {
  this.fetchNews();
}
```
In `fetchNews` we call `getNews()` which returns a promise. So we use the `.then()` method which takes a callback function, and the callback function takes an argument (the articles).

Now assign the articles in the state to the articles argument. I only typed `articles` because it’s a new ES6 syntax that means { articles: articles } , and we set `refreshing` to `false` to stop the spinner animation.
```jsx
fetchNews() {
  getNews().then(
      articles => this.setState({ articles, refreshing: false })
  ).catch(() => this.setState({ refreshing: false }));
}
```
`.catch()` is called in rejected cases.

`handleRefresh` starts the spinner animation and call `fetchNews()`. We pass `() => this.fetchNews()` , so it’s called immediately after we assign the state.
```jsx
handleRefresh() {
  this.setState({ refreshing: true },() => this.fetchNews());
}
```
In the `render` method, we return a `FlatList` element. Then we pass some props. `data` is the array of articles from `this.state`. The `renderItem` takes a function to render each item in the array, but in our case it just returns the     `Article` component we imported earlier (we’ll get there). And we pass the `article` item as a prop to use later in that component.

# Article.js

In `src/components` create a new JavaScript file and call it `Article.js`.

Let’s start by installing two simple libraries using npm: `react-native-elements`, which gives us some premade components we could use, and `moment` that will handle our time.

run using terminal/cmd:

`npm install --save react-native-elements moment`

In Article.js:

```jsx
import React from 'react';
import { View, Linking, TouchableNativeFeedback } from 'react-native';
import { Text, Button, Card, Divider } from 'react-native-elements';
import moment from 'moment';

export default class Article extends React.Component {
  render() {
    const {
      title,
      description,
      publishedAt,
      source,
      urlToImage,
      url
    } = this.props.article;
    const { noteStyle, featuredTitleStyle } = styles;
    const time = moment(publishedAt || moment.now()).fromNow();
    const defaultImg =
      'https://wallpaper.wiki/wp-content/uploads/2017/04/wallpaper.wiki-Images-HD-Diamond-Pattern-PIC-WPB009691.jpg';

    return (
      <TouchableNativeFeedback
        useForeground
        onPress={() => Linking.openURL(url)}
      >
        <Card
          featuredTitle={title}
          featuredTitleStyle={featuredTitleStyle}
          image={{
            uri: urlToImage || defaultImg
          }}
        >
          <Text style={{ marginBottom: 10 }}>
            {description || 'Read More..'}
          </Text>
          <Divider style={{ backgroundColor: '#dfe6e9' }} />
          <View
            style={{ flexDirection: 'row', justifyContent: 'space-between' }}
          >
            <Text style={noteStyle}>{source.name.toUpperCase()}</Text>
            <Text style={noteStyle}>{time}</Text>
          </View>
        </Card>
      </TouchableNativeFeedback>
    );
  }
}

const styles = {
  noteStyle: {
    margin: 5,
    fontStyle: 'italic',
    color: '#b2bec3',
    fontSize: 10
  },
  featuredTitleStyle: {
    marginHorizontal: 5,
    textShadowColor: '#00000f',
    textShadowOffset: { width: 3, height: 3 },
    textShadowRadius: 3
  }
};
```
There is a lot going on here. First, we start by [destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) the `article` prop and the `styles` object defined below the class.

In `render` we define `time` to store the time for when the article was published. We use the `moment` library to convert the date to the time passed since then, and we pass `publishedAt` or time from now if `publishedAt` is `null`.

`defaultImg` is assigned an image URL in case the URL of the article image is `null`.

The `render` method returns `TouchableNativeFeedback` (use `TouchableOpacity` instead if it does not work on your platform) to handle when the user presses the card. We pass it some props: `useForground` which tells the element to use the foreground when displaying the ripple effect on the card, and `onPress` , which takes a function and executes it when the user presses the card. We passed `() => Linking.openUrl(url)` which simply opens the URL to the full article when we press the card.

The card takes three props: `featuredTitle` which is just a fancy title placed over the image you could use `title` instead if you want, `featuredTitleStyle` to style it, and image which is the article image from the article prop. Otherwise, if its `null` , it’s going to be the `defaultImg`.
```jsx
// ..
  featuredTitle={title}
  featuredTitleStyle={featuredTitleStyle}
  image={{ uri: urlToImage || defaultImg }}
// ..
```

As for the `text` element, it will hold the description for the article.

`<Text style={{ marginBottom: 10 }}>{description}</Text>`

We added a `divider` to separate the description from time and source name.

`<Divider style={{ backgroundColor: '#dfe6e9' }} />`

Below the `Divider` , we have a `View` that contains the source name and the time the article was published.
```jsx
// ..
<View 
  style={{ flexDirection: ‘row’, justifyContent: ‘space-between’ }} > 
  <Text style={noteStyle}>{source.name.toUpperCase()}</Text>
  <Text style={noteStyle}>{time}</Text>
</View>
// ..
```

After the `class`, we defined the styles for these components.

Now if we run the app:
![screenshot](https://thepracticaldev.s3.amazonaws.com/i/fa1ztf421utl1cal60h3.jpg)
*and we can refresh the app*
![screenshot](https://thepracticaldev.s3.amazonaws.com/i/ghu5if5yehnln21u1hyn.jpg)
There you go! The source code for the app is available on GitHub [HERE](https://github.com/msal4/royal_news) you can improve upon it and make a pull request.

I hope you enjoyed my article! If you have any questions at all, feel free to comment or reach me on [twitter](https://twitter.com/4msal4) and I will definitely help.
