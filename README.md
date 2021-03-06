# green_curry: Curried functional programming library
## Colorful and delicious!
(ES6 required)

(please disregard the culture references in the examples)

The contents of this package are organized into submodules:

[F: general functions and constants](https://github.com/lightalkmst/green_curry#f-general-functions-and-constants)

[L: lists (arrays)](https://github.com/lightalkmst/green_curry#l-1-list-and-2-lists-functions)

[D: dictionaries (objects)](https://github.com/lightalkmst/green_curry#d-dictionary-functions)

[S: strings](https://github.com/lightalkmst/green_curry#s-string-functions)

This library is to provide support for functional programming. Though some other libraries exist that expose functional programming in JavaScript, they take a partially object-oriented approach that dilutes a lot of the value of a functional style. Though this mixture can be useful, those libraries have not closed the gap caused by some of JavaScript's idiosyncracies.

I believe that what's held JavaScript back from being adopted as a functional programming language is its lack of a purely functional library. Functional programming's rewards are reaped best when currying is available, enabling point-free form to increase the abstractness and modularity of the code. One of the strengths of functional programming is that a vast majority of boilerplate is already replaced by higher-order library functions. As these functions are all very general, common boilerplate is replaced with simple functions that leave just your desired logic in place. Additionally, since higher-order functions are fueled by inversion of control, the same functions are used and recycled; they may never become outdated.

Here is an example:
```javascript
// summing up a list
function sum_list(list) {
  var ans = 0;
  for (var i = 0; i < list.length; i++) {
    ans += list[i];
  }
  return ans;
}

// summing up a list of lists
function sum_lists(list) {
  var ans = 0;
  for (var i = 0; i < list.length; i++) {
    ans += sum_list(list[i]);
  }
  return ans;
}

// summing up a list of lists of lists
function sum_lists2(list) {
  var ans = 0;
  for (var i = 0; i < list.length; i++) {
    ans += sum_lists(list[i]);
  }
  return ans;
}
```
The above code, functionally:
```javascript
const green_curry = require ('green_curry')
green_curry.globalize ()

// summing up a list
const sum_list = L.fold (F['+']) (0)

// summing up a list of lists
const sum_lists = F.c () (L.map (sum_list) >> sum_list)

// summing up a list of lists of lists
const sum_lists2 = F.c () (L.map (sum_lists) >> sum_list)
```
An understanding of the typed lambda calculus, currying, JavaScript type system, closures, and mutability are recommended for effective use of this library. All functions are free of self-references, allowing their safe use as first-class functions. All functions are pure, except globalize, F.c, and F.p.

## globalize
Pulls the included submodules into global scope to obviate the need for fully-qualifying each resource

All examples on this page will assume this has already been called

(note: works both in-server and in-browser)
```javascript
var green_curry = require ('green_curry')
green_curry.F.log ('Hint: 3?') // prints 'Hint: 3?'
green_curry.globalize () // raises green_curry library to global scope
F.log ('Hint: 3?') // prints 'Hint: 3?'
```
## F (general functions and constants)
(note: regard the operators as prefix notation)

#### F.e : obj
Constant exception object thrown by this library

#### F.id : 'a -> 'a
The identity function
```javascript
F.id ('Hint: 3?') // 'Hint: 3?'
```
#### F.const : 'a -> unit -> 'a
Generates a constant function
```javascript
var f = F.const ('Hint: 3?')
f () // 'Hint: 3?'
var f = F.const ('9')
f () // '9'
```
#### F.ignore : 'a -> unit
Does nothing
```javascript
F.ignore ('Hint: 3?') // does nothing
```
#### F.exec : (unit -> 'a) -> 'a
Executes the given function
```javascript
F.exec (() => 'Hint: 3?') // 'Hint: 3?'
```
#### F.ex_if : bool -> unit
If passed true

Then throws the exception F.e
```javascript
F.ex_if (true) // throws exception
F.ex_if (false) // does nothing
```
#### F.log : 'a -> unit
An alias for console.log.bind (console)

Aliasing and calling console.log by itself without binding will throw an exception
```javascript
var f = console.log
f ('Hint: 3?') // throws null pointer exception
var f = F.log
f ('Hint: 3?') // prints 'Hint: 3?'
```
#### F.eval : string -> 'a
A wrapper for eval that will always operate within calling scope

An unaliased call to eval will operate within the calling scope

An aliased call to eval will operate at the global scope
```javascript
var a = 'Hint: 3?'
eval ('a') // 'Hint: 3?'
var f = eval
f ('a') // undefined
var f = F.eval
f ('a') // 'Hint: 3?'
```
#### F.= : 'a -> 'a -> bool
#### F.== : 'a -> 'a -> bool
Compares the arguments with soft equality
```javascript
F['=='] ('Hint: 3?') ('Hint: 3?') // true
F['=='] ('Hint: 3?') ('9') // false
```
#### F.=== : 'a -> 'a -> bool
Compares the arguments with hard equality
```javascript
F['==='] ('Hint: 3?') ('Hint: 3?') // true
F['==='] ('Hint: 3?') ('9') // false
```
#### F.!= : 'a -> 'a -> bool
#### F.<> : 'a -> 'a -> bool
Compares the arguments with soft inequality
```javascript
F['!='] ('Hint: 3?') ('Hint: 3?') // false
F['!='] ('Hint: 3?') ('9') // true
```
#### F.!== : 'a -> 'a -> bool
Compares the arguments with hard inequality
```javascript
F['!=='] ('Hint: 3?') ('Hint: 3?') // false
F['!=='] ('Hint: 3?') ('9') // true
```
#### F.> : int -> int -> bool
Returns if (arg1) is greater than (arg2)
```javascript
F['>'] (3) (3) // false
F['>'] (3) (9) // false
F['>'] (9) (3) // true
```
#### F.>= : int -> int -> bool
Returns if (arg1) is greater than or equal to (arg2)
```javascript
F['>='] (3) (3) // true
F['>='] (3) (9) // false
F['>='] (9) (3) // true
```
#### F.< : int -> int -> bool
Returns if (arg1) is lesser than (arg2)
```javascript
F['<'] (3) (3) // false
F['<'] (3) (9) // true
F['<'] (9) (3) // false
```
#### F.<= : int -> int -> bool
Returns if (arg1) is lesser than or equal to (arg2)
```javascript
F['<='] (3) (3) // true
F['<='] (3) (9) // true
F['<='] (9) (3) // false
```
#### F.! : bool -> bool
Negates the argument
```javascript
F['!'] (true) // false
```
#### F.~ : int -> int
2's complements the argument
```javascript
F['~'] (3) // -4
```
#### F.+ : int -> int
Adds the arguments
```javascript
F['+'] (3) (3) // 6
```
#### F.- : int -> int
Subtracts the arguments
```javascript
F['-'] (3) (3) // 0
F['-'] (3) (0) // 3
```
#### F.* : int -> int
Multiplies the arguments
```javascript
F['*'] (3) (3) // 9
```
#### F./ : int -> int
Divides the arguments
```javascript
F['/'] (3) (3) // 1
F['/'] (3) (1) // 3
```
#### F.% : int -> int
Modulo divides the arguments
```javascript
F['%'] (3) (3) // 0
F['%'] (3) (1) // 0
```
#### F.| : int -> int
Bitwise Or the arguments
```javascript
F['|'] (3) (3) // 3
```
#### F.|| : bool -> bool
Logical Or the arguments

(note: the arguments are evaluated eagerly so this does not short-circuit)
```javascript
F['||'] (true) (true) // true
```
#### F.& : int -> int
Bitwise And the arguments
```javascript
F['&'] (3) (3) // 3
```
#### F.&& : bool -> bool
Logical And the arguments

(note: the arguments are evaluated eagerly so this does not short-circuit)
```javascript
F['&&'] (true) (true) // true
```
#### F.^ : int -> int
Bitwise Xor the arguments
```javascript
F['^'] (3) (3) // 0
```
#### F.>>> : int -> int
Sign-propagating right shifts the arguments
```javascript
F['>>>'] (9) (2) // 2
F['>>>'] (-9) (2) // -3
```
#### F.>>>> : int -> int
Zero-fill right shifts the arguments
```javascript
F['>>>>'] (9) (2) // 2
F['>>>>'] (-9) (2) // -1073741821
```
#### F.<<< : int -> int
Left shifts the arguments
```javascript
F['<<<'] (9) (2) // 36
```
#### F.?? : 'a -> 'a -> 'a
If (arg1) is defined

Then return (arg1)

Else return (arg2)

(note: the arguments are evaluated eagerly so this does not short-circuit)
```javascript
F['??'] (9) (3) // 9
F['??'] (undefined) (3) // 3
```
#### F.?: : bool -> 'a -> 'a -> 'a
(note: the arguments are evaluated eagerly so this does not short-circuit)
```javascript
F['?:'] (true) (3) (9) // 3
F['?:'] (false) (3) (9) // 9
```
#### F.|> : 'a -> ('a -> 'b) -> 'b
#### F.@@ : 'a -> ('a -> 'b) -> 'b
Passes (arg1) into (arg2)
```javascript
F['|>'] (3) (F['+'] (3)) // 6
```
#### F.<| : ('a -> 'b) -> 'a -> 'b
Passes (arg2) into (arg1)
```javascript
F['<|'] (F['+'] (3)) (3) // 6
```
#### F.<< : ('a -> 'b) -> ('c -> 'a) -> ('c -> 'a)
Function composes the arguments
```javascript
var f = F['>>'] (F['+'] (3)) (F['*'] (3))
f (3) // 12
```
#### F.>> : ('a -> 'b) -> ('c -> 'a) -> ('c -> 'a)
Reverse function composes the arguments
```javascript
var f = F['>>'] (F['+'] (3)) (F['*'] (3))
f (3) // 18
```
#### F.neg : ('a -> bool) -> ('a -> bool)
Negates the predicate
```javascript
var f = F.neg (F['='] ('Hint: 3?'))
f ('Hint: 3?') // false
```
#### F.union : ('a -> bool) -> ('a -> bool) -> ('a -> bool)
Returns a predicate that is a union of the predicates
```javascript
var f = F.union (x => x % 2 == 0) (x => x % 5 == 0)
f (3) // false
f (4) // true
f (5) // true
f (10) // true
```
#### F.inter : ('a -> bool) -> ('a -> bool) -> ('a -> bool)
Returns a predicate that is an intersection of the predicates
```javascript
var f = F.inter (x => x % 2 == 0) (x => x % 5 == 0)
f (3) // false
f (4) // false
f (5) // false
f (10) // true
```
#### F.try : bool -> (unit -> 'a) list -> 'a
Invokes the first function in (arg2)

If the function throws an exception

Then

&nbsp;&nbsp;&nbsp;&nbsp;If (arg1) is true

&nbsp;&nbsp;&nbsp;&nbsp;Then the exception is printed to console

&nbsp;&nbsp;&nbsp;&nbsp;Recurse this function with (arg1) and the remainder of (arg2)

Else the result is returned
```javascript
F.try (true) ([
    () => throw F.e,
    () => throw F.e,
    () => throw F.e,
    () => 'Hint: 3?',
]) // 'Hint: 3?' // each exception is printed
```
#### F.swap : ('a -> 'b -> 'c) -> ('b -> 'a -> 'c)
Swaps the order of the next two arguments of the given function
```javascript
var f = F.swap (F['-'])
f (3) (0) // -3
```
#### F.delay : int -> (unit -> unit) -> unit
Calls the given function after waiting the given time in ms
```javascript
F.delay (3) (() => F.log ('Hint: 3?')) // waits 3ms and then prints 'Hint: 3?'
```
#### F.tap : ('a -> 'b) -> 'a -> 'a
Passes argument 2 to argument 1 and then returns argument 2
(note: for side-effecting when you want to retain the reference)
```javascript
F.tap (F.log) ('Hint: 3?') // 'Hint: 3?' // prints 'Hint: 3?'
```
#### F.rcomp : (? -> ?) list -> (? -> ?)
Reverse function composes the argument
```javascript
var f = F.rcomp ([
    F.tap (F.log),
    F['='] ('Hint: 3?'),
    F.tap (F.log),
])
f ('Hint: 3?') // true // prints 'Hint: 3?' then 'true'
```
#### F.c : unit -> (? -> ?) list -> (? -> ?)
Reverse function composes the argument, but with a temporary DSL that is applied only one level deep

(note: requires at least two functions to be composed to work properly)

(note: this is safe for nested usage in other instances of F.c and F.p)
```javascript
var f = F.c () (
    F.tap (F.log)
    >> F['='] ('Hint: 3?')
    >> F.tap (F.log)
)
f ('Hint: 3?') // true // prints 'Hint: 3?' then 'true'
```
#### F.p : ? -> (? -> ?) list -> ?
Passes (arg1) to the reverse function composed (arg2), but with a temporary DSL that is applied only one level deep

(note: requires at least two functions to be composed to work properly)

(note: this is safe for nested usage in other instances of F.c and F.p)
```javascript
F.p ('Hint: 3?') (
    F.tap (F.log)
    >> F['='] ('Hint: 3?')
    >> F.tap (F.log)
) == true // prints 'Hint: 3?' then 'true'
```
#### F.memoize : ('a -> 'b) -> ('a -> 'b)
Returns a memoized version of the function

The memoization has O(n) lookup
```javascript
var f = F.memoize (F.log)
f ('Hint: 3?') // prints 'Hint: 3?'
f ('Hint: 3?') // does nothing
```
#### F.times : int -> (unit -> unit) -> unit
Invokes (arg2) (arg1) times
```javascript
F.times (3) (() => F.log ('Hint: 3?')) // prints 'Hint: 3?' 3 times
```
#### F.after : int -> ('a -> 'b') -> ('a -> unit/'b)
Returns a version of (arg2) that does nothing and returns undefined until the (arg1)th time when it reverts to normal
```javascript
var f = F.after (3) (F.tap (F.log))
f ('Hint: 3?') // undefined // does nothing
f ('Hint: 3?') // undefined // does nothing
f ('Hint: 3?') // 'Hint: 3?' // prints 'Hint: 3?'
```
#### F.before : int -> ('a -> 'b') -> ('a -> unit/'b)
Returns a version of (arg2) that operates normally until the (arg1)th time when it starts doing nothing and returns undefined
```javascript
var f = F.before (3) (F.tap (F.log))
f ('Hint: 3?') // 'Hint: 3?' // prints 'Hint: 3?'
f ('Hint: 3?') // 'Hint: 3?' // prints 'Hint: 3?'
f ('Hint: 3?') // undefined // does nothing
```
## L (1 list and 2 lists functions)
(note: lists are assumed to be dense, meaning all data is contiguous)

(note: all respective orders are preserved, except in obvious cases)

### 1 list functions

#### L.cons : 'a -> 'a list -> 'a list
Appends (arg1) to the front of (arg2)
```javascript
L.cons (1) ([2, 3, 4]) // [1, 2, 3, 4]
```
#### L.head : 'a list -> 'a
Returns the first element of the list
```javascript
L.head ([1, 2, 3]) // 1
```
#### L.tail : 'a list -> 'a list
Returns all elements of the list except the first
```javascript
L.tail ([1, 2, 3]) // [2, 3]
```
#### L.length : 'a list -> int
Returns the length of the list
```javascript
L.length ([1, 2, 3]) // 3
```
#### L.is_empty : 'a list -> bool
Returns true if the list is empty, false otherwise
```javascript
L.is_empty ([]) // true
L.is_empty ([1, 2, 3]) // false
```
#### L.get : int -> 'a list -> 'a
If (arg1) is greater than (arg2)'s length

Then returns undefined

Else returns the element at index (arg1) in (arg2) otherwise
```javascript
L.get (3) ([1, 2, 3]) // throws F.e
L.get (3) ([1, 2, 3, 4]) // 4
```
#### L.range : int -> int -> int list
If (arg1) is less than or equal to (arg2)

Then returns a list of increasing consecutive elements ranging from (arg1) to (arg2), both inclusive

Else returns an empty list
```javascript
L.range (3) (9) // [3, 4, 5, 6, 7, 8, 9]
L.range (3) (3) // [3]
L.range (9) (3) // []
```
#### L.create : int -> 'a -> 'a list
Returns a list of (arg1) (arg2) elements
```javascript
L.create (9) (3) // [3, 3, 3, 3, 3, 3, 3, 3, 3]
```
#### L.init : int -> (int -> 'a) -> 'a list
Returns a list of (arg1) elements generated by (arg2) passed the index of its element
```javascript
L.init (3) (i => i + 3) // [3, 4, 5]
```
#### L.rev : 'a list -> 'a list
Returns a reverse of the list
```javascript
L.rev ([1, 2, 3]) // [3, 2, 1]
```
#### L.iter : ('a -> unit) -> 'a list -> unit
For each element in (arg2)

&nbsp;&nbsp;&nbsp;&nbsp;Passes the element to (arg1)
```javascript
L.iter (F.log) ([1, 2, 3]) // prints '1' then '2' then '3'
```
#### L.iteri : (int -> 'a -> unit) -> 'a list -> unit
Same as L.iter, except additionally passes the index as well
```javascript
L.iteri (i => h => F.log (`${i}: ${h}`)) ([1, 2, 3]) // prints '0: 1' then '1: 2' then '2: 3'
```
#### L.fold : ('a -> 'b -> 'a) -> 'a -> 'b list -> 'a
(arg2) is the initial state of the accumulator

(arg1) is iteratively passed the previous accumulator and the next element in (arg2)

Returns the final accumulator
```javascript
F.fold (F['+']) (9) ([1, 2, 3]) // 15
F.fold (F['-']) (9) ([1, 2, 3]) // 3
```
#### L.reduce : ('a -> 'a -> 'a) -> 'a list -> 'a
If (arg2) is empty

Then throws the exception F.e

Else same as L.fold, except the first element in (arg1) is taken as the initial accumulator
```javascript
F.reduce (F['+']) ([1, 2, 3]) // 6
F.reduce (F['-']) ([1, 2, 3]) // -4
```
#### L.scan : ('a -> 'b -> 'a) -> 'a -> 'b list -> 'a list
The first element in (arg2) is the initial state of the accumulator

(arg1) is iteratively passed the previous accumulator and the next element in (arg2)

Returns the list of all accumulators
```javascript
F.scan (F['+']) (9) ([1, 2, 3]) // [9, 10, 12, 15]
F.scan (F['-']) (9) ([1, 2, 3]) // [9, 8, 6, 3]
```
#### L.map : ('a -> 'b) -> 'a list -> 'b list
Returns (arg2) with each element transformed by passing that element to (arg1)
```javascript
F.map (F['+'] (3)) ([1, 2, 3]) // [4, 5, 6]
```
#### L.mapi : (int -> 'a -> 'b) -> 'a list -> 'b list
Same as L.map, except additionally passes the index as well
```javascript
F.mapi (F['+']) ([1, 2, 3]) // [1, 3, 5]
```
#### L.find : ('a -> bool) -> 'a list -> 'a
If an element exists in (arg2) for which (arg1) returns true

Then return that element

Else return undefined
```javascript
L.find (F['='] (3)) ([1, 2, 3]) // 3
L.find (F['='] (9)) ([1, 2, 3]) // undefined
```
#### L.pick : ('a -> unit/'b) -> 'a list -> unit/'b
If an element exists in (arg2) for which (arg1) does not return undefined

Then return that element passed to (arg1)

Else return undefined
```javascript
L.pick (h => h % 3 != 0 ? undefined : h + 3) ([1, 2, 3]) // 6
L.pick (h => h % 3 != 0 ? undefined : h + 3) ([1, 2]) // undefined
```
#### L.filter : ('a -> bool) -> 'a list -> 'a list
Returns (arg2) without the elements for which (arg1) returns false
```javascript
L.filter (F['='] (3)) ([1, 2, 3]) // [3]
```
#### L.for_all : ('a -> bool) -> 'a list -> bool
Returns if (arg1) returns true for all elements (arg2), vacuously true
```javascript
L.for_all (F['='] (3)) ([3, 3, 3]) // true
L.for_all (F['='] (3)) ([1, 2, 3]) // false
L.for_all (F['='] (3)) ([]) // true
```
#### L.exists : ('a -> bool) -> 'a list -> bool
Returns if (arg1) returns true for at least one element in (arg2), vacuously false
```javascript
L.exists (F['='] (3)) ([3, 3, 3]) // true
L.exists (F['='] (3)) ([1, 2, 9]) // false
L.exists (F['='] (3)) ([]) // false
```
#### L.contains : 'a -> 'a list -> bool
Returns if (arg1) exists in (arg2)
```javascript
L.contains (3) ([3, 3, 3]) // true
L.contains (3) ([]) // false
```
#### L.sort : ('a -> 'a -> int) -> 'a list -> 'a list
Returns (arg2) sorted by (arg1) determined by normal comparator standards
```javascript
L.sort (F['-']) ([1, 6, 2, 7, 3, 8, 4, 9, 5, 0]) // [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```
#### L.partition : ('a -> bool) -> 'a list -> ('a list * 'a list)
Returns the list containing the elements of (arg2) for which (arg1) returned true and the list containing all other elements
```javascript
L.partition (F['='] (3)) ([1, 2, 3, 4, 5]) // [[3], [1, 2, 4, 5]]
```
#### L.clone: 'a list -> 'a list
Returns a shallow copy of the list
```javascript
L.clone ([1, 2, 3, 4, 5]) // [1, 2, 3, 4, 5]
```
#### L.uniq : 'a list -> 'a list
Returns the list with duplicates removed
```javascript
L.uniq ([1, 2, 3, 3, 3, 4, 5]) // [1, 2, 3, 4, 5]
```
#### L.unzip : ('a * 'b) list -> 'a list * 'b list
Returns the list of the first element of each element of the list and the list of the second element of each element of the list

(note: this function does not enforce density)
```javascript
L.unzip ([[1, 2], [3, 4], [5, 6]]) // [[1, 3, 5], [2, 4, 6]]
```
### 2 list functions
#### L.append : 'a list -> 'a list -> 'a list
Returns (arg1) pre-pended to (arg2)
```javascript
L.append ([1, 2, 3]) ([4, 5, 6])
```
#### L.uneq_length : 'a list -> 'b list -> bool
Returns if (arg1) and (arg2) have unequal length
```javascript
L.uneq_length ([1, 2, 3]) ([4, 5, 6]) // false
L.uneq_length ([1, 2, 3]) ([4, 5, 6, 7]) // true
```
#### L.iter2 : ('a -> 'b -> unit) -> 'a list -> 'b list -> unit
If (arg2) and (arg3) have unequal lengths

Then throws exception F.e

Else same as L.iter, except additionally passing the element of (arg3)
```javascript
L.iter2 (h1 => h2 => F.log (`${h1}, ${h2}`)) ([1, 2]) ([3, 4]) // prints '1, 3' then '2, 4'
```
#### L.iteri2 : (int -> 'a -> 'b -> unit) -> 'a list -> 'b list -> unit
If (arg2) and (arg3) have unequal lengths

Then throws exception F.e

Else same as L.iter2, except additionally passing the index
```javascript
L.iteri2 (i => h1 => h2 => F.log (`${i}: ${h1}, ${h2}`)) ([1, 2]) ([3, 4]) // prints '0: 1, 3' then '1: 2, 4'
```
#### L.fold2 : ('a -> 'b -> 'c -> 'a) -> 'a -> 'b list -> 'c list -> 'a
If (arg2) and (arg3) have unequal lengths

Then throws exception F.e

Else same as L.fold, except additionally passing the element of (arg3)
```javascript
L.fold (a => h1 => h2 => a + h1 + h2) (1) ([1, 2]) ([3, 4]) // 11
```
#### L.mapi2 : (int -> 'a -> 'b -> 'c) -> 'a list -> 'b list -> 'c list
If (arg2) and (arg3) have unequal lengths

Then throws exception F.e

Else same as L.mapi, except additionally passing the element of (arg3)
```javascript
L.mapi2 (i => h1 => h2 => i + h1 + h2) ([1, 2]) ([3, 4]) // [4, 7]
```
#### L.map2 : ('a -> 'b -> 'c) -> 'a list -> 'b list -> 'c list
If (arg2) and (arg3) have unequal lengths

Then throws exception F.e

Else same as L.map, except additionally passing the element of (arg3)
```javascript
L.map2 (F['+']) ([1, 2]) ([3, 4]) // [4, 6]
```
#### L.for_all2 : ('a -> 'b -> bool) -> 'a list -> 'b list -> bool
If (arg2) and (arg3) have unequal lengths

Then throws exception F.e

Else same as L.for_all, except additionally passing the element of (arg3)
```javascript
L.for_all2 (F['=']) ([3, 3]) ([3, 3]) // true
L.for_all2 (F['=']) ([1, 3]) ([3, 3]) // false
L.for_all2 (F['=']) ([]) ([]) // true
```
#### L.exists2 : ('a -> 'b -> bool) -> 'a list -> 'b list -> bool
If (arg2) and (arg3) have unequal lengths

Then throws exception F.e

Else same as L.exists, except additionally passing the element of (arg3)
```javascript
L.exists2 (F['=']) ([3, 3]) ([3, 3]) // true
L.exists2 (F['=']) ([1, 3]) ([3, 3]) // true
L.exists2 (F['=']) ([]) ([]) // false
```
#### L.zip : 'a list -> 'b list -> ('a * 'b) list
If (arg2) and (arg3) have unequal lengths

Then throws exception F.e

Else returns the list with each element the list with the element of (arg1) first and the element of (arg2) second
```javascript
L.zip ([1, 2]) ([3, 4]) // [[1, 3], [2, 4]]
```
## D (dictionary functions)
#### D.is_empty : 'a, 'b dictionary -> bool
Returns if (arg1) is empty
```javascript
D.is_empty ({}) // true
D.is_empty ({c: 'Hint: 3?'}) // false
```
#### D.get : 'a -> 'a, 'b dictionary -> 'b
Returns the value in (arg2) with key (arg1)
```javascript
D.get ('c') ({c: 'Hint: 3?'}) // 'Hint: 3?'
```
#### D.create : ('a * 'b) list -> 'a, 'b dictionary
Returns the dictionary with pairs of each element with the first element as the key and the second element as the value
```javascript
D.create ([['Hint', '3?'], ['Not_a_Hint', 'You\'re already dead']]) // {Hint: '3?', Not_a_Hint: 'You\'re already dead'}
```
#### D.keys : 'a, 'b dictionary -> 'a list
Returns a list of the keys of (arg1)
```javascript
D.keys ({
    Hint: '3?',
    Not_a_Hint: 'You\'re already dead',
}) // ['Hint', 'Not_a_Hint']
```
#### D.vals : 'a, 'b dictionary -> 'b list
Returns a list of the values of (arg1)
```javascript
D.keys ({
    Hint: '3?',
    Not_a_Hint: 'You\'re already dead',
}) // ['3?', 'You\'re already dead']
```
#### D.pairs : 'a, 'b dictionary -> ('a * 'b) list
Returns a list of the key, value pairs of (arg1)
```javascript
D.keys ({
    Hint: '3?',
    Not_a_Hint: 'You\'re already dead',
}) // [['Hint', '3?'], ['Not_a_Hint', 'You\'re already dead']]
```
#### D.bind : 'a, 'b dictionary -> 'a, 'b dictionary
Binds the self-references for functions in the map to the dictionary and returns the dictionary

(note: this one of the gaps I mentioned in the opening; in languages that automatically resolve this problem, it's not an issue)
```javascript
var d = {
    f: function () {return this.a},
    a: 3,
}
d.f () // 3
var f = d.f
f () // undefined
m = D.bind (d)
var f = d.f
f () // 3
var n = {
    f: d.f,
    a: 9,
}
d.f () // 3
```
#### D.freeze : 'a, 'b dictionary -> 'a, 'b dictionary
Freezes the dictionary and returns the dictionary
```javascript
var d = D.freeze ({
    a: 1,
})
d.a = 2
F.log (d.a) // prints 1
```
#### D.freeze_bind : 'a, 'b dictionary -> 'a, 'b dictionary
Same as calling D.bind and then D.freeze on the dictionary

#### D.iter : ('a -> unit) -> 'b, 'a dictionary -> unit
Same as L.iteri on the values of (arg2), except with keys instead of indices
```javascript
D.iter (F.log) ({
    Hint: '3?',
    Not_a_Hint: 'You\'re already dead',
}) // prints '3' then 'You\'re already dead'
```
#### D.iterk : ('a -> 'b -> unit) -> 'a, 'b dictionary -> unit
Same as D.iter, except additionally passing the key
```javascript
D.iterk (k => v => F.log (`${k}: ${v}`)) ({
    Hint: '3?',
    Not_a_Hint: 'You\'re already dead',
}) // prints 'Hint: 3?' then 'Not_a_Hint: You\'re already dead'
```
#### D.fold : ('a -> 'b -> 'a) -> 'a -> ('c, 'b) list -> 'a
Same as L.fold on the values of (arg2)
```javascript
D.fold (F['+']) (1) ({
    a: 1,
    b: 2,
    c: 3,
}) // 7
```
#### D.map : ('a -> 'b) -> 'c, 'a dictionary -> 'c, 'b dictionary
Same as L.map on the values of (arg2)
```javascript
D.map (F['+'] (3)) ({
    a: 1,
    b: 2,
    c: 3,
}) // {a: 4, b: 5, c: 6}
```
#### D.mapk : ('a -> 'b -> 'c) -> 'a, 'b dictionary -> 'a, 'c dictionary
Same as D.map, except additionally passing the key
```javascript
D.mapk (k => v => `${k}: ${v}`) ({
    a: 1,
    b: 2,
    c: 3,
}) // {a: 'a: 1', b: 'b: 2', c: 'c: 3'}
```
#### D.find : ('a -> bool) -> 'b, 'a dictionary -> 'a
Same as L.find on the values of (arg2)
```javascript
D.find (F['='] ('3?')) ({
    Hint: '3?',
    Not_a_Hint: 'You\'re already dead',
}) // '3?'
```
#### D.filter : ('a -> bool) -> 'a list -> 'a list
Same as L.filter on the values of (arg2)
```javascript
D.filter (F['='] ('3?')) ({
    Hint: '3?',
    Not_a_Hint: 'You\'re already dead',
}) // {Hint: '3?'}
```
#### D.filterk : ('a -> 'b -> bool) -> 'a list -> 'a list
Same as D.filter, except additionally passed the key
```javascript
D.filterk (k => v => `${k}: ${v}` == 'Hint: 3?') ({
    Hint: '3?',
    Not_a_Hint: 'You\'re already dead',
}) // {Hint: '3?'}
```
#### D.for_all : ('a -> bool) -> 'b, 'a dictionary -> bool
Same as L.for_all on the values of (arg2)
```javascript
D.for_all (F['='] ('3?')) ({
    Hint: '3?',
    Not_a_Hint: 'You\'re already dead',
}) // false
```
#### D.exists : ('a -> bool) -> 'b, 'a dictionary -> bool
Same as L.exists on the values of (arg2)
```javascript
D.exists (F['='] ('3?')) ({
    Hint: '3?',
    Not_a_Hint: 'You\'re already dead',
}) // true
```
#### D.contains : 'a -> 'b, 'a dictionary -> bool
Same as L.contains on the values of (arg2)
```javascript
D.exists ('3?') ({
    Hint: '3?',
    Not_a_Hint: 'You\'re already dead',
}) // true
```
#### D.length : 'a, 'b dictionary -> int
Returns the number of pairs in (arg1)
```javascript
D.length ({
    Hint: '3?',
    Not_a_Hint: 'You\'re already dead',
}) // 2
```
#### D.partition : ('a -> bool) -> 'b, 'a dictionary -> (('b, 'a) dictionary * ('b, 'a) dictionary)
Same as L.partition on the values of (arg2)
```javascript
D.partition (F['='] ('3?')) ({
    Hint: '3?',
    Not_a_Hint: 'You\'re already dead',
}) // [{Hint: '3?'}, {Not_a_Hint: 'You\'re already dead'}]
```
#### D.extend : 'a, 'b dictionary -> 'a, 'b dictionary -> 'a, 'b dictionary
Returns (arg1) overlaid by (arg2)
```javascript
D.extend ({
    Hint: '3?',
    a: '3',
}) ({
    Not_a_Hint: 'You\'re already dead',
    a: '4',
}) // {Hint: '3?', Not_a_Hint: 'You\'re already dead', a: '4'}
```
#### D.delete : 'a, 'b dictionary -> 'a list -> 'a, 'b dictionary
Returns (arg1) without the pairs without the keys in (arg2)
```javascript
D.delete ({
    Hint: '3?',
    Not_a_Hint: 'You\'re already dead',
}) (['Not_a_Hint']) // {Hint: '3?'}
```
## S (string functions)
#### S.length : string -> int
Returns the length of (arg1)
```javascript
S.length ('Hint: 3?') // 8
```
#### S.get : int -> string -> string
Returns the character at (arg1) in (arg2)
```javascript
S.get (6) ('Hint: 3?') // 3
```
#### S.substr : int -> int -> string -> string
Returns the substring from (arg1) to (arg2) of (arg3) with some slice logic
```javascript
S.substr (6) (7) ('Hint: 3?') // 3
```
#### S.index : string -> string -> int
Returns the first index at which (arg1) appears in (arg2)
```javascript
S.index ('a') ('Not_a_Hint: You\'re already dead') // 4
```
#### S.contains : string -> string -> bool
Returns if (arg1) appears in (arg2)
```javascript
S.contains ('3') ('Hint: 3?') // true
```
#### S.compare : string -> string -> int
Follows normal comparator rules for strings for comparing (arg1) to (arg2)
```javascript
S.compare ('Hint: 3?') ('Not_a_Hint: You\'re already dead') // -1
```
#### S.match : regex -> string -> string list
If (arg2) is matched by (arg1)

Then return a list of the match followed by captured groups

Else return null
```javascript
S.match (/(.)?$/) ('Hint: 3?') // ['3?', '3']
```
#### S.replace : regex -> string -> string -> string
Returns (arg3) with matches of (arg1) replaced by (arg2)
```javascript
S.replace (/(\d)\?$/) ('4')  ('Hint: 3?') // 'Hint: 4'
```
#### S.rindex : string -> string -> int
Same as S.index, except with the last occurance
```javascript
S.rindex ('3') ('Not_a_Hint: You\'re already dead') // 29
```
#### S.search : regex -> string -> int
Returns the first index that (arg1) matches in (arg2)
```javascript
S.search (/^/) ('Not_a_Hint: You\'re already dead') // 0
```
#### S.split : regex -> string -> string list
Returns a list of the substrings of (arg2) split by (arg1)
```javascript
S.split (/:/) ('Hint: 3?') // ['Hint', ' 3?']
```
#### S.lower : string -> string
Returns (arg1) with all characters lowercase
```javascript
S.lower ('Hint: 3?') // 'hint: 3?'
```
#### S.upper : string -> string
Returns (arg1) with all characters uppercase
```javascript
S.upper ('Hint: 3?') // 'HINT: 3?'
```
#### S.trim : string -> string
Returns (arg1) without surrounding whitespace
```javascript
S.trim (' Hint: 3? ') // 'Hint: 3?'
```
