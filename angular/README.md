# Angular 19

## Sgnals

Las señales envuelven un valor que notifica a los clientes cuando un valor irá cambiando, estas pueden tener cualquier valor desde datos primitivos hasta datos complejos.

**Ejemplo de declaración de una señal**

```
const count = signal(0)
```

### Wiretable signals

Las writable signal proveen al API para actualizar sus valores directamente a través de los siguiente métodos.

```
.set() // Cambia el valor de la señal directamente
.update() // Cambia el valor de la señal desde un valor que ya existía
```

### Computed signals

Las señales computadas solamente son de lectura que derivan del valor de otras señales.

**Declaración de una señal computada**

```
const count: WritableSignal<number> = signal(0);
const doubleCount: Singla<number> = computed(() => count() * 2);
```

La señal computada *doubleCount,* no se ejecuta para calcular su valor hasta la primera vez que se lee doubleCount. El valor calculado se almacenará en el caché, en caso de leer doubleCount de nuevo, se devolverá el valor almacenado en caché sin volver a calcularlo.

Cabe recalcar que las señales computadas no podrán asignar valores ya que, el tipado de estas no son de WritableSignals

```
const showCount = signal(false);
const count = signal(0);
const conditionalCount = computed(() => {
  if (showCount()) {
    return `The count is ${count()}.`;
  } else {
    return 'Nothing to see here!';
  }
});
```

En este ejemplo cuando leemos conditionalCount, si showCount es falso se mostrará el mensaje *"Nothing to see here"* sin leer la señal count. Lo cual se traduce que si después actualizamos el valor de count no se volverá a ejecutar la señal computada de *conditionalCount.*

En caso que actualicemos *showCount* a true, y después se lee *conditionalCount* otra vez esto se volverá a ejecutar y tomará el camino donde *showCount()* sea verdadero cambiando el valor de la señal computada e invalidando el valor guardado en el caché

## Effects

Los efectos son operaciones que corren cuando una o más señales cambian de valor. Normalmente estas corren al menos una vez cuando esto sucede, se "persigue" el valor de las señales ya leídas.

```
effect(() => {
  console.log(`The current count is: ${count()}`);
});
```

## Resources (Experimental)

La mayoría de señales son asíncronas -signal, computed, input, etc. Los resources nos ofrecen una forma de incorporar datos asíncronos dentro de la aplicación.

Tu puedes usar un Resource para realizar cualquier tipo de operación asíncrona, la mayoría de casos de uso se ocupa para hacer un fetch a la data desde un servidor

```
import {resource, Signal} from '@angular/core';
const userId: Signal<string> = getUserId();
const userResource = resource({
  // Define la reactividad de un request computado
  // El valor del request computado se re calcula siempre y cuando las señales cambien
  request: () => ({id: userId()}),
  // Define un loader asíncrono que recibirá la data
  // El recurso llama esta función cada vez que el valor del request cambie
  loader: ({request}) => fetchUser(request),
});
// Crea un señal computada basada en el resultado del resource loader
const firstName = computed(() => userResource.value().firstName);
```

**Propiedad request**

La propiedad request define la reactividad del computado lo que produce un valor de la petición. Cuando la señal cambien, el resource produce un nuevo valor.

**Propiedad loader.**

Función asíncrona que permite un solo paramétro, y devuelve un valor.

## Components

### Anatomía de los componentes

```
//Decorador Component que contendrá los metadatos, lo que incluirá el selector, template, y styles)
@Component({
  selector: 'profile-photo', //Como se llama nuestro componente, al momento de llamarlo en un template
  template: `<img src="profile-photo.jpg" alt="Your profile photo">`, // Template o Ruta del template
  styles: `img { border-radius: 50%; }`, //CSS o ruta del CSS
})
export class ProfilePhoto { }
```

Cabe recalcar que a partir de la versión 19 de angular, los componentes son stand alone, lo que significa que podemos directamente agregar el array de componentes externos.

```
import {ProfilePhoto} from './profile-photo';
@Component({
  imports: [ProfilePhoto],
})
export class UserProfile { }
```

### Inputs

Cuando usamos un componente, normalmente queremos pasar datos al mismo. Para eso ocupamos los inputs, estos nos ayudarán a recibir datos desde el template. Se declara de la siguiente forma

```
import {Component, input} from '@angular/core';
@Component({/*...*/})
export class CustomSlider {
  value = input(0);
// En caso que querramos que el input sea requerido
  value = input.required()
}
```

