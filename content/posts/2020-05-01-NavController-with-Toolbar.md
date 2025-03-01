---
title: NavController with Toolbar
date: "2020-05-01T05:30:00Z"
categories: [ Code ]
tags: [ android ]
---

# Navigation Component

Android Jetpack's Navigation component helps you implement navigation, from simple button clicks to more complex patterns, such as app bars and the navigation drawer. The Navigation component also ensures a consistent and predictable user experience by adhering to an established set of principles.

## Documentation for toolbar 

To sync up the toolbar and navigation component, the official documentation suggests

``` kotlin
val navController = findNavController(R.id.nav_host_fragment)
val appBarConfiguration = AppBarConfiguration(navController.graph)
findViewById<Toolbar>(R.id.toolbar).setupWithNavController(navController, appBarConfiguration)
```

The above configurations seems good and works for most of the common cases until we hit a feature request of handling custom navigate back action on a particular fragment.

We can do that by,

``` kotlin
// This callback will only be called when MyFragment is at least Started.
val callback = requireActivity().onBackPressedDispatcher.addCallback(this) {
    // Handle the back button event
}
```

And we register the In-App up action via `Option menu`

``` kotlin
override fun onOptionsItemSelected(item: MenuItem): Boolean = when (item.itemId) {
    android.R.id.home -> {
        // custom logic here
        true
    }
```

## Problem

The problem with this implementation is that, it only works with **System Navigation Back** button. And will not work for **In-App UP action** on the toolbar.

## Solution

Instead of using `toolbar.setupWithNavController(navController, appBarConfiguration)`, use

``` kotlin
NavigationUI.setupActionBarWithNavController(this@MainActivity, navController, appBarConfiguration)
```

and 

``` kotlin
override fun onSupportNavigateUp(): Boolean = navController.navigateUp(appBarConfiguration) || super.onSupportNavigateUp()
```
