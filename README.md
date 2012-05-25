ATFunctionalCollectionExtensions
================================

Functional programming constructs for Objective-C collections (map, reduce, each, filter, inject)

Objective-C collections are pretty fantastic: immutability by default, and lots of other very solid features. They can be, however, quite a pain to deal with, for two reasons:

 1. Verbosity. Operations on collections is something that needs to be done very often, and often multiple operations are chained together. A simple Clojure `(/ (reduce + coll) (count coll)))` (to average a collection) involves a loop and several lines of code in Objective-C.

 2. Object wrappers. I don't have an answer for this, yet. But it's super annoying and creates a lot of boilerplate code to have to wrap native values in NSValue or NSNumber. And of course, it doesn't work with everything, like a user-defined `struct`.

## Example

I'm attempting to deal with problem #1 by creating categories to `NSArray` and `NSDictionary` that implement common functional programming collection operations.

The example above (averaging a collection) goes from this relatively unclean loop:

````objc
NSUInteger sum = 0;
for (NSNumber *number in collection) {
    sum += [number intValue];
}
CGFloat avg = sum / collection.count;
````

To:

````objc
CGFloat avg = [collection reduce:^(NSNumber *a, NSNumber *b) { return [NSNumber numberWithInt:(a.intValue + b.intValue)]; }].intValue / collection.count;
````


## API

````objc
@interface NSArray (ATFunctionalCollectionExtensions)

// Calls the block for each item in the collection.
- (void)each:(void(^)(id object))block;

// Calls the block for each item in the collection, collects the return values and returns them in a new array.
- (NSArray *)map:(id(^)(id object))block;

// Reduces the array using the block provided.
// Return value should itself be reducible.
- (id)reduce:(id(^)(id a, id b))block;

// Filters the array using the provided block.
// The block should return YES if the object should be kept.
- (NSArray *)filter:(BOOL(^)(id object))block;

// Accumulates the array using the provided block and the initial value for the accumulator.
- (id)inject:(id(^)(id accumulator, id object))block initial:(id)accumulator;

@end



@interface NSDictionary (ATFunctionalCollectionExtensions)

// Calls the block for each item in the collection.
- (void)each:(void(^)(id key, id object))block;

// Calls the block for each item in the collection, collects the return values and returns them in a new dictionary.
- (NSDictionary *)map:(id(^)(id key, id object))block;

// Filters the array using the provided block.
// The block should return YES if the object should be kept.
- (NSDictionary *)filter:(BOOL(^)(id key, id object))block;

// Accumulates the dictionart using the provided block and the initial value for the a
ccumulator.
- (id)inject:(id(^)(id accumulator, id key, id object))block initial:(id)accumulator;

// Returns all keys.
- (NSArray *)keys;

// Returns all values;
- (NSArray *)objects;

@end

````


