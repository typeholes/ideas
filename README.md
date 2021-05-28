# ideas

This repo is a space to try out ideas for a programming language with the central theme of tracking undefined symbols in the type system and use type inference to make it manageable. 
I believe this will enable some functionality that is not available (or conveniently available) existing languages. 
Ideally every feature in the language will just by syntactic sugar around this one idea. 
Syntax sugar will of course be used for ergonomics, but it will also be used to clarify semantics.
For example, function arguments can be implemented as holes and are common enough that we want to know the hole is meant to be an argument without drilling into the code

I'll be making up syntax as I go, so lets start with a simple declaration.

	x : Int
	x = 5

This reads:  
x is an Int	
x equals 5

	add : Int @ x : Int, y : Int 
	add = 
		Hole x : Int
		Hole y : Int
		x + y

This reads:
Add is an Int with holes x which is and Int and y which is and Int
we could sugar this to look like a Haskell function definition

	add : Int -> Int -> Int
	add x y = x + y

We could call this by filling the holes

	add#x = 4
	add#y = 5
	add

or by a more normal function application syntax sugar

	add(x,y)

or by a haskell function application sugar

	add x y

Maybe it would be possible to let the reader pick their sugarring so I could write in haskell like syntax without making it harder for read for those more familiar with a c based syntax.  

* TODO add some more examples of implementing common language features as hole sugarring

Okay functions and arguments aren't much of a selling point.
Lets solve a big pain point in Haskell.
Haskell has an incredible feature where you can tell that a function is pure by its type. i.e I can tell that foo : Int -> Int does ask for user input or modify any files just from the type. This is great until you need to add logging to the rocks function and have a call stack of pure functions like:

	foo(bar(baz(zing(zang(myriad(rocks True))))))

So rocks need to change from type 

	Bool -> String

to type

	Bool -> IO String

Now I have to change myriad because it takes a String, not and IO String, and so on  up the stack. That sucks.  I don't want to have to maintain pure and impure versions of every function. More to the point - I don't want a version of foo that is impure when just because it might call an impure function.

With type tracked holes I can keep the type of foo pure without caring if it is called with an Int, a pure function returning and Int, or an impure function returning and Int. Let's imagin a REPL with a command ? which returns an expressions type

	? foo
	foo : String -> Int

	? foo(1)
	Int

	? foo(pureFunction(x))
	Int

	? loggedFunction
	Int -> Int @ log : Logger

	? foo(loggedFunction(1)
	Int @ log : Logger

* TODO more examples of novel features

An outstanding question is how to handle name collision and usage collision with type tracked holes. A simple example issue:  

	? add( loggedFunction(1), loggedFunction(1) )

Should the holes of the loggedFunction calls be joined to make:

	Int @ log : Logger

or should they remain distinct:
	Int @ log : Logger, log : Logger

The second option obviously suffers from the dificulty of distinguishing which hole you are filling. The first is simple but there are certainly times where you would want to provide different loggers. 

* TODO more examples of path dependencies and conflicts

> ideally it would be easy to say "all of these holes must be filled with the same value" and "these are distinct holes that can have different values"

* TODO discussion of interfaces vs typeclasses.  

> Assuming we meet the above goal, type tracked holes could be used to provide the coherence benefits of typeclasses without required newTypes and orphan instance problems.




