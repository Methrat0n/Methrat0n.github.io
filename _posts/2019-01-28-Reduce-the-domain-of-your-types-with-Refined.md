# Reduce the domain of your types with Refined

Reduce the domain of your types with Refined
Some days ago one of my coworker ask me about [Refined](https://github.com/fthomas/refined). He said he cannot find an explanation about what refined does. After some thinking I came back with this : `Refined` let you reduce the domain of your Types.
## Refined
But first, what's `Refined` ? `Refined` is a Scala library. More precisely it's a port of an `Haskell` library to `Scala`. Even if it came from `Haskell`, it's not complicated to use. It is complicated under the hood but that's out of the scope of this article. Let's just say Refined let you write this :

<script src="https://gist.github.com/Methrat0n/29306e960e22c9033dc7524582de802a.js"></script>

It's pretty self-explaining but let's review some of it. The first line define a new type by combaining three existing one. The base type is an `Int` and the predicate type is `Positive`. It's this predicate that define the constraint we add to the `Int` type.
As per this example, we see `Refined` is about constraining existing types to define new ones. In other words `Refined` reduce the value a type can take, making him more specific.
A typical use case would be to ensure the boundaries of an id. Let's say we integrate into an existing system and we need an id beginning after 400.000. We can validate the input data but we can't enforce the information in the system. We have no way to make sure the 400.000 rule hasn't been broken somewhere else.
To avoid this kind of error we could define :

<script src="https://gist.github.com/Methrat0n/ed736c8cbd5e5fb6646b3b78cfec8f95.js"></script>


Using this type where we would have used `Int` for our `Id` has two advantages : we ensure the 400.000 rule and we clearly state that this value is an Id.
## Domain Reduction
By defining the `Id` type, base on the `Int` type we have selected a subset of `Int`.
Before our refinement, the type possible values were `Int.MinValue` to `Int.MaxValue`. After the refinement our new type possible values are `400.000` to `Int.MaxValue`, which is literally a subset of the first interval.
In this sens, Refined is a way to define types which are subset of others. And it allow to do this by simple Type Level Programming.
## Domain Reduction in Scala (And other Object Oriented Languages)
Domain reduction is more used in Object Oriented programming than you think.
Our Id type could have been define like this :

<script src="https://gist.github.com/Methrat0n/fa5781f1ad184edb39e1dfb4923f99a9.js"></script>


By limiting the valide value in `apply` we reduce the domain of our attribute, making the `Id` type a subset of `Int`. More generally, when one type inherit from another it define a specific case of a more general one.

<script src="https://gist.github.com/Methrat0n/de74876c49aa39fffb87e1426d9a5500.js"></script>

In the example, the `Dog` type is one of the two value the `Animal` type can take. In this sens, inheritance is about Domain Reduction.
## Conclusion
We have seen that `Refined` allow use to create new types by constraining other types. This is what I call Domain Reduction but it's not limited to `Refined`. Creating a class to limit the value of an unique attribute is about the same, as is inheritance. Refined simply let us enforce our business rules into ou types without all the boilerplate of defining classes and unsafe validations for each.