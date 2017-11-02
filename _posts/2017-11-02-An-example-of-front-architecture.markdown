---
layout: single
title:  "An example of Front End Architecture after ES6"
date:   2017-11-02 12:00:00 +0100
categories: Front Architecture React
permalink: an-example-of-front-architecture
header:
  overlay_image: https://www.javascript.com/images/meta/facebook.png
---

From the down of time to 2005, no one would have talked about architecture in frontend development. Nowadays, a modern frontend application can amount to thousands of lines of code. So what has changed? The epoch changes, and with it the end user's needs. We now have to care about multiple devices, off-line or client-side applications.

When the frontend was just a hundred lines long, it was useless to architect it, but now we have real complexity, different concerns and a need to organize all of that to simplify evolution and testability. My personal point of view is that we need the following organization:

![simple_architecture](/assets/images/simple_architecture.png)

Services abstract requesting. Models describe the shape of the data. Validations check the inputs of the system, while tests verify the behavior of all the application. The trick here is in the packages GlobalStores, GlobalStyles and Views which are the heart of the frontend: Data and Rendering.

Testing will be addressed in it’s own article, we are going to explain what is inside each of these packages and why. Finally, We will talk about Dependency Injection and see how it ties everything together.

## GlobalStores

A store contains a part of the application state, with actions to change this state and reactions of the state to actions. The components will listen to changes in the store and refresh themselves when a change is detected. Stores let us separate the data from the view, like MVC but without a true Controller (as the components handle that role). This separation can make our component stateless, which are pure functions.

This particular store is called global as it will be used application-wide. It does not prevent us from splitting it into smaller pieces to fit our model. In an online pokedex we could have one part for all the pokémons :

{% highlight JavaScript %}
import pokemonStore from 'GlobalStore/PokemonStore'

pokemonStore.savePokemon(newPokemon)

{% endhighlight %}

This gives us clarity. We know we are doing an action, which data we are changing, and also that we are using a global store and not a local one, which is specific to a component.
We have talked about how to store datas, now we need to see how we get them.

## Services

Each service represents a kind of data, for example, a PokemonService may offer us access to Pokemon's data. The way a service requests data and where the data come from is implementation details. But the asynchronous nature of the request needs to be represented. Promises are the simplest solution for this.

{% highlight JavaScript %}
onClickLoadPokemonHandler() {
  pokemonService.loadPokemon().map(pokemonStore.savePokemon)
}
{% endhighlight %}

We can split or arrange our services to fit our model or any other business view. This way we also simplify future modifications and add readability to our code base.

## Models

The model is the format our data will fit in. In plain JavaScript, this directory is all about JsDoc. This is fine but not great. In typed languages like Typescript or Flow, this directory is more important as it will contain type declarations and maybe some utils on these types.

## Views

The Views package is split into subpackages, each representing a screen of the application. Each screen is a composition of others components. These components will themselves be a composition of others when it makes sense.

A good practice is to add a business meaning to each component, simplifying maintenance and evolutions.

### Local Stores

Next to each component, there may be a local store. This store should only be used in this very same component. It will hold the data of the component and will be created when the component is [mounted](https://reactjs.org/docs/react-component.html#componentwillmount). After the component is [unmounted](https://reactjs.org/docs/react-component.html#componentwillunmount), the store will be destroyed.

### Local Styles

Also next to each component there is a style file. This file holds the specific style for the component, which should not be used outside this same component. To ensure this we use a top class

{% highlight CSS %}
.mon-composant
  .une-classe-specifique
    ... 
  .une-autre-classe-specifique
    ...
{% endhighlight %}

And we mirror this hierarchy into the render of our component, and that's it. Now, this style is isolated from the others components. Be sure to make very specific classes when possible.

## Validations

This is the directory holding most of the business rules. A validation is a set of functions, typically one by model. Each function will test a value against a rule, returning a value or an error. We can then compose error messages, handle them in a unique place and take the right action without risk. Most of the time, that place would be the event handler of a component.

We extract the validations from the component to avoid rule duplication, letting us modify this rule easily in the future. This unicity also let us unit test each rule easily.

Now that we have seen how I have split all the responsibilities into different packages we will see how to reassemble them back together.

## Dependency injection (DI)

DI is about initializing all our sections at the same place and take all our dependencies as parameters. Only Services, Stores, Validations and Views can have dependencies. But the Views are special: we do not want to unit test them and can hardcode the dependencies using import statement.
{% highlight JavaScript %}
import { createPokemonStore } from 'GlobalStore/PokemonStore'
import { createPokemonService } from 'Services/PokemonService'
import { createPokemonValidation }  from 'Validations/PokemonValidation'
import TopComponent from ‘Views’
const pokemonStore = createPokemonStore()
const pokemonService = createPokemonService()
const pokemonValidation = createPokemonValidation(pokemonService)

ReactDOM.render(<TopComponent />)
{% endhighlight %}
Our root package will do all the initializations, the view being last to make sure everything is ready before displaying any data. With this construct we can also avoid circular dependencies as the initialization order cannot turn around.

## Conclusion

In reaction to the grow of the codebase in frontend application I have showed that we need to organize it. My personal advice is to split things up into models, services, stores, validations and views.

![complete_architecture](/assets/images/complete_architecture.png)

I have explained the responsibilities of each package : The Global Stores contains application-wide data. Services let us request data to the outside of the application. Models describe the shape of our data. Views contains all the rendering logic, which is also the handling of event, some local data and local style. Validations test values against business rules. To used all this together I have recommended a DI approach that avoid circular references and allow each package to be tested separately.

This construct may seems complexe at first but it help us test each part of our application and will let us evolve more simply when new features will be add to our code base. Speaking of which, in an upcoming article, I will give more details about testing. It will cover different ways to test each of this packages depending on your choices and needs.

Until next time!

 