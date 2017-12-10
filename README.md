# angular-expression-parser
This library helps in achieving AngularJs equivalents of `$parse`, `$eval` and `$watch` in Angular.


## How to use
Download `expression-parser.ts` and `services.ts` in your Angular application.

Register the `PipeProvider` declared in `services.ts` with the Application module.

## $parse
In your component file implement the below changes

1. add the below import statements
```ts
import { $parse } from "path_to_expression-parser";
import { PipeProvider } from "path_to_services";
```
2. Add the PipeProvider dependency
```ts
constructor(private pipeProvider: PipeProvider) {}
```
Arguments to `$parse` function:
1. Expression to be parsed
2. PipeProvider

```let fn = $parse('(((firstName | uppercase) + lastName) | lowercase) | slice:start:end | uppercase', this.pipeProvider);```

This returns a function which can later be excuted with a context.

## $eval
The function returned with `$parse` accepts two arguments
1. context
2. overrides

example:
```ts
class AppComponent implements OnInit{

  constructor(public pipeProvider: PipeProvider) {}
  
  ngOnInit() {
    this.first = 'Hi';
    this.last = "World";
    let fn = $parse('first + " " + last | uppercase', this.pipeProvider);
    console.log(fn(this)); // logs HI WORLD
    console.log(fn(this, {first: 'hello'})); // logs HELLO WORLD
  }
}
```

## $watch
To achieve `$watch` like functionality, we need to excute the function returned by `$parse` in `ngDoCheck` life cycle method.

example:
```ts
class AppComponent implements OnInit, DoCheck{
  
  constructor(public pipeProvider: PipeProvider) {}
  
  ngOnInit() {
    this.first = 'Hi';
    this.last = "World";
    this.watchers = [];
    let fn = $parse('first + " " + last | uppercase', this.pipeProvider);
    this.watchers.push([fn]);
    this.watchers.push([fn, {first: 'hello'}]);
  }
  
  ngDoCheck() { // evaluates the parsed functions on each change detection cycle
    for (const [fn, locals] of this.watchers) {
      console.log(fn(this, locals)); 
    }
  }
}
```
