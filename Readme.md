#Angular2 Simplified (Sorta)

So, this is sort of a short demonstration for my own reference and anyone who's probably wondering how to Angular2 but ain't quite there yet.
There are a lot of moving parts to Angular2 and things can get a bit messy, so this guide will have
a lot of code snippets on how to get stuff on and working. There will also be theory , but not too much. 

##First things first
Yo, gotta make sure you got NodeJS and NPM installed on your machine.
If you want easy-peasy and not have to go through creating your own `package.json` and `npm install` everything, Angular2 CLI offers easy commands that scaffold 
stuff for you so you can start adding your custom code and test applications in no time.

To install [angular2 cli](https://github.com/angular/angular-cli) :: `npm install -g angular-cli`
Create a new app :: `ng new app-name`
Get inside your app :: `cd app-name` 
Start your app :: `ng serve`

You can find all possible blueprints in the table below:

Scaffold  | Usage
---       | ---
Component | `ng g component my-new-component`
Directive | `ng g directive my-new-directive`
Pipe      | `ng g pipe my-new-pipe`
Service   | `ng g service my-new-service`
Class     | `ng g class my-new-class`
Interface | `ng g interface my-new-interface`
Enum      | `ng g enum my-new-enum`
Module    | `ng g module my-module`

Note, when you start your app, it is hosted on `localhost:4200`. Everytime you create and save stuff, it automatically gets update.
Note, that what you are writing in is Typescript and not Javascript. It's a superset. Learn it. Yeah.

##Directives
A directive is how we add dynamic behaviour to HTML. 
Kinds of directives : Component , Sturctural and Attribute.

##Component
It's a directive with a template. How do you make a Component?
using cli :: `ng g component my-new-component`

or custome file::

`component-name.ts`

First, put a bunch of requires on top, cuz you're going to be needing these things.
```
import { NgModule, Component } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
```
Then, open up a decorator, where you will define stuff.
The decorator adds more behaviour to our calls from outside the class. It must be declated immediately before the class.
```
@Component({
			selector : 'my-app' //this is what you're going to use as tags in your html.
			template : '<div> Some stuff , yo  {{printThis}}</div> //this is where your html stuff goes in. I generally like to have my own separate html file for this.
			templateUrl: 'my-app.component.html' //where all my html behavior resides
			styles : [`.customstyle { someproperty: somevalue;}] //custom styles but, better to have a separate file
			styleUrls: ['custom.css']
			})	
export class ComponentName {
			//define variables, behaviour and other stuff here
			printThis = "THIS WILL BE PRINTED IN THE DIV UP THERE"
}

```

You'll also need to declare the ComponentName so you're going to have to have a NgModule to do that.

```
@NgModule({
  imports: [ BrowserModule ],
  declarations: [ ComponentName ],
  bootstrap :[ ComponentName ] //this makes sure your component is rendered.
  
})
```

This is a basic template of how stuff should looke like.

##Defining custom data and pass it to our templates.

If our decorator looks like this :: 
```
@Component({
  selector: 'racing-app',
  template: `
    <h1>{{ heading }}</h1>
    <h2>{{ race.name }}</h2>
    <p>{{ race.date }}</p>
    <p>{{ race.about }}</p>
  `
})
```
then the class should look like this ::
```
class AppComponent {
  heading = "Ultra Racing Schedule"
  race = {
    "id": 1,
    "name": "Daytona Thunderdome",
    "date": new Date('2512-01-04T14:00:00'),
    "about": "Race through the ruins of an ancient Florida battle arena.",
    "entryFee": 3200
  }
}
```
So the data is instantly rendered. 

## Structural Directive : '
`ngFor` ::This is used to loop through a array of data in our HTML template that is originally served by our component class.
`ngIf`  ::This is used to evaluate conditionals and make changes accordingly.

### Using `ngFor` :: 

modify template as such :: 
```
 <li  *ngFor="let race of races">
      <h2>{{race.name}}</h2>
      <p>{{race.date}}</p>
      <p>{{race.about}}</p>
    </li>
