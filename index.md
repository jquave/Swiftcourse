//
//  Model.swift
//  DemoApp
//
//  Created by Jameson Quave on 6/17/15.
//  Copyright Â© 2015 Lumarow. All rights reserved.
//

import Foundation

/*
Let's say you have a music app that allows users to create playlists from a list of songs. You would want to create a Swift model representing this by creating structs for each type of data you need in your app. We'll call these Track and Playlist.
A single track may have properties like title, a price, and a rating a user has given the track. Something like this would work for the first two values:
*/

/*
```swift
struct Track {
  let title: String
  var price: Float
}
```

For the title, it's fine to make this a constant value. The title of a Track simply won't change, so we can use the *let* keyword to indicate this fact. The compiler will make sure the title is never accidentally changed, and present an error if we try. The price however may change from time to time. So, we will use the *var* keyword instead when defining the price.
For both of these values, by indicating that they *can exist*, we are also declaring to the Swift compiler that they *must exist* by the time the init() function has been completed.
Behind the scenes Swift inspects struct's variables, and creates named arguments which can then be used to set default values for each one. In other words, we can create a Track object by using this default initializer:


```swift
let track = Track(title: "All Along The Watchtower", price: 1.29)
```

If we were to implement our own initializer to do the same thing, it would be written like this:

```swift
struct Track {
  let title: String
  var price: Float
  init(title: String, price: Float) {
    self.title = title
    self.price = price
  }
}
```

The rating for a Track is a little different, because initially it doesn't make sense to provide a default. Just because a track has not specifically been rated by a user yet, does not mean that it should use a default such as zero. Unrated songs aren't bad songs, right? You may think you could use a default of 2.5 instead, since it is right in the middle, but this is still innaccurate. If I haven't rated my favorite song of all time, that still doesn't make it "just average 2.5/5". So what we need instead is some way to say this value is simply unspecified initially. The value is optional and we need Swift to understand this when we model our data. Fortunately Swift has a language feature for exactly this type of situation and it's called, you guessed it, an *Optional*.
To indicate that a type is optional, we just append a question mark (?) to the end of the type annotation. So the type of rating is an optional Int, or *Int?*.
When we create a Track object in our code, we need to pass in initial values, but for rating there is no logical default to use. So, we can default to having this not be specified yet by just using the *nil* keyword, a keyword in Swift that means "nothing". An Int that is nil for example is not 0, 35, -1, or any number at all. It simply does not exist.

```swift
let myTrack = Track(title: "Shake It Off", price: 1.29, rating: nil)
```
*/

/*
Let's say we wanted to print a friendly description of any Track object to the console, it's easy to do this by just providing a description variable which returns a String.

struct Track {
    let title: String
    var price: Float
    var rating: Int?
    var prettyDescription: String {
        get {
            return "Title: \(title) $\(price) - \(rating) out of 5"
        }
    }
}
*/

/*
Now, when we want to inspect an individual Track object, we can just call print(myTrack.prettyDescription), and we'll get back our pretty formatted String.

```swift
print(myTrack.prettyDescription)
// Outputs: Title: Shake It Off $1.29 - nil out of 5
```

The *get* syntax for description used here is to allow for a computed variable. When description is accessed, the code inside of the curly braces after the *get* keyword is evaluated and the result is returned. With this, it's easy to produce a useful description of the Track object, based on the current value of the track's other variables.
In this case, we return a String which will show the values of title and price within the String. This is done by writing a slash followed by opening and closing parentheses, and then evaluating whatever is inside. So inside of the String "\(title)", the resulting String will contain whatever the variable *title* evaluates to.
We would also like to show the rating, so we have \(rating) inside of our String in the same way. However rating is an Optional, and in this example it is nil. So what we see printed to the console is "Title: Shake It Off $1.29 - nil out of 5", which is not exactly the most user-friendly way of showing that no rating has been specified yet. Wouldn't it be better to say "Not rated"?
We can adjust our get method (known as a getter) to check whether or not the Optional rating variable has been set using a simple concise syntax known as optional binding.

```swift
struct Track {
    let title: String
    var price: Float
    var rating: Int?
    var prettyDescription: String {
        get {
            if let unwrappedRating = rating {
                return "Title: \(title) $\(price) - \(unwrappedRating) out of 5"
            }
            else {
                return "Title: \(title) $\(price) - Not rated"
            }
        }
    }
}
```

*/

