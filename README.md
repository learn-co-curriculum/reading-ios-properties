#Properties  

###Private instance variables within a method 
#####(or with "local" scope)
Every variable we create within an instance method has been an "instance variable" by definition. It has "local" scope, because it is limited to being used within the method in which it has been created. In other words, if you were to try to access that variable in another method, even within the same class, the compiler would not recognize it, and your program would not run.

###Private instance variables available to an entire class 
#####(or with "protected" scope)
But what if we want to share the information stored in a variable across multiple methods within a class? We could pass the variable from method to method via a method's arguments. However, this might get tedious quickly if the variable we wish to pass appears in many places.

Fortunately, we are also able to add variables to our `@interface`, giving our instance variable "protected" scope, or scope across an entire instance of an object. If we add the variable to an `@interface` in our `.m` file, it will still be private. However, if we add the variable to an `@interface` our `.h` file, it will be public. As a reminder, public vs. private is really just another way to describe who can read and/or write to variables of a class and call methods of a class. When a variable is public, other classes have access to it. (But you'll have to read on to find out how to actually use a public variable in another class!)

It has become convention to place an underscore in front of an instance variable with "protected" scope.

######Example

```objc
//Product.h

@interface Product : NSObject {
	NSNumber *_size;
}

//Any other interface related details go outside of the brackets, such as method declarations

@end
```

Having defined this `_size` variable, we can now set its value and get its value throughout the class!

Keep in mind: Instance variables with protected scope are held in memory until the entire instance of the object is released from memory. For instance, a variable that holds a `UIView` would be held in memory until the `UIViewController` class it is instantiated within has also been released from memory.



###Public instance variables 
#####(or instance variables with "global" scope)

So what if we want to access this variable from outside of the class? What if we want to set its state when something changes in another class? We can do that with "accessor" methods.

There are two standard types of accessor methods: "Getters" and "Setters". Let's take a look at each.



###Getter

In order to get the value of the `_size` instance variable from outside the class `Product`, we must declare and define a method that "gets" that value. We call this method, quite appropriately, the "getter" method. Here is an example of a default getter for the `NSNumber` instance variable called `_size`. 

######Example
```objc
//Product.m

-(NSNumber *)size {
    return _size;
}

```
###Setter

Similarly, we must declare and define a method that "sets" the value of this instance variable. Here is a default "setter" method.

######Example
```objc
//Product.m

-(void)setSize:(NSNumber *)size {
    _size = size;
}
```

Getters and setters are collectively known as "accessors", and with them, we can store information at a memory address to which we give a name (a.k.a. an instance variable).

Don't forget to declare these methods and the instance variable itself in the `.h` of your `Product` class!


##Properties

So...you've been lied to. Kind of. But it was the good kind of lie. Because you've learned the old, harder way of doing things, and now you are going to learn a more elegant method.

You see, over time, the folks at Apple got smart about instance variables and recognized that all that work was a bit repetitious for basic uses of instance variables. And thus, "properties" were born.

Properties are a form of abstraction. They encapsulate an instance variable, a setter, and a getter method and we only have to write one line of code!

######Example
```objc
//Product.h

@interface Product : NSObject 

@property (strong, nonatomic) NSNumber *size;

@end
```

With just a single line of code, we have indicated that we are creating an instance variable, and we have an implicit setter and getter. To be clear, we can still write our accessor methods as we did before, but it is not necessary unless we wish to customize them with additional code.

So, let's walk through what we have written above in more detail.

First, take note of the fact that properties do not get written within brackets as instance variables do.

In order to declare a property, we start with the syntax `@property`. Pretty straightforward.

####Property attributes

Then we provide any attributes of this property in parentheses.

#####`readwrite`/`readonly`
We have already discussed the difference between public and private variables, but sometimes you would like a variable to be publically available for other classes to see, but not for other classes to be able to change. When this is the case we add `readonly` to the property attributes and we also redeclare the property in the `@interface` of our `.m` file with the `readwrite` attribute set. (If we do not have it set to `readwrite` in our `.m` file, no one will be able to set the value of the property!)

#####Other attributes
Other attributes you will want to learn about in the future include: `atomic`, `weak`, `copy`, `unsafe_unretained`, and attributes to rename your getter and setter methods. 

However, as a beginner, let's stick with the standard `(strong, nonatomic)` attributes with the occasional `readonly`/`readwrite` added in.

####Property type and name
Finally, we tell the program what type the property is, and then give it a name. As you can see, we do not include an underscore before the name of the property. However if we access the instance variable within the property, it will by default, include the underscore. (We won't get into that here, but just know that if you see the underscore, you are again looking at an instance variable.)

###Accessing properties

So if we're in another class and we wish to set the `size` property (a.k.a. instance variable) of our `Product`, how do we do it?

Well, we could call the accessor methods (`[aProduct size]`), but there is another, slightly more concise option: Dot notation. Let's take a look at an example in which we call the `size` property of our `Product` class from another class called `SizeConverter`.

######Example
```objc
//SizeConverter.m

- (NSNumber *)ouncesToPounds:(Product *)aProduct {
	return aProduct.size / 16.0;
}
```

Within the same class, we would use `self.size`.

###`@synthesize`

You may see a line of code in older objective-C projects that looks something like this in the `.m` file:

```objc
@synthesize product = _product;
```
This line of code would autogenerate your setter and getter and also create the instance variable for your property.

In more recent versions of xcode, Apple made it such that simply by declaring a property, you autogenerated all of the boilerplate underlying code, so you no longer have to concern yourself with this (but now you know what it means if you come across it!)

###Common mistakes

- When writing a getter / setter / or initializer, it is common to see folks do something like this:

######Example
```objc
//Product.m

-(void)setSize:(NSNumber *)size {
    self.size = size;
}
```

What is wrong with the above? By trying to set the property rather than the instance variable with in the setter (or returning the property from the getter), we will find ourselves within an infinite loop. Recall that the property is actually shorthand for an instance variable, a setter method, and a getter method. So the following is another way to write what we have just above:

######Example
```objc
//Product.m

-(void)setSize:(NSNumber *)size {
    [self setSize:size];
}
```

See the problem? We have effectively called the same method, and doing so will happen again and again until our app crashes!

So the rule is, in setters, getters, and initializers we use the instance variable (`_size`); everywhere else, use the property.

Summary

You have now learned the basics of properties: how to create them, how to use them, and where they come from. Get used to using them. And if you see someone declare an instance variable in their `@interface`, show them the way of the property!