Es así como mandamos datos desde el template:

```
<custom-slider [value]="50" />
```

### Lifeciycle

Los componentes tienen un ciclo de vida, siguiendo una secuencia de pasos que ocurren desde la creación del componentes hasta su destrucción.

> Nota: Para implementar los lifecycle hooks debemos implementar su interfaz. Esto es de manera opcional

**Creación**

Se inicializa el componente.

`ngInit()`

**Detección de cambios**

Se ejecuta una vez cuando se ha inicializado el componente

`ngOnInit()`

Se ejecuta cada vez que los inputs de los componentes han cambiado

`ngOnChanges()`

Se ejecuta cada vez que el componente ha checado los cambios

`ngDoCheck()`

Se ejecuta solamente una vez después que el componente ha sido inicializado

`ngAfterContentInit()`

Se ejecuta una vez que el contenido del componente ha sido checado o verificado por cambios

`ngAfterContentChecked()`

Se ejecuta una vez, después que la vista del componente ha sido inicializada

`ngAfterViewInit()`

Se ejecuta cada vez que la vista del componente ha sido checado para cambios

`ngAfterViewChecked()`

**Renderizado**

Se ejecuta una vez que todos los componentes han sido renderizados en el DOM.

`afterNextRender()`

Se ejecuta cada vez que todos los componentes han sido renderizados en el DOM

`afterRender()`

**Destrucción**

Se ejecuta una vez, antes que se destruya el componente.

`ngOnDestroy()`

## Templates

Cada componente tiene un **template** que define el DOM que el componente se renderiza dentro de la página. Los componentes son basados en HTML, con features adicionales como:

* Built-in template functions
* Data binding,
* Event listening
* Variables
* Etc

### Event listeners

Hay diferentes events listeners en un elemento en el template, especificando el nombre del evento dentro parentesis junto a una sentencia que se ejecuta cada vez que se produce el evento.

**Ejemplo:**

```
<input type="text" (keyup)="updateField()" />
```

### Two-way biding

Las two-way binding es una forma abreviada de vincular simultáneamente un valor a un elemento, a la vez que se da a ese elemento la capacidad de propagar los cambios a través de esta vinculación. Es comúnmente usada para mantener los datos del componente en sincronización de un form control como el un usuario interactúa con el control. Su sintaxis se define con la combinación de brackets y paréntesis [()].

```
<input type="text" [(ngModel)]="firstName" />
```

### Control flow

**@if, @else-if, @else**

@if es un bloque condicional que muestra el contenido cuando su expresión es verdadera

@else-if si quiere anidar otra condición

@else en caso que ninguna condición se cumpla

```
@if (a > b) {
  {{a}} is greater than {{b}}
} @else if (b > a) {
  {{a}} is less than {{b}}
} @else {
  {{a}} is equal to {{b}}
}
```

**@for**

@for es un ciclo que pasa a través de una colección y repetidamente renderiza el contenido del bloque.

```
@for (item of items; track item.id) {
  {{ item.name }}
}
```

**¿Por qué se debe utilizar track en las iteraciones @for?**

Las expresiones con track permiten mantener una relación entre tus datos y el DOM en la página gracias a esto optimizas el perfomance ejecutando el mínimo necesario.

> Para colecciones estáticas se recomienda utilizar $index

```
@for (item of items; track item.id; let idx = $index, e = $even) {
  <p>Item #{{ idx }}: {{ item.name }}</p>
}
```

**Fallback con @empty**

En caso que tu colección se encuentre vacía puedes aplicar un fallback después del bloque for, cuando esto suceda se mostrará lo que le indiques dentro del fallback, debería ser un empty state

> Fallback: comportamientos por defecto

**@switch**

El bloque switch es una alternativa con una sintaxis diferente para renderizar datos.

```
@switch (userPermissions) {
  @case ('admin') {
    <app-admin-dashboard />
  }
  @case ('reviewer') {
    <app-reviewer-dashboard />
  }
  @case ('editor') {
    <app-editor-dashboard />
  }
  @default {
    <app-viewer-dashboard />
  }
}
```

### Pipes

Los pipes son operadores especiales dentro de los templates que permiten transformar los datos de forma declarativa. Solo modifican los datos de forma **"temporal" ,**

**¿Cómo funcionan los pipes?**

**Lista de pipes**

* AsyncPipe
  * Lee un valor de promise o RxJS