/*
Optional binding starts with the keywords *if let* followed by a new variable name, then the assignment operator (=), and then the name of the optional whose value we are attempting to access. The new variable, in this case *unwrappedRating* will contain the result of whatever is unwrapped from the optional, but only if the value exists. Next we open a set of curly braces and can execute whatever code uses the optional's value, knowing for sure that it is available.
Just like any other *if* statement, we can also provide an *else* clause, and react to a situation where the optional was nil.
So, in our example, when the rating can be *unwrapped* in to the variable unwrappedRating, we will return a string that shows "... - 5 out of 5" at the end of the String. But, if the optional was nil, and therefore the else clause is executed, then we will just see "... - Not Rated"
When an optional's value is accessed in this way, it's called "unwrapping" the optional, which is why I used the named *unwrappedRating*. However, in practice it is common to use the same variable name. This leads to an intentional form of variable shadowing, where the optional value is obscured by the unwrapped variable of the same name. So in practice, we would write the method just sticking with the name *rating*.

```swift
struct Track {
    let title: String
    var price: Float
    var rating: Int?
    var prettyDescription: String {
        get {
            if let rating = rating {
                return "Title: \(title) $\(price) - \(rating) out of 5"
            }
            else {
                return "Title: \(title) $\(price) - Not rated"
            }
        }
    }
}
```
*/

/*
This is not the only way to resolve this issue. We could get a little fancy and render actual stars in the console. Swift supports emoji so we can actually just type the stars right in to a string. We can build out a simple function that puts together a string showing the number of stars as filled in emoji. So 3 out of 5 stars would look like "Title: Shake It Off $1.29 - âœ­âœ­âœ­âœ©âœ©"

```swift
struct Track {
    let title: String
    var price: Float
    var rating: Int?
    func starsFromRating(myRating: Int?) -> String {
        var ratingStr = ""
        if let myRating = myRating {
            for i in 0..<5 {
                if i < myRating {
                    ratingStr += "âœ­"
                }
                else {
                    ratingStr += "âœ©"
                }
            }
            return ratingStr
        }
        return "Not rated"
    }
    var prettyDescription: String {
        get {
            return "Title: \(title) $\(price) - \( starsFromRating(rating) )"
        }
    }
}
```

We will modify our test Track to indicate 3 stars for testing purposes.

```swift
let myTrack = Track(title: "Shake It Off", price: 1.29, rating: 3)
```

*/