```

where the `races` looks something like :
`races = [ { data 1.1,data2.1,data3.1}, {data 2.1,data2.2,data2.3}]`

### Using `ngIf` ::
Template now looks like 
```
<button *ngIf="(race.isRacing)===false"  >Enter Race</button>
<h3 *ngIf="(race.isRacing)===true" >Already Racing</h3>	
``` 
considering that `races` has a property :: `races = [ {1st..., isRacing=true}, {2nd...., isRacing=true}]`

##Pipes
A Pipe takes in some data and outputs out the data in a format that we desire.
For example, if you want something displayed in $ and not have an ugly looking 'USD' text, you can do the following :
In our template 
`<h2>{{race.name}} {{race.entryFee | currency:'USD':true }}</h2>` the `currency` is a predefined pipe. The `:true` will show the `$` sign.
`<p>{{race.date | date:'MMM d, y, h:mm a' }}</p>` the `date` pipe helps get the formate out in Month name, day value, time value.
check the Angular2 API reference for more pipes.


##Methods with define behaviour
If you want to have your custom method deciding some stuff, then you gotta

`template : '<h2> Total cost of it all :: {{ totalCost() }}</h2>'`
where 
```
totalCost()
  {
    let sum = 0;
    for (let race of this.races) {
    if (race.isRacing) {sum += race.entryFee;} //if isRacing is true, calculate sum of all racing entryFee. 
  }
  return sum;
  }

 ```
======================================================================================
## Separation of files
So, you really cant stuff everything into one file. Use `main.ts` for bootstrapping and declaration, and make separate files for Components.
For example, if you want to create a new component, make it in a file called `your-new-component.component.ts`
dont forget to create a new `selector: somename` and `export class NewComponent { }`. Now, in `main.ts`,
do : `import { NewComponent } from './your-new-component.component`
also , need to add it to our declarations : ` @NgModule( { declarations: [NewComponent] }) `


## Using Models 
You can separate out data models for use in different components.

`model-name.ts`
```
export class modelName {
	data1: type1;
	data2: type2;
	      .
		  .
		  .
	dataN : typeN;
}
```
in our `componentfile.ts` , we `import { modelName } from {./model-name}`.
using the data in our component will be essentially : 
```
export class ComponentFile {
	dataSet : modelName[]; //this is how we are using this stuff.
}
```
now, if we want to mock some data out , we create a `mocks.ts` file. Again, remember to `import { MOCKDATA } from './mocks.ts' ` in `componentfile.ts`
our `mocks.ts` looks like this :: 

```
import { ModelName} from './model-name'

	export const MOCKDATA: modelName[] = [	
	{
		data1: value1;
		data2: value2;
	     	 .
		   	 .
		 	 .
		dataN : valueN
	},
	{
		data1: value1;
		data2: value2;
	     	 .
		   	 .
		 	 .
		dataN : valueN
	}, .. etc
	];
```

in our ComponentFile , we create a `ngOnInit()` function. This is invoked after the component is constructed and is the best place to initialize property values.
```
ngOnInit() {
	this.dataSet = MOCKDATA;
}
```
##Property Binding [JS to HTML]
Property Binding allows us to bind component properties to any DOM element properties.
Any update to the component property value will update the DOM propertym but not vice versa - that's why it's "one-way binding".

`<html-tag [property] = " object or loopingvariable <.> key_defined_in_mock_or_class " >`

##Class Binding [JS to HTML]
`<li class="card" *ngFor="let race of races" [class.racing]="race.isRacing"  >`

##Event Binding [HTML to JS]
1.Define a method to be called from HTML event. for eg. `myMethod(someparam)`
2.Put the behaviour you want in that method. Let the method exist in the `export class yourComponent() { myMethod(param){ } }`
3.To capture an event from our template, we wrap the name of the event we want to listen to in parenthesis and specify the method to call.
  `<div class="someclass" (event) = "myMethod(parameter)">`

##Two Way Binding
The `[(ngModel)]` syntax allows us to specify a component property that will use two-way binding.
Two-way binding means that if the component property is modified inside the component(JS) or inside our web page (HTML), it will stay in sync.
`<html-tag class="somestuff" [(ngModel)] = "property_to_bind" > </html-tag>`

