# Changelog

`Parchment` adheres to [Semantic Versioning](http://semver.org/).

## [1.0.0](https://github.com/rechsteiner/Parchment/compare/v0.9.0...v1.0.0) - 2018-01-29

This release introduces a lot of breaking changes, a bunch of new features and a couple of bug fixes. Here are the most notable changes, with a full list of changes below.

### Removed `PagingOptions` initializer #98

All configuration is now moved into properties on the `PagingViewController` class. You no longer have to initialize a `PagingViewController` with an instance conforming to the `PagingOptions` protocol. This reduces the boilerplate of having to create a separate options struct when you just need to override a single value. It also means you can change the options after the `PagingViewController` has been initialized. All the properties on the `PagingTheme` protocol has also moved into separate properties on `PagingViewController`.

**Before:**

```Swift
struct Theme: PagingTheme {
  let textColor: UIColor = .red
}

struct Options: PagingOptions {
  let theme: PagingTheme = Theme()
  let menuItemSize: PagingMenuItemSize = .fixed(width: 100, height: 40)
}

let pagingViewController = PagingViewController(options: Options())
```

**After:**

```Swift
let pagingViewController = PagingViewController()
pagingViewController.menuItemSize = .fixed(width: 100, height: 40)
pagingViewController.textColor = .red
```

### Renamed data source #99

The current data source protocol has been renamed to `PagingViewControllerInfiniteDataSource` and moved into the property called `infiniteDataSource`.

### Added new data source #99

A new `PagingViewControllerDataSource` protocol has been added that makes it easier to set up a custom data source if you have a fixed number of view controllers. To use the new data source, you only need to return the total number of
view controllers as well as the view controller and `PagingItem` for a given index. The new data source replaces the existing `dataSource` property.

**Example:**

```Swift
extension ViewController: PagingViewControllerDataSource {

  func numberOfViewControllers<T>(in: PagingViewController<T>) -> Int {
    return items.count
  }  

  func pagingViewController<T>(_ pagingViewController: PagingViewController<T>, viewControllerForIndex index: Int) -> UIViewController {
    return ItemViewController(item: items[index])
  }

  func pagingViewController<T>(_ pagingViewController: PagingViewController<T>, pagingItemForIndex index: Int) -> T {
    return items[index] as! T
  }
}

...

pagingViewController.dataSource = self
```

### Updated delegate protocol #100

Three new delegate methods have been added to the `PagingViewControllerDelegate` protocol. You can now be notified before, during and after the user navigates to another view controller:

```Swift
protocol PagingViewControllerDelegate: class {

  func pagingViewController<T>(
    _ pagingViewController: PagingViewController<T>,
    isScrollingFromItem currentPagingItem: T,
    toItem upcomingPagingItem: T?,
    startingViewController: UIViewController,
    destinationViewController: UIViewController?,
    progress: CGFloat)

  func pagingViewController<T>(
    _ pagingViewController: PagingViewController<T>,
    willScrollToItem pagingItem: T,
    startingViewController: UIViewController,
    destinationViewController: UIViewController)

  func pagingViewController<T>(
    _ pagingViewController: PagingViewController<T>,
    didScrollToItem pagingItem: T,
    startingViewController: UIViewController?,
    destinationViewController: UIViewController,
    transitionSuccessful: Bool)

  func pagingViewController<T>(
    _ pagingViewController: PagingViewController<T>,
    widthForPagingItem pagingItem: T,
    isSelected: Bool) -> CGFloat?
}
```

The `widthForPagingItem:` delegate has been changed to return `CGFloat?` instead of `CGFloat` (See: #100). The default implementation will return nil.

### Removed `FixedPagingViewControllerDelegate` protocol #100

The `FixedPagingViewControllerDelegate` protocol is replaced by the `PagingViewControllerDelegate` protocol. The new delegate does not include the index for the paging items, but you can get the current index from the `PagingIndexItem` like this:

```Swift
extension ViewController: PagingViewControllerDelegate {
  func pagingViewController<T>(_ pagingViewController: PagingViewController<T>, didScrollToItem pagingItem: T, startingViewController: UIViewController?, destinationViewController: UIViewController, transitionSuccessful: Bool) {
    if let indexItem = pagingItem as? PagingIndexItem {
      print("index: ", indexItem.index)
    }
  }
}
```

### Added

* Add option to always center selected menu item #101
* Allow subclassing the collection view layout #104
* Add empty implementations of collection view delegate methods (4840483)
* Add option to disable content interaction #113
* Add option for selected background color #114
* Add method for selecting paging items based on index #117

### Changed

* Rename selectPagingItem to select(pagingItem:) #105
* Make PagingState property public #107
* Make PagingItems struct public #108
* Make PagingState extension properties public (f842a7b)
* Make indicator layout attributes open to allow subclassing (7c35acc)
* Change collection view delegate methods to open (68b125b)
* Replace PagingTheme with PagingOptions #111
* Rename `headerBackgroundColor` to `menuBackgroundColor` #116

### Fixes

* Fix performance issue with updating content offset #106
* Set background color for paging cell based on options (1a70bd6)
* Fix layout calculation when using transforms #102
* Account for menu spacing when generating items #103
* Fix bug with animation when selecting items (73913f)

## [0.9.0](https://github.com/rechsteiner/Parchment/compare/v0.8.0...v0.9.0) - 2017-12-25

### Added

* Add method for reloading data #94
* Allow setting different width for selected cell #85

## [0.8.0](https://github.com/rechsteiner/Parchment/compare/v0.7.0...v0.8.0) - 2017-11-19

### Fixes

* Fix selecting paging items before view appears #79
* Fix issues with safe area insets #81

## [0.7.0](https://github.com/rechsteiner/Parchment/compare/v0.6.0...v0.7.0) - 2017-11-07

### Changes

* Change access for EMPageViewController delegate method #72
* Improve performance by using manual frames for menu cells #68
* Add option to customize indicator and border view #67
* Update documentation #60

## [0.6.0](https://github.com/rechsteiner/Parchment/compare/v0.5.0...v0.6.0) - 2017-09-25

### Changes

* Upgrade to Swift 4.0 #54

### Fixes

* Fix bug where selecting items was not working #55

## [0.5.0](https://github.com/rechsteiner/Parchment/compare/v0.4.0...v0.5.0) - 2017-08-22

### Added

* Add support for scrolling in header #48

### Changes

* Require `PagingItem` to conform to `Hashable` and `Comparable`:
  [fbd7aff](https://github.com/rechsteiner/Parchment/pull/48/commits/fbd7aff8c1e3ac17dad8644961d073dc49da6a1e)
* Use custom collection view layout instead of using
  `UICollectionViewFlowLayout`
  [c6f78b4](https://github.com/rechsteiner/Parchment/pull/48/commits/c6f78b4521c4cae56050316ae3ec3ac72fe895ba)

## [0.4.0](https://github.com/rechsteiner/Parchment/compare/v0.3.0...v0.4.0) - 2017-05-04

### Added

* Add delegate for selected items in FixedPagingViewController #46

### Fixes

* Fix issue with delayed rendering #45

## [0.3.0](https://github.com/rechsteiner/Parchment/compare/v0.2.0...v0.3.0) - 2017-03-12

### Changes

* Allow selectPagingItem to be called before viewDidAppear #32
* Move collection view above paging view #31

### Fixes

* Fixes for EMPageViewController #36
* Fix calculation for transition distance #33
* Fix background color on header view #41

## [0.2.0](https://github.com/rechsteiner/Parchment/compare/v0.1.2...v0.2.0) - 2017-02-19

### Added

* Add progress value to menu items: #20
* Scroll menu items alongside content: #22
* Option to add spacing to indicator: #27
* Add new icons example project
* Add example for loading view controllers from storyboard

## [0.1.2](https://github.com/rechsteiner/Parchment/compare/v0.1.1...v0.1.2) - 2016-12-08

### Changes

* Update to Swift 3.0 #11
* Update public accessors
  [1f057a9](https://github.com/rechsteiner/Parchment/commit/1f057a94dc8e204343eeb78b9be6f516e1a6af15)

### Fixes

* Account for menuInsets when using sizeToFit #8
* Add support for centering fixed width menu items #10

## [0.1.1](https://github.com/rechsteiner/Parchment/compare/v0.1.0...v0.1.1) - 2016-05-22

### Changes

* Add MIT license #4

## [0.1.0](https://github.com/rechsteiner/Parchment/compare/0ad346e...v0.1.0) - 2016-05-22

* Inital release