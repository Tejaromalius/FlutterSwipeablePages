# Make Your App Shine: Swiping Pages with Navigation Bar in Flutter

Have you ever admired the smooth swiping between sections in apps like WhatsApp? It makes navigating a breeze, and you can achieve the same effect in your Flutter app! This guide will walk you through creating that swiping magic, complete with a functional navigation bar.

## Demo's Always First

<p align="center">
<img src="https://github.com/Tejaromalius/FlutterSwipeablePages/raw/main/assets/demo.gif" alt="Widget demo"  height=500/>
</p>

## You Liked It? Time to Code It!

### Getting Started

We only need one library from Flutter: `material.dart`. This library provides essential building blocks for creating beautiful and functional user interfaces. Let's import it at the beginning of our code:

```dart
import 'package:flutter/material.dart';
```

### Defining Your App's Sections

Imagine your app's main areas, like a home screen, search bar, or profile page. These will be the sections users can swipe between. In this example, we'll use colored containers to represent them, but you can replace them with your actual app screens later. We'll store these sections in a list called `Pages.list`:

```dart
class Pages {
  static final List<Widget> list = [
    Container(color: Colors.amber, child: Center(child: Text('Home'))),
    Container(color: Colors.blue, child: Center(child: Text('Search'))),
    Container(color: Colors.green, child: Center(child: Text('Feed'))),
    Container(color: Colors.red, child: Center(child: Text('Profile'))),
  ];
}
```

### Building Your App

Now comes the exciting part! We'll create a `MainApp` class that behaves like our app's core:

```dart
void main() => runApp(MaterialApp(home: MainApp(), debugShowCheckedModeBanner: false));
```

Here's a breakdown of what we'll build in the `MainApp` class:

1. **Keeping Track of the Current Page:** We'll use a variable called `_currentPageIndex` to remember which page the user is currently on. This helps us keep the navigation bar in sync with the displayed content.

2. **Navigation with Controller:** We'll introduce a `PageController`. Imagine this as a conductor for your pages. It allows the navigation bar to tell the pages which one to show when a user selects a destination.

3. **Creating the Swipeable Effect:** We'll use a `PageView` widget to display our list of pages (`Pages.list`). Think of it like a container that holds all your pages side-by-side. We'll set its width and height to the size of the screen, allowing users to swipe horizontally between them. We can also update `_currentPageIndex` within `onPageChanged` to ensure it reflects the currently displayed page.

4. **The Navigation Bar (Your User's Guide):** We'll use a `NavigationBar` widget to create a visual representation of the available sections in your app. Each item in the navigation bar corresponds to a page in the `PageView`. When a user taps on a navigation bar item, we want to switch to the corresponding page. We'll achieve this with the help of the `PageController`!

Here's the code that brings it all together:

```dart
class MainApp extends StatefulWidget {
  @override
  _MainAppState createState() => _MainAppState();
}

class _MainAppState extends State<MainApp> {
  int _currentPageIndex = 0;
  final PageController _pageController = PageController();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: PageView(
        controller: _pageController, // Link the PageView to the controller
        children: Pages.list,
        onPageChanged: (index) => setState(() => _currentPageIndex = index),
      ),
      bottomNavigationBar: NavigationBar(
        selectedIndex: _currentPageIndex, // Keep the nav bar in sync
        onDestinationSelected: (index) {
          setState(() => _currentPageIndex = index); // Update current page
          _pageController.jumpToPage(index); // Jump to the selected page
        },
        destinations: [
          NavigationDestination(icon: Icon(Icons.home), label: 'Home'),
          NavigationDestination(icon: Icon(Icons.search), label: 'Search'),
          NavigationDestination(icon: Icon(Icons.feed_outlined), label: 'Feed'),
          NavigationDestination(icon: Icon(Icons.person), label: 'Profile'),
        ],
      ),
    );
  }
}
```
---
### Scrolling Vertically But Pages Change?

Since we have not specified any physics for our `PageView`, even a slight horizontal movement while vertically scrolling can be mistaken for a call to change page. To correct that we can extend `ScrollPhyics` and specify our own custom rules!
```dart
class PageViewScrollPhysics extends ScrollPhysics {
  const PageViewScrollPhysics({super.parent});

  @override
  PageViewScrollPhysics applyTo(ScrollPhysics? ancestor) {
    return PageViewScrollPhysics(parent: buildParent(ancestor));
  }

  @override
  double get dragStartDistanceMotionThreshold => 75;

  @override
  double get minFlingDistance => 75;
}
```
The only thing we need to do now is add this physics to our `PageView`.

---
## THE END
 Our final code now looks like:
```dart
import 'package:flutter/material.dart';

class Pages {
  static final List<Widget> list = [
    Container(color: Colors.amber, child: Center(child: Text('Home'))),
    Container(color: Colors.blue, child: Center(child: Text('Search'))),
    Container(color: Colors.green, child: Center(child: Text('Feed'))),
    Container(color: Colors.red, child: Center(child: Text('Profile'))),
  ];
}

void main() => runApp(MaterialApp(home: MainApp(), debugShowCheckedModeBanner: false));

class MainApp extends StatefulWidget {
  @override
  _MainAppState createState() => _MainAppState();
}

class _MainAppState extends State<MainApp> {
  int _currentPageIndex = 0;
  final PageController _pageController = PageController();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: PageView(
        controller: _pageController, // Link the PageView to the controller
        children: Pages.list,
        physics: PageViewScrollPhysics(),
        onPageChanged: (index) => setState(() => _currentPageIndex = index),
      ),
      bottomNavigationBar: NavigationBar(
        selectedIndex: _currentPageIndex, // Keep the nav bar in sync
        onDestinationSelected: (index) {
          setState(() => _currentPageIndex = index); // Update current page
          _pageController.jumpToPage(index); // Jump to the selected page
        },
        destinations: [
          NavigationDestination(icon: Icon(Icons.home), label: 'Home'),
          NavigationDestination(icon: Icon(Icons.search), label: 'Search'),
          NavigationDestination(icon: Icon(Icons.feed_outlined), label: 'Feed'),
          NavigationDestination(icon: Icon(Icons.person), label: 'Profile'),
        ],
      ),
    );
  }
}

class PageViewScrollPhysics extends ScrollPhysics {
  const PageViewScrollPhysics({super.parent});

  @override
  PageViewScrollPhysics applyTo(ScrollPhysics? ancestor) {
    return PageViewScrollPhysics(parent: buildParent(ancestor));
  }

  @override
  double get dragStartDistanceMotionThreshold => 75;

  @override
  double get minFlingDistance => 75;
}
```

With this code, you've achieved a swipeable page layout with a functional navigation bar, mimicking the WhatsApp experience! Hope you have enjoyed this short tutorial.