##Services
So, you wan't to change data, but with Mocking, you can't change everything everywhere and it's hard to switch between mock and real data.
Services are used to organize and share code across your app, and they're usually where we create our data access methods.

You gotta create `your-service.service.ts` and then `import { DATASET } from './mocks' ` within the file.
Currently, our service is just returning the mocked data so 
```
export YourService {
	getData(){
		return DATASET;
	}
}
```
so now in your main component : `componentfile.ts` add
`import {YourService} from './your-service.service.ts'`

When you run an Angular2 application, it creates a dependency injector. An injector is in charge of knowning how to create and send things.
The injecttor creates a service , we have it resend to same service to which ever component is asking for it. Now, how does an injector know what it can inject?
We gotta tell the injector what it can inject by registering "providers" with it.

1.Add the injectable decorator to YourService.
`import { Injectable } from '@angular/core'`;

```
@Injectable()
export class YourService {
	getData(){
		return DATASET;
	}
}
```

2. Let our injector know about our service by naming it as a provider.
This happens in your `main.ts`
`import { YourService } from './your-service.service.ts'`
```
@NgModule({
	//other thingys
	providers: [ YourService ]
})
```
So now all subcomponents can ask for (inject) our `YourService` when they need it, and an instance of `YourService` will
either be delivered if it exists, or it will be created and delivered.


3. Inject the dependency into our ` myCompotent.component.ts `

`import { YourService } from './your-service.service.ts'`
```
@Component ({ ... })
export class yourComponent {
	myData : MyData [];


constructor(private yourService_variable : YourService ) { }
//This is Typescript syntax for setting the type of the parameter. This is what identifies that YourService should be injected into this component.

ngOnInit{
		this.myData : this.YourService.getData();
	}
}
```
The app is now Scalable because our dependencies aren't strongly tied to our classes.
The app is now Testable because it'd be easy to mock services when we test the component.

=================================================================

## Get data from the Internet

`myCompotent.component.ts` asks for data.
`my-data.service.ts` fetches the appropriate data from the internet.
`mydata.json` holds the data that comes from the internet. It could also be a `php` render. 

##Story of an Angular App
When a user visits our web page, the Angular app loads first in our browser, and then it fetches the 
needed data.
```
Browser requests -> Server
HTML/JS/CSS Response  <- Server
Angular Loads on Browser
App(browser) -> data request via API
JSON data    <- Server (web api)
```

## Set up HTTP Library
1. Create a JSON file with data.
`my-data.json`
```
{
	'data' : 
	[
		{
			"data1" : val1,
			"data2" : "val2",
			"data3" : val3
					.
					.
					.
			"dataN" : valN 
		},
		{ .... },
		{ .... }
	]
}
```

2. Ensure our app includes the libraries it needs to do `Http` calls.
The HTTP library provides the `get` call we will use to call across the internet.
The `RxJS` library stands for Reactive Extensions and provides some advance tooling for our `http` calls.

3. Tell our injector about the `http` provider.
In `main.ts` add`import {HttpModule} from '@angular/http';`
and within our NgModule :: `@NgModule ({ imports : [HttpModule ]})`
4. Inject the http into our service and make `http get` request. 
In our `YourService.ts` we add
```
import {Http} from '@angular/http';
import 'rxjs/add/operator/map';

export class YourService{
	constructor (private http: Http){ } //Injecting HTTP as a dependency

	getData() {
		return this.http.get('app/my-data.json').map(response => <MyDatap[]>response.json().data);
		//You might expect to get to return a promise but this returns an observable. Observables just give
		//us additional functionality on our http calls- one of which is to return an array.
	}
}
```
5. Listen for data returned by this request.
So now, in our `myCompotent.ts` , in the `ngOnInit` we subscribe as a listener.
```
ngOnInit(){
	this.YourService.getData().subscribe(myData=> this.myData = myData);
}
```
Make sure that this.myData is an array.

=============================================
#So, there you have it. This is the guide. I hope you find it useful.


