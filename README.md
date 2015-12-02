
# Property Basics

## Objectives

1. Recognize how properties can simplify code.
2. Declare a property correctly.
3. Access a property from outside the class file.
4. Access a property from within the class file using `self.` ("self dot").  

## The Usefulness of Properties

When writing code, the real power of utilizing properties reveals itself in being able to write methods that can access values stored in properties without needing to receive those values as arguments. Do you recall from previous labs that we had to pass objects around as method arguments? It was a lot of repetition and we couldn't guarantee that the method would receive the same argument objects every time it was called. 

By giving the class ownership of that object as a property we can then access it within a method implementation knowing that it will be the same object every time. Furthermore, we can anticipate that every instance of a given class will own an object associated with that property. We can then use those owned objects (properties) to distinguish or make behavioral decisions for the property's owner:

```objc
FISCar *car = [[FISCar alloc] init];
car.driver = @"Mark";

if ([car.driver isEqualToString:@"Mark"]) {
    [car unlock];
} else {
    [car soundCarAlarm];
}
```
This code snippet assumes a car instance object with a string property called `driver`. If the contents of the `driver` property matches the string submitted to the `isEqualToString:` method, then the `car` object will perform the custom `unlock` method, otherwise it will perform the custom `soundCarAlarm` method. As long as the `FISCar` class is defined with a string property called `driver` (and two custom methods called `unlock` and `soundCarAlarm`), this code snippet can be run on any instance object of the `FISCar` class because we can guarantee that it will own a string object called `driver`.

## Declaring Properties

A property declaration can only be placed inside the `@interface` section of a class file. In most cases you will use the following syntax:

```objc
@property (strong, nonatomic) Class *objectName;
@property (nonatomic) PrimitiveType variableName; 
```
Which, assuming that we have a `FISWarship` class, might be implemented like this:

```objc
// FISWarship.h

@interface FISWarship : NSObject

@property (strong, nonatomic) NSString *shipName;
@property (nonatomic) NSUInteger currentSpeedInKnots;
@property (nonatomic) NSUInteger maximumSpeedInKnots;

- (void)fullSpeedAhead;
- (void)fullStop;

@end
```
**Top-tip:** *It's standard practice to group all of the property declarations together and write them above the method declarations. Don't mix your property declarations with your method declarations.*

Just like in a local scope, primitives are declared as properties **without** using a pointer reference noted with a `*` ("star"). Primitives are also declared as properties **without** the `strong` keyword, which notes the retention type.

Even though `strong` and `nonatomic` are default settings that you don't need to understand just yet, Objective-C still requires that you declare them.

## Access Properties With `self.` ("self dot")

Remember that properties can be accessed using dot notation. If we have an instance of our `FISWarship` class, we can set the values of its properties after initializing the instance:

```objc
FISWarship *ussIowa = [[FISWarship alloc] init];
ussIowa.shipName = @"USS Iowa";
ussIowa.maximumSpeedInKnots = 33;
ussIowa.currentSpeedInKnots =  0;
```
However, from within the class file that owns a given property (i.e. that the property is declared in), the property can be accessed with dot notation by using the `self` keyword.

Since our `FISWarship` class should be capable of changing speed, we should write a `fullSpeedAhead` method that sets the `currentSpeedInKnots` property to the match the `maximumSpeedInKnots` value, and a `fullStop` method that sets it to zero: 

```objc
// FISWarship.m

@implementation FISWarship

- (void)fullSpeedAhead {
    self.currentSpeedInKnots = self.maximumSpeedInKnots;
    NSLog(@"Full speed ahead, aye!");
}

- (void)fullStop {
    self.currentSpeedInKnots = 0;
    NSLog(@"Full stop, aye!");
}

@end
```

Then, when an instance of our `FISWarship` class such as `ussIowa` receives a message containing one of these method calls, it will access the value of the relevant property on *itself*:

```objc
[ussIowa fullSpeedAhead];
NSLog(@"Speed: %lu knots", ussIowa.currentSpeedInKnots);

[ussIowa fullStop];
NSLog(@"Speed: %lu knots", ussIowa.currentSpeedInKnots);
```
This will print:

```
Full speed ahead, aye!
Speed: 33 knots
Full stop, aye!
Speed: 0 knots
```

<a href='https://learn.co/lessons/reading-ios-properties' data-visibility='hidden'>View this lesson on Learn.co</a>
