
# Property Basics

## Objectives

1. Recognize how properties can simplify code.
2. Declare a property correctly.
3. Access a property from outside the class file.
4. Access a property from within the class file using `self.` ("self dot").  

## The Usefulness of Properties

When writing code, the real power of utilizing properties reveals itself in being able to write methods that can access values stored in properties without needing to receive those values as arguments. Did you notice in the Apples & Holidays lab that every one the "holiday" methods had to accept the dictionary as an argument? 

```objc
// FISAppDelegate.h

@interface FISAppDelegate : UIResponder <UIApplicationDelegate>

- (NSArray *)holidaysInSeason:(NSString *)season
                   inDatabase:(NSDictionary *)database;

- (NSArray *)suppliesInHoliday:(NSString *)holiday
                      inSeason:(NSString *)season
                    inDatabase:(NSDictionary *)database;

- (BOOL)holiday:(NSString* )holiday
     isInSeason:(NSString *)season
     inDatabase:(NSDictionary *)database;

- (BOOL)supply:(NSString *)supply
   isInHoliday:(NSString *)holiday
      inSeason:(NSString *)season
    inDatabase:(NSDictionary *)database;

- (NSDictionary *)addHoliday:(NSString *)holiday
                    toSeason:(NSString *)season
                  inDatabase:(NSDictionary *)database;

- (NSDictionary *)addSupply:(NSString *)supply
                  toHoliday:(NSString *)holiday
                   inSeason:(NSString *)season
                 inDatabase:(NSDictionary *)database;
                 
@end
```

It would have been much easier to write those methods to access the dictionary as a property belonging to `FISAppDelegate`:

```objc
// FISAppDelegate.h

@interface FISAppDelegate : UIResponder <UIApplicationDelegate>

@property (strong, nonatomic) NSDictionary *database;

- (NSArray *)holidaysInSeason:(NSString *)season;

- (NSArray *)suppliesInHoliday:(NSString *)holiday
                      inSeason:(NSString *)season;

- (BOOL)holiday:(NSString* )holiday
     isInSeason:(NSString *)season;

- (BOOL)supply:(NSString *)supply
   isInHoliday:(NSString *)holiday
      inSeason:(NSString *)season;

- (void)addHoliday:(NSString *)holiday
          toSeason:(NSString *)season;

- (void)addSupply:(NSString *)supply
        toHoliday:(NSString *)holiday
         inSeason:(NSString *)season;

@end 
```
Each of the methods could have accessed the `database` property from within their own implementations. Also, the test file would not have needed to be responsible for the `database` dictionary and the final two methods would not have needed to provide a return since the `database` dictionary would have been owned as a property by the `FISAppDelegate` instance.

**Top-tip:** *It's standard practice to group all of the property declarations together and write them above the method declarations. Don't mix your property declarations with your method declarations.*

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
