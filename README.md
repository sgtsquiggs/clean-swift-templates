# Clean Swift templates ([Tailor](https://github.com/sleekbyte/tailor) compliant)

This is a modification of a modification of Raymond Law's [Clean Swift](http://clean-swift.com) templates.

The original modifications were made by [HELM](www.helm.cat).

To learn more about Clean Swift and the VIP cycle, read:

http://clean-swift.com/clean-swift-ios-architecture

# Changelog

The HELM modification aims to create work-ready templates and snippets to achieve the maximum efficiency for people who are already familiar with them. Furthermore HELM added extra features in order to attempt to solve some issues.

I have taken the HELM-modified templates and reformatted them to match the [Tailor](https://github.com/sleekbyte/tailor) formatting guidelines, for those of us using Tailor to enforce standarized code formatting.

Here is a list of what is modified:
* Braces follow the OTBS (https://en.wikipedia.org/wiki/Indent_style#Variant:_1TBS)
* Deleted all example functions (doSomething, presentSomething, etc.) aswell as example models
* Deleted worker auto-creation from Scene template
* Singleton using the simpler `static let sharedInstance = ClassName()` format

Added features:
* Awesome snippet to create usecases (HELM)
* New data-passing method (HELM)
* Store template (HELM)

## Snippet
![alt text](http://helm.cat/assets/github-images/usecase-snippet.gif "Use Case Snippet")

(note: actual snippet uses two spaces not four)

## New data-passing method
Previously to pass data we need to do something like this in the source Router:
```swift
let user = sourceViewController.output.selectedUser
destinationViewController.output.user = user
```
We think this is wrong because:

1. We are not trying to output anything from the ViewController.
2. We are assuming the output === Interactor and the architecture loses sense (no component should know about what kind of object its output/input is).
3. We dont want the ViewController to know anythyng about Business model.
4. We want the Interactor to handle this data but we don't want the ViewController to know anything about it.

So we added 2 new protocols on the router

```swift
protocol RouterDataSource: class {

}
protocol RouterDataDestination: class {

}
```
- The RouterDataSource protocol is used to determine what business data has to be passed somewhere.
- The RouterDataDestination protocol is used to determine what data has to be received and handled by this scene.

So following the example before we would add this in the source Router:
```swift
protocol UserListRouterDataSource: class {
  var selectedUser:User! { get }
}

protocol UserListRouterDataDestination: class {

}
```

And this in the destination:
```swift
protocol UserDetailRouterDataSource: class {

}

protocol UserDetailRouterDataDestination: class {
  var user:User! { get set }
}
```

As we added a dataSource object and a dataDestination object in the Router, we can now do this:

```
let user = dataSource.selectedUser
userDetailViewController.router.dataDestination.user = user
```

As previously mentioned we believe this handling should be done by the Interactor as well but without the ViewController knowing it so we added the protocols there and changed the Configurator.swift in order to connect both protocols.

So the source Interactor would look like this:
```swift
protocol UserListDataSource {
  var selectedUser:User! { get }
}

protocol UserListDataDestination {

}

class UserListInteractor: UserListInteractorInput, UserListDataSource, UserListDataDestination {
```

And as you would assume, the destination Interactor looks like this:

```swift
protocol UserDetailDataSource {

}

protocol UserDetailDataDestination {
  var user:User! { get set }
}

class UserDetailInteractor: UserDetailInteractorInput, UserDetailDataSource, UserDetailDataDestination {
```

# Installation

To install the Clean Swift Xcode HELM templates and snippets, run:
```
./configure
make install
```
To uninstall the Clean Swift Xcode templates and snippets, run:
```
make uninstall
```
