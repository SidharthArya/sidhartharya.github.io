# Angular


Angular is an application design framework and development platform for creating efficient and sophisticated single-page apps. Uses [Typescript]({{< relref "20211027112629-typescript.md" >}}).


## Features {#features}

-   Validation
-   Routing
-   Unit Testing
-   Typescript


## Components {#components}

-   @angular/core
-   @angular/compiler
-   @angular/http
-   @angular/router


## Concepts {#concepts}

-   Modules  - RootModule (AppModule)
    -   Component - RootComponent (AppComponent) - HTML + Class
        -   Template - View - HTML
        -   Class - Code - TypeScript
        -   Metadata - Information - Decorator
-   Services - Business Logic


## Interpolation {#interpolation}

Interpolation is a technique that allows the user to bind a value to a UI element.


## Attributes vs Properties {#attributes-vs-properties}

-   Attributes and properties are not the same
    Attributes - HTML
    Properties - DOM


## Property Bindings {#property-bindings}

```html
<input [id]="myId" type="text" value="Temp">
```

Binds myId variable to id property

[class.text-danger]="true"

ngClass


## Style Binding {#style-binding}

[style.color]="orange"


## Binding {#binding}


### Data Binding {#data-binding}

[]


### Event Binding {#event-binding}

()


### Two way Binding {#two-way-binding}

[(NgModel)]=var


## Template Reference {#template-reference}

\#var


## Structural Directives {#structural-directives}

-   \*ngIf - then, else
-   [ngSwitch] - \*ngSwitchCase
-   \*ngFor - even, odd, first, last

&lt;ng-template&gt;


## Component Interaction {#component-interaction}

-   @Input - Parent to Child
-   @Output - Child to Parent - Use EventEmitter


## Pipes {#pipes}

-   lowercase
-   uppercase
-   titlecase
-   slice
-   json
-   number:'min number of integers.minimum no of decimal digits:maximum no of decimal digits'
-   percent
-   currency
-   date


## Dependency Injection {#dependency-injection}


## Angular Material {#e3e217be-a0ad-40cb-bf30-520da2c8f5c6}

[Material Design]({{< relref "20211101142807-material_design.md" >}}) for Angular