* CurrencyPipe
  * Transforma un número a un cambio de moneda en string.
* DatePipe
  * Formatea el valor de la fecha acordado en la reglas locales
* DecimalPipe
  * Transforma un número en un string con un punto decimal, formateado con base a las reglas locales
* I18nPluralPipe
  * Mapea un valor pluralizando el mismo acorde a las reglas locales
* JsonPipe
  * Transforma un objeto a un string usando JSON.stringify
* KeyValuePipe
  * Transforma un objeto en un arreglo de clave-valor
* LowerCasePipe
  * Transforma el texto a lowercase
* PercentPipe
  * Transforma un número en porcentaje
* SlicePipe
  * Crea un nuevo arreglo de elementos
* TitleCasePipe
  * Transforma el texto a TitleCase
* UpperCasePipe
  * Transforma el texto a upperCase

**¿Cómo usar los pipes?**

El operador pipe se usa con el siguiente símbolo "|", dentro de nuestro template.

`<p>Total: {{ amount | currency }}</p>`

**Combina multiples pipes en la misma expresión**

```
<p>The event will occur on {{ scheduledOn | date | uppercase }}.</p>
```

**Pasar parámetros para los pipes**

Para pasar parametros a un pipe debemos agregar dos puntos al final del mismo.

```
<p>The event will occur at {{ scheduledOn | date:'hh:mm' }}.</p>
```

**Con dos valores**

```
<p>The event will occur at {{ scheduledOn | date:'hh:mm':'UTC' }}.</p>
```

#### Custom pipes

Para definir un pipe debemos:

* Implementar la interfaz PipeTransform por ende un metodo transform
* Un nombre que se especificará en el decorador

```
// kebab-case.pipe.ts
import { Pipe, PipeTransform } from '@angular/core';
@Pipe({
  name: 'kebabCase',
})
export class KebabCasePipe implements PipeTransform {
  transform(value: string): string {
    return value.toLowerCase().replace(/ /g, '-');
  }
}
```

## Dependency Injection

Para inyectar una dependencias (o servicios) dentro de un componente, debes instanciar el objeto del servicio.

```
import { inject } from "@angular/core";
export class HeroListComponent {
  private heroService = inject(HeroService);
}
```

## Routing

Para definiar las rutas en un componente se hace de la siguiente manera:

```
const routes: Routes = [
  { path: 'first-component', component: FirstComponent },
  { path: 'second-component', component: SecondComponent },
];
```

La primera propiedad se define el path donde se va acceder al componente y la segunda corresponde al componete que se usará

**Agrega las rutas a la apliación**

Para asignar las rutas dentro de una plantilla debemos agregar el atributo **routerLink.**

```
<h1>Angular Router App</h1>
<nav>
  <ul>
    <li><a routerLink="/first-component" routerLinkActive="active" ariaCurrentWhenActive="page">First Component</a></li>
    <li><a routerLink="/second-component" routerLinkActive="active" ariaCurrentWhenActive="page">Second Component</a></li>
  </ul>
</nav>
<!-- The routed views render in the <router-outlet>-->
<router-outlet /> 
 
```

En caso que ninguna ruta coincida, debes aplicar el operador "**" para dirgir a una página por defecto. Útil para NotFoundPages

```
const routes: Routes = [
  { path: 'first-component', component: FirstComponent },
  { path: 'second-component', component: SecondComponent },
  { path: '**', component: PageNotFoundComponent },  // Wildcard route for a 404 page
];
```

**Rutas anidadas**

Se ocupa la misma estructura pero se agrega la propiedad **children.**

```

const routes: Routes = [
  {
    path: 'first-component',
    component: FirstComponent,
    children: [
      {
        path: 'child-a', // child route path
        component: ChildAComponent, // child route component that the router renders
      },
      {
        path: 'child-b',
        component: ChildBComponent, // another child route component that the router renders
      },
    ],
  },
];
```

**Lazyloading**

Angular solo cargará los modulos que se le indique. Mientras que la carga de componentes normal se realiza desde el inicio de la aplicación.

```
const routes: Routes = [
  {
    path: 'lazy',
    loadComponent: () => import('./lazy.component').then(c => c.LazyComponent)
  }
];
```

**Guards**


### Router outlet

### Router links

## Forms

### Reactive forms

### Strictly typed reactive forms

### Validate form input

## Http Client

### Making requests

### Intercepting requests and responses

## Dev tools

### Angular CLI