/*
Swift offers a shortcut for our println() function. Rather than accessing *myTrack.prettyDescription* and printing that, we can instead just call *print(myTrack)*. In order to enable this functionality, we need to conform to a *protocol* known as a *CustomStringConvertible*.
A protocol in Swift is like a struct or class that does not implement any of it's own functions or properties, and instead just describes the methods that are required for any other object to conform to it. So, how do we know what CustomStringConvertible requires of us for conformance? The easiest way is to just indicate that you intend to conform to the protocol, by add it after the name of our struct's name.

```swift
struct Track: CustomStringConvertible {
```

If you tried to compile this now, you would see an error.

> Type 'Track' does not conform to protocol 'CustomStringConvertible'

In the issue navigator in Xcode, we can expand this issue for more detail, which is where it goes on to say this:

> Protocol requires property 'description' with type 'String' (Swift.CustomStringConvertible)

By specifying we intend to conform to the CustomStringConvertible protocol, we are also getting the Swift compiler and Xcode to cause a build-breaking error if we fail to actually do that. In this case, we aren't conforming because this protocol requires that we have a String property called *description*.
If we rename our *prettyDescription* to *description*, we will be conforming to the protocol, and Swift will now understand that the description property is what we want displayed when we print an instance of Track.

```swift
struct Track: CustomStringConvertible {
    let title: String
    var price: Float
    var rating: Int?
    func starsFromRating(myRating: Int?) -> String {
        var ratingStr = ""
        if let myRating = myRating {
            for i in 0..<5 {
                if i < myRating {
                    ratingStr += "âœ­"
                }
                else {
                    ratingStr += "âœ©"
                }
            }
            return ratingStr
        }
        return "Not rated"
    }
    var description: String {
        get {
            return "\(title) $\(price) - \( starsFromRating(rating) )"
        }
    }
}
```

Now that we're conforming to the CustomStringConvertible protocol, we can just call print on our object directly.

```swift
print(myTrack)
```

Another way to investigate a protocol from within Xcode is to hold the Command key and click on the name in the text-editor. This will jump to the definiton of the protocol. For example with CustomStringConvertible, the definition in the standard library is as follows:

```swift
/// A type with a customized textual representation.
///
/// This textual representation is used when values are written to an
/// *output stream*, for example, by `print`.
///
/// - Note: `String(instance)` will work for an `instance` of *any*
///   type, returning its `description` if the `instance` happens to be
///   `CustomStringConvertible`.  Using `CustomStringConvertible` as a
/// generic constraint, or accessing a conforming type's `description`
/// directly, is therefore discouraged.
///
/// - SeeAlso: `String.init<T>(T)`, `CustomDebugStringConvertible`
protocol CustomStringConvertible {
    
    /// A textual representation of `self`.
    var description: String { get }
}
```

Most of what you see here is just documentation in the form of comments. If we ignore every line that is a comment (any line starting with 2 or more slashes (//), we are only left with this:

```swift
protocol CustomStringConvertible {
    var description: String { get }
}
```

What you see here is a *requirement* that CustomStringConvertible conformance assures the description property is available. Conforming to the CustomStringConvertible protocol is as simple as adding a String called *description* with a computed getter, as we just demonstrated.

Swift and the iOS and Mac OS SDKs have many protocols for all types of various purposes, this is just a simple example of one. We can also define our own. Most commonly you will want to do this when you find yourself adding the same types of properties or behaviors to multiple structs. For example, we have a property for rating songs, but what if we also wanted to rate our playlists?
First off, you would want to define your playlist as another struct. Here our Playlist has a name, and an array of Track objects, both which can be changed by the user, so we'll make them both vars.

```swift
struct Playlist {
  var name: String
  var tracks: [Track]
}
```
*/

/*
Instead of adding another rating optional on to Playlist, let's create a protocol that describes all objects that can be rated. We'll call it Rateable, which according to Merriam-Webster is a real word.

```swift
protocol Rateable {
}
```

So now we have a protocol, so we can modify the Playlist to implement it.

```swift
struct Playlist: Rateable {
  var name: String
  var tracks: [Track]
}
```

Since we haven't specified any kind of restriction on what it actually means to be Rateable, the Playlist conforms. For something to be rateable, all it really need is a rating property that provides both a getter and setter.
So let's change the Rateable protocol to have a property for rating. As before, rating will be optional because it's possible to have an object that just hasn't had a rating specified yet.

protocol Rateable {
  var rating: Int? { get set }
}

Our Playlist no longer conforms, but that's ok we just need to add the property in.

*/

protocol Rateable {
    var rating: Int? { get set }
}


/*

```swift
struct Playlist: Rateable {
  var name: String
  var tracks: [Track]
  var rating: Int?
}
```

Let's create some tracks and a few playlists. In an actual app the user would produce these using a series of UI elements, but here we can just hard-code them for now. */
let tracks = [
    Track(title: "Welcome To New York", price: 1.29, rating: 1),
    Track(title: "Blank Space", price: 1.29, rating: nil),
    Track(title: "Shake It Off", price: 1.29, rating: 3)
]
let myPlaylist = Playlist(name: "My Swift Playlist", tracks: tracks, rating: 5)

