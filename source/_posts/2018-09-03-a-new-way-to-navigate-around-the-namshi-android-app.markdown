---
layout: post
title: "A new way to navigate around the Namshi android app"
date: 2018-09-03 06:25
comments: true
categories: [android, mobile, navigation]
author: Akshay Jayakumar
---

From a UX standpoint, it is really important to strike a good balance between usability and how information is organized within the application. Too much information might be overwhelming to the user, and an improper flow of the information will become a discouraging experience. Having a proper navigation pattern is vital as this helps the users to navigate between various hierarchies of structured or organized information.  One of the biggest challenges within the mobile application purview is in providing a proper navigation, especially due to the smaller size of mobile screens. Several navigational patterns have been designed but each has its own strengths and weaknesses.   

<!-- more -->

Android Navigation Drawer (a.k.a Burger Menu or Side Menu) has been ruling Android apps UX for almost 5 years now. Google has made it so easy to implement that it became the primary choice of every app developer when it comes to app navigation. Almost all the apps developed by Google migrated to Navigation Drawer after it was released, so did the Namshi one.

![Navigation pattern in NAMSHI Android App.](https://d2mxuefqeaa7sj.cloudfront.net/s_07C9FE3356B59E821BA4395C1035CE40A82CC8DB34E1180E780172D6BED63FC6_1535430699961_NAMSHI-Android-Navigation-Drawer-small.gif)

As per good UX principles :
"**It is extremely important to present your users with the most important destinations within the app.**"

While Navigation drawer completely fulfills the above statement, There exist some fundamental problems with this navigation pattern.  Some of these problems are :

1. Lower Discoverability
2. Less Efficient
3. Clash with Platform Navigation Patterns
4. Not Glanceable

These problems are described in detail [here](https://lmjabreu.com/post/why-and-how-to-avoid-hamburger-menus/)

Side menu or a Navigation Drawer can hold relatively large amounts of heterogeneous contents. Apart from having a regular list of navigational items, it can also accommodate secondary information such as user profile details, or actions that are less frequently used but relevant under certain scenarios. One of the major advantages of having a Side menu or a Navigation Drawer is in its ability to save the screen real estate by taking the navigation away from the main screen, thereby making less overwhelming to the users but also can generally result in having poor visibility.

Another major downside of having a Side menu or a Navigation Drawer is that users tend to lose the context quickly as in which page/ destination they are currently in. This cannot be identified easily, as the navigation is hidden beyond the edges of the screen and always require a click of a button or a swipe. Such a limitation in providing a quick visual communication is considered non-desirable.

For an app like ours, with fewer top-level destinations, having a Navigation Drawer is kind of an overkill because there isn’t any secondary information displayed to the user other than the navigation. A fair amount of the screen remains, unused.

## Welcoming Bottom NavBar

A good percentage of the users prefer to have a single-hand interaction with their mobile devices/ apps. Pressing on the Burger menu icon in the action bar or swiping a finger from the edge of the screen reveals the hidden Navigation Drawer. Most of the cases, using Navigation Drawer will require the use of your second hand.  Though this is a typical UX pattern followed in many play store apps, it is not really the best nor is necessary depending on the context of your app. It is imperative to have a consistent navigation and the flow within the app making sense to your users.

Bottom navigation is one of the best suitable navigation patterns, arguably due to its ergonomic placement on the screen. It provides quick and easy access to the various top-level destinations. As mentioned in the Google Material Design guidelines, it is recommended to use the new Bottom navigation when there are **three** to **five** top-level destinations thus making it ideal for the Namshi app, as it has five top-level destination pages (Home, Search, Wishlist, Shopping bag, and My Namshi).

## A little about the Namshi app Configuration

Our app is highly configuration-driven. A set of configuration settings from our server, dictates the app on its various aspects such as the language of the displayed content, the home screen layout, content modules like images, gifs, videos, sliders, expandable/ scrolling lists, target for the user actions, showing quick alerts, arrangement of our products catalog, details in our checkout page, payment methods, our brand new delivery promises ([read more](http://tech.namshi.io/blog/2018/08/06/delivery-promises-in-the-wild/)), region-specific business rules. Pretty much everything in the app… you name it, it’s configuration driven! Navigation too is no exception and will follow suit! A specific property in the app configuration decides how our users will navigate within the app. This makes the implementation of the new Bottom navigation much challenging as any new changes should not break the existing Navigation Drawer functionality.  

## Implementing Bottom Navigation View

Bottom Navigation View is available as part of the Android Design Support library and the corresponding dependency should be added in the app `build.gradle` file.


    dependencies {
      ...
      compile 'com.android.support:design:<relevant.sdk.version>'
      // This was added in version 26.1.0. Visit the android doc for more info.
    }

Once this dependency is added, next would be to include the BottomNavigationView in your app layout. Add the BottomNavigationView to the root layout of your app.


    <android.support.design.widget.BottomNavigationView
           android:id="@+id/namshi_bottom_navigation"
           android:layout_width="match_parent"
           android:layout_height="wrap_content"
           android:layout_gravity="bottom" />


> Having a CoordinatorLayout as the root will enable us to use **bottom navigation behavior**. This behavior will make the BottomNavigationView scroll aware by hiding/ showing it when users scroll through a list thereby giving more space for displaying contents.

**Other Supported Attributes**
Below are some of the supported attributes.

  - **elevation** - Controls the elevation of this view.
  - **itemIconTint** - Single color or even a color selector -  Sets the color of the menu item icon depending on their states.
  - **itemTextColor** - This attribute can be used to change the title text color of the menu item. Supports a single color or a color selector.

**Setting Bottom navigation Menu Items**
Adding menu items to a BottomNavigationView is similar to that of adding menu items to a NavigationView in a Navigation Drawer layout. Menu items can be defined in an xml menu resource file or can be added dynamically. Being configuration-driven, it makes more sense to dynamically add the menu items depending on the configuration, rather than to have it populated from a static menu resource file.

> BottomNavigationView supports up to five menu items and anything more than that will result in a Runtime exception, crashing the app. This is a typical scenario that can occur upon activity re-creation when menu items are added dynamically.

See to it that a proper check is in place, so as to not exceed the limit of menu items in the BottomNavigationView. An alternative to this is to clear any existing menu items prior to populating it.


    fun addBottomNavigationMenuItems() {
      bottomNavigationView?.let { bnv ->
          bnv.menu?.let { menu ->
              menu.clear()
              // Add menu items to bnv here
          }
      }
    }

BottomNavigationView has a lot of limitations compared to many 3rd party Bottom navigation libraries. One such main limitation is the lack of support for Action Views in menu items. Android provides custom view support for menu items by the means of Action Views. Unfortunately, BottomNavigationView tends to ignore the Action Views, making it hard to customize individual menu items. Setting an Action View on the BottomNavigationView menu items seems to have no effect by which it is drawn in the layout. Below code snippet illustrates adding a Search menu item dynamically to the BottomNavigationView.


    val menuSearch =
        bottomNavigationView.menu.add(
          Menu.NONE, R.id.bottom_nav_item_search, Menu.NONE, R.string.search)

    menuSearch
      .setIcon(R.drawable.bnv_search_selector)
      .setActionView(View(context))
      .actionView.tag = arrayOf(FRAGMENT_PRODUCTS_SEARCH)

Even though BottomNavigationView ignores the Action Views, this can still be leveraged to make our new navigation aware of the destination pages. Every menu item has an Action View set to it, so that, the respective Action Views can hold a list of fragment tags that it represents. More about this is in the **Fragment Awareness** section, below.

## Event listeners

Just like any other views, BottomNavigationView also has got a set of events and associated listeners to it. The one we are interested now is **OnNavigationItemSelectedListener**. Selecting any menu items will trigger the **onNavigationItemSelected()** event of this particular listener. This event will also pass along with it the selected menu item based on which, appropriate logic for the navigation is performed.


    override fun onNavigationItemSelected(item: MenuItem): Boolean {
      when (itemId) {
          ...
          R.id.bottom_nav_item_search -> appMenuListener.displaySearchFragment()
          ...      
      }
    }

## Fragment awareness and support for Deep links

The Namshi android app follows a Single Activity and Multiple Fragments pattern and its architecture are highly decoupled. A helper class is responsible for performing all fragment transactions and this is in turn used by the AppMenuListener (a dagger2 dependency) which encapsulates the necessary logic for performing navigation to the appropriate destination page. When a user selects any particular navigation menu item, the corresponding event is triggered that invokes a specific action defined in the AppMenuListener.

Apart from this, users can still navigate to any destinations within the app by external means such as a Push Notification or even by Deep-links. In the Namshi android app, deep-links are resolved by a DeepLinkListener (yet another dagger2 dependency) which will perform the relevant routing, making use of the actions defined in the AppMenuListener. Implementing a consistent navigation across the app and to maintain the proper menu item states without changing this underlying implementation becomes challenging because, in such scenarios, navigation is not through but outside the bottom navigation.

In order to overcome this, our BottomNavigationView controller has implemented OnBackStackChangedListener of the FragmentManager class, which will trigger an event whenever a fragment is changed in the back stack. This will try to match the tag of the topmost fragment in the back stack to that stored in the navigation menu items.


    override fun onBackStackChanged() {
      clearMenuItemState()
      // NPE Check - if not detached from the activity
      val topFragment = FragmentHelper.getTopFragment(activity)
      topFragment?.let { fragment ->
        changeMenuItemState(fragment.tag)
      }  
    }

    fun changeMenuItemState(fragmentTag : String ?) {
        ... // Clear previous menu states if required
        menu?.let {
          for (i in 0 until it.size()) {
              val menuItem = it.getItem(i)
              menuItem?.let { item ->
                  val tags = item.actionView?.tag as? Array<String> ?: null
                  val index = tags?.indexOf(fragmentTag) ?: -1
                  if (index >= 0)
                    ... // Change the menu item state and return
              }
          }
        }
    }

Let’s see the new Bottom navigation in action!


![](https://d2mxuefqeaa7sj.cloudfront.net/s_07C9FE3356B59E821BA4395C1035CE40A82CC8DB34E1180E780172D6BED63FC6_1535456216606_ezgif.com-resize+2.gif)


## Notification Bubble

One of the most sought-after features for the Bottom navigation is to have a notification bubble with a notification count which is not supported by the BottomNavigationView out of the box. Using Action Views would have been the ideal approach for such use cases, but that is not an option here! Having that said, it is also not an impossible task either, to implement a simple Notification bubble to the menu items in the BottomNavigationView. Just a tiny tweak in the BottomNavigationView layout hierarchy can help us reach our goal! Every menu item in the BottomNavigationView is essentially a **BottomNavigationItemView** extending the android **FrameLayout**. There are no APIs available to interact with this directly. Below is a sample snippet for adding a notification bubble/ badge to any specific menu item in the BottomNavigationView.


    fun addNotificationBadge() {
      bottomNavigationView?.let {
          ... // Get the Menu View from the parent BottomNavigationView
          menuView?.let { mView ->
            ... // Get the corresponding menu item index
            val menuItemView = mView.getChildAt(/*index*/) as? BottomNavigationItemView
            val bubbleView = LayoutInflater.from(context)
                            .inflate(R.layout.bottom_nav_bubble_layout, null)
            ... // Find the corresponding view to update the count
            menuItemView.addView(bubbleView)
          }
      }
    }

Voila! Make sure to add the notification bubble during the initial setup of the BottomNavigationView but after the initialization of the menu items.


![Notification bubble in action](https://d2mxuefqeaa7sj.cloudfront.net/s_07C9FE3356B59E821BA4395C1035CE40A82CC8DB34E1180E780172D6BED63FC6_1535456573986_ezgif.com-gif-maker.gif)

> It will be good to consider making the notification bubble layout as simple as possible so as to reduce the layout over-draws. Adhere to good practices, use flat rather than nested or intricate layouts!  

## Future enhancements

During I/O 2018, Google introduced the new **Navigation-components** to the Android Architecture which will greatly simplify the way navigation is done within the app. This will help in implementing a consistent navigation between various destinations within your app in a disentangled way. Each destination can be a fragment, an activity, a navigation graph or a subgraph. Custom destinations are also supported. Navigation-components also support actions, type-safe arguments, deep-links and will also go well with the BottomNavigationView. Many of the problems and user requirements mentioned above can be addressed with this. One such important issue that this will solve, is building the stack of destination pages when a user navigates through a deep-link, which otherwise would have happened during manual navigation. Our app, being mostly a “Single Activity and Multiple Fragments app” can be easily migrated to the new Navigation Architecture with less effort. This promising new addition to the Android Architecture enforces conformance to the Architecture guidelines thereby facilitating a consistent and predictable navigation by decoupling the routing logic that otherwise is contained in the view layer, which can become quite tedious to maintain and modify in larger applications.
Another great feature to have with Bottom navigation is to introduce the **Bottom Navigation Behavior** which will show and hide the Bottom Navigation View when a user scrolls through a huge list just like our Products catalog page, giving more space for displaying the list contents.

## To wrap-up!

Android BottomNavigationView has got several limitations and there are many 3rd party implementations overcoming them. Nevertheless, none of that has stopped us from using it in the Namshi android app. We at Namshi embrace new challenges that help us get better in delivering the best experience for our users. Apart from this fixed navigation pattern, the deep-links support provides quick access to any specific destinations within the app rather than to go through multiple levels, manually. Fragment awareness comes in handy, as this will make the Navigation menu items to be on the right state when the destination page is loaded. By using Bottom Navigation, the content of the app of becomes readily discoverable and it gets easy to do single-handed navigation.
Go ahead and download [Namshi App](https://play.google.com/store/apps/details?id=com.namshi.android&hl=en) from Google Play and let us know how the new navigation feels like!
