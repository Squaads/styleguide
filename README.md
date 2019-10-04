# Squaads Bible 📖

This is the Squaads Styleguide, we will include in this all the arrangements we make as a team in order to improve code legibility, efficiency and scaling. 

This are all intentions we try to follow in order to code on a better way. Here are all best practices we all agree with and feel comfortable following them when coding. 

At Squaads we believe that every developer has its own way to code and at some situations you will be forced to solve a problem on a certain "non-sexy" way. Therefore, this guide is more related to the structure of the code rather than the code itself. 

We are also open to debate all the statemets below, however know that if you like non-functional programming (ask us for the definition) you are 🔥🔥🔥FIRED🔥🔥🔥. 

- [1. Mom or Dad Code Decisions](#1-code-stuff)
    - [Async/Await vs Then](##async/await-vs-then)
    - [Constructor vs LifeCycle Methods](##constructor-vs-lifecycle-methods)
    - [Interfaces vs Classes](##interfaces-vs-classes)
    - [Promises vs Observables](##promises-vs-observables)
    - [Services vs Providers](##services-vs-providers)
    - [Reactive Forms vs NgModel](##reactive-forms-vs-ngmodel)
- [2. Mimimi Code Stuff](#2-mimimi-code-stuff)
    - [Naming](##naming)
    - [One function per function](##one-function-per-function)
    - [Typing](##typing)
    - [Naming Styles](##camelcase-and-kebab-case)
    - [Navigation Params](##navigation-params)
    - [Optimizing CSS](##optimizing-css)
- [3. Code Organisation](#3-code-organisation)
    - [File Naming](##file-naming)
    - [File Single Responsability](##file-single-responsability)
    - [One View, One Page](##one-view-one-page)


# 1. Mom or Dad Code Decisions

Here we will state our choice between two options that are widely used by the community. They were made after intense fights and several briberies but if you feel you have something to share or discuss, submit your opinion and it will be ignored as soon as possible **LOL**

## Async/Await vs Then

In squaads the only pyramids we like are the ones in Giza.

~~~ ts
❌🤢
promise().then() {
    // do stuff
    anotherPromise().then() {
        // do stuff
        thirdPromise().then() {
            // do more stuff
            oneMorePromise().then() {
                // idk why so many promises, 
                // but it could happen and this is 
                // clearly a 🔺
            }
        }.catch()
    }.catch()
}.catch()
~~~

As you can see, when you need to use several promises in a row and you cant do them all at the same time with a `Promise.all()`, you will end up with a pyramid. Instead of that, you can use async/await and have a much more sexy code.

~~~ ts
✔😍
const result = promise().catch();
// stuff
const anotherResult = anotherPromise().catch();
// more stuff
const thirdResult = thirdPromise().catch();
// do more stuff
const oneMoreResult = oneMorePromise().catch();
~~~

## Constructor vs LifeCycle Methods

When initialising values on an Ionic Page, we rather locate it on the [Life Cycle Hooks](https://ionicframework.com/docs/angular/lifecycle) rather than on the page constructor. 

The reason for this decision is because the Life Cycle Events are called every time a page is shown, so we can assure the data needed for it to work correctly. We can also control at which point of the Page Load will any variable be loaded or any method called. 

~~~ ts
❌🤢
constructor(private someModule: ModuleModel) {
    this.pageVar = 'somevalue';
}

ngOnInit() {
    // other stuff
}
~~~

~~~ ts
✔😍
constructor(private someModule: ModuleModel) {  }

ngOnInit() {
    this.pageVar = 'somevalue';
}
~~~

## Interfaces vs Classes

Using models on Typescript is very useful for typing an entity of your project. If you only need to type an entity, stablishing its properties, an Interface is the solution. 

However, if you'd like to create a dummy entity and change the values of it afterwards, it is very useful to have a dummy constructor. That's why we rather use classes for model definitions instead of Interfaces. No other methods will be declared at that class.

~~~ ts
❌🤢
export interface Model {
    prop1: string;
    prop2: int;
    prop3: bool;
    prop4: AnotherModel[]; 
}
~~~

~~~ ts
✔😍
export interface Model {
    prop1: string;
    prop2: int;
    prop3: bool;
    prop4: AnotherModel[]; 

    constructor() {
        prop1 = "";
        prop2 = 0;
        prop3 = undefined;
        prop4 = []
    }
}
~~~

Obviously all properties will be checked if there have been correctly filled before the model is stored or used.

## Ternary Operators vs If/Else

We have some team members that are loyal devotes to the ternary operators. However we have to stop them some times when the ternary is complex either because it has too many conditions or because there are many operations done afterwards. 

~~~ ts
❌🤢
let i = 0
if (array.find(arrayValue => arrayValue === value)) {
    i = 10;
} else {
    i = -10
}

const i = (anotherVar != condition() && array.filter(value => condition(value))) 
    ? getAnObject().parseToArray().map(value => changeValue()).otherFunction()
    : getOtherObject().parseToArray().map(value => changeValue()).otherFunction()
~~~

~~~ ts
✔😍
if (anotherVar != condition() && array.filter(value => condition(value))) {
    const arrayOfValues = getAnObject().parseToArray();
    const otherArray = arrayOfValues().map(value => changeValue())
    const finalArray = otherArray.otherFunction()
} else {
    const anotherArrayOfValues = getAnObject().parseToArray();
    const anotherOtherArray = arrayOfValues().map(value => changeValue())
    const anotherFinalArray = otherArray.otherFunction()
}

const i = (array.find(arrayValue => arrayValue === value)) ? 10 : -10
~~~

Therefore:
- if/else => complex situations
- ternary operators => simple operations / assignments

## Promises vs Observables

When creating a function that returns an Observable, you can instead return a Promise using rxjs operators. We are against of it. The Squaad team finds more useful functions to return an observable always and let the element that uses that function decide whether it needs an Observable or a Promise.

~~~ ts
❌🤢
function accessToDB() : Promise<DBElement> {
    return getElementFromDB().pipe(take(1)).toPromise();
} 
~~~

~~~ ts
✔😍
// provider.ts
function accessToDB() : Promise<DBElement> {
    return getElementFromDB().pipe(take(1)).toPromise();
} 

// controller.ts
async function getElementData() {
    const element = await Provider.accessToDB().pipe(take(1)).toPromise(); 
}
~~~

## Services vs Providers

The mother of all discussions. We know what @joshmorony said, providers and services are the same thing. They actually have never coexisted BUT in squaads we agreed (after several days debatig) that for single responsability and modular programming reasons we agreed to use them **BOTH**. 

Providers will be the ones that communicate with the Database, interacting with entities and db methods. In other words, providers will serve as a type of interface of the db.

~~~ ts
❌🤢
// model.provider.ts
function getModelFromDB() : Promise<DBElement> {
    return db.get('model')
} 
~~~

On the other hand, services can actually manipulate those entities or do any other functions the controllers require.

~~~ ts
✔😍
// model.service.ts
async function getModelDetails() : ModelDetails {
    const model : Model = await provider.getModelFromDB().pipe(take(1)).toPromise()
    return model.details
} 

// controller.ts
async function getElementData() {
    const element = await Provider.accessToDB().pipe(take(1)).toPromise(); 
}
~~~

## Reactive Forms vs NgModel

|| Reactive Forms|ngModel|
| ------------- |:-------------:| :-----:|
| Secure input | ✔ |❌|
| Better Code | ✔ |❌|
| Quick to code | ❌ |✔|

After this detailed Pro&Cons list you will understand our point of view. Reactive Forms are cool and all that but you dont need one for a single input. We let the dev decide whether it is needed to invest time on a Reactive Form or not, but we always recommend to choose the Reactive Form if you need any kind of **validation** or the data will be **stored**.   

# 2. Mimimi Code Stuff

Here we will explain some code details we want to mantain common in all projects. There could be some insignificant details but there are very important to us in order to mantain certain similarity between all projects.

## Naming

We are tired of Log In or Sign In so we just chose to call it **Log in** always.

~~~ ts
❌🤢
function signIn() {}
~~~

~~~ ts
✔😍
function logIn() {}
~~~

## One function per function

Have you heard about the principle of single responsibility? Then you would know that its not sexy to write ands in function names. Please, make a function for just one thing. ❤

~~~ ts
❌🤢
function doSomethingAndAnother() {}
~~~

~~~ ts
✔😍
function doSomething() {}
function doAnother() {}
~~~

## Typing

what-the-f***-is-this? Its a question we dont want to ever repeat when trying to find out what a function is returning. So please, please PLEASE. Avoid using any and type everything. Each variable has the right to know what is it, just that...nothing more. Actually, no, you are 🔥🔥🔥FIRED🔥🔥🔥

~~~ ts
❌🤢
function doSomething(var : any) {
    const something = somethingElse(var);
    return anotherThing; 
}
~~~

~~~ ts
✔😍
function doSomething(var : number) : string {
    const something : Model = somethingElse(var);
    return anotherThing; 
}
~~~

## camelCase and kebab-case

We don't negotiate with terrorist. Please, for var naming use camelCase and for keys use kebab-case. All in English pleaaase 🇬🇧. Dont use spaces, special characters. Also try to name correctly everything !! 🤗🙏

~~~ ts
❌🤢
const myobject_special = {
    "PropertyName": onevar,
    "propiedad2": Othervar,
    "ID": a,
}
~~~

~~~ ts
✔😍
const myObject = {
    "property-name": objName,
    "property-location": objLocation,
    "id": newID,
}

// there is no need to state property on all cases, I just didn't know what to write 
~~~

## Navigation Params

Here we have a total dilemma. When passing params from one page to another, should we use queryParams or services? Should we pass a reference to the object or the object itself?

After hours, we finally decided that its much sexy to pass the reference to the object through queryParams even though it means that the app will access the DB. The reason to choose this is because that access to the database won't cost that much and the code will be easy to read and secure.

Despite the fact that it is said that using a service is a good practice, we find it confising and that it can lead to unexpected errors. Then, our only choice are queryParams and passing an entire stringified object through them its not sexy at all.

## Optimizing CSS

- For image display use `<div> + background-image` since it makes the layout customization much more faster and easier
- Please, use CSS variables for the layout main colors 

# 3. Code Organisation

## File Naming

Some of us like to navigate through files without GUI, so please, try to state what the file contains in its name:


~~~ ts
❌🤢
- app
    - pages
        - user.ts
    - models
        - user.ts
    - providers
        - user.ts
    - services
        - user.ts
~~~

~~~ ts
✔😍
- app
    - pages
        - user.page.ts
    - models
        - user.model.ts
    - providers
        - user.provider.ts
    - services
        - user.service.ts
~~~

## File Single-Responsability

As it is said in all introductory courses:

- HTML  =   structure🦴
- CSS   =   layout 😄  
- JS    =   control 🧠

Therefore, even though in angular its really easy to include functionality on the HTML through the (mehtod/property binding) or the *ngConditionals. We force ourselves to avoid that and keep everything in the place it belongs, so we encourage you to do the same. If we achieve that, we will develop a much more clean and scaling code where every item does what it is meant to do. 

Sorry, we dont like Frankensteins 👹

> PD: this also includes placing `RouterLinks` and any navigation items on the .ts file

## One View, One Page

We love to write reusable code in order to minimize the time spent on developing something and maximize the quality of our software, making it scalable and easy to read. Yet we think that abusing of something without thinking first is a bad practice. We have realised that trying to bring two similar views into one page takes too much time to develop and makes the code awful 🤢🤢.

So, just at this particular case, we encourage to copy-paste code and divide the views into two separate pages. It will be much more clear and will make us develop much faster 🚀