/*
If we now call print(myPlaylist) we can see the contents listed out using the default print mechanism.

> DemoApp.Playlist(name: "My Swift Playlist", tracks: [Welcome To New York $1.29 - âœ­âœ©âœ©âœ©âœ©, Blank Space $1.29 - âœ©âœ©âœ©âœ©âœ©, Shake It Off $1.29 - âœ­âœ­âœ­âœ©âœ©], rating: Optional(5))


Similar to before, let's create a custom desription and adhere to CustomStringConvertible to make this a little nicer. Since we're already conforming to Rateable, we just add a comma to say that we're conforming to multiple protocols.

```swift
struct Playlist: Rateable, CustomStringConvertible {
```

Next we can implement the description.

```swift
struct Playlist: Rateable, CustomStringConvertible {
    var name: String
    var tracks: [Track]
    var rating: Int?
    var description: String {
        get {
            return "\(name) \(rating)/5 \n\(tracks)"
        }
    }
}
```
*/

/*
> My Swift Playlist Optional(5)/5
> [Welcome To New York $1.29 - âœ­âœ©âœ©âœ©âœ©, Blank Space $1.29 - Not rated, Shake It Off $1.29 - âœ­âœ­âœ­âœ©âœ©]

This is a little bit better, but we left out the emoji stars and printing the rating just has all the same problems as before. We could copy in the starsFromRating we defined in Track...

```swift
struct Playlist: Rateable, CustomStringConvertible {
    var name: String
    var tracks: [Track]
    var rating: Int?
    var description: String {
        get {
            return "\(name) \(starsFromRating(rating)) \n\(tracks)"
        }
    }
    func starsFromRating(myRating: Int?) -> String {
        var ratingStr = ""
        if let myRating = myRating {
            for i in 0..<5 {
                if i < myRating {
                    ratingStr += "âœ­"
                }
                else {
                    ratingStr += "âœ©"
                }
            }
            return ratingStr
        }
        return "Not rated"
    }
}
```
*/

/*

This works but it's just a complete copy of the starsFromRating function we defined earlier. This is a huge violation of the DRY principle (Don't Repeat Yourself). We could move the function out in to global space and just call it that way, but that doesn't seem very elegant; so what do we do?
This is where protocol extensions come in handy. Let's modify both the Playlist and Track to remove the starsFromRating functions.
*/

struct Track: CustomStringConvertible {
    let title: String
    var price: Float
    var rating: Int?
    var description: String {
        get {
            return "\(title) $\(price) - \( starsFromRating(rating) )"
        }
    }
}

struct Playlist: Rateable, CustomStringConvertible {
    var name: String
    var tracks: [Track]
    var rating: Int?
    var description: String {
        get {
            return "\(name) \(starsFromRating(rating)) \n\(tracks)"
        }
    }
}

/*
We'll get some errors here because starsFromRating is no longer defined, so let's define it in the Rateable protocol.
*/

extension Rateable {
    func starsFromRating(myRating: Int?) -> String {
        var ratingStr = ""
        if let myRating = myRating {
            for i in 0..<5 {
                if i < myRating {
                    ratingStr += "âœ­"
                }
                else {
                    ratingStr += "âœ©"
                }
            }
            return ratingStr
        }
        return "Not rated"
    }
}

/*
As we saw before, protocols do not provide method implementations. They simply define the the method's name and it's arguments. Protocol *extensions* however can be used to *add functionality to all conforming objects*. An extension is typically used to add more functionality to already defined objects. Usually anything that can be done in an extension can also just be done in the initial declaration. This is not the case for protocol extensions however. Using extensions is the only way to actually provide the body for methods.
In this case we define starsFromRating. Now simply by making Playlist a Rateable, we get the starsFromRating method tacked on to the struct itself for free. We could do the exact same thing with Track, but just for fun we'll make Track conform to Rateable in an extension of it's own. We can extend the Track struct by creating an extension, specifying the conformance, and then providing an empty body for the actual extension.
*/
extension Track: Rateable { }

/*

```swift
print(myPlaylist)
```

> My Swift Playlist âœ­âœ­âœ­âœ­âœ­
> [Welcome To New York $1.29 - âœ­âœ©âœ©âœ©âœ©, Blank Space $1.29 - Not rated, Shake It Off $1.29 - âœ­âœ­âœ­âœ©âœ©]

*/

struct Modeler {
    init() {
        print(myPlaylist)
//        print(myTrack)
        //print(myTrack)
    }
}






