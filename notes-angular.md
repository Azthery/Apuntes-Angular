# Apuntes de Angular


## **string interpolation**:
_{{SI}}_ {{ string }}
dentro de los string responde como .js
______________________________________________________________________

## **Agregar variables**:
para agregar variables y usarlas como {{SI}}
devemos de ir al archivo .ts (_app.components.ts_)
______________________________________________________________________

## **Data Binding**:
para agregar actualizacion de datos en tiempo real hacemos uso de lo siguiente:
```html
<div><input [(ngModel)]="nombre">
A que tu nombre es: {{nombre}}</div>
```
la variable _nombre_ se actualiza en tiempo real, sin embargo de esta forma solo nos data el valor default:
```html
<div><input [ngModel]="apellido">
Tu apellido es 🤔 {{apellido}}</div>
```
tener en cuenta que en el archivo _app.module.ts_ debemos de integrar lo siguiente:
```ts
import { FormsModule } from '@angular/forms'
imports: [
    FormsModule
]
```
______________________________________________________________________

## **nglf** condicionante:
nos permite generar un condicionante en el html con esta syntax:
```html
<div *ngIf="condicionante == 'password'">
    contenido
  </div>
```
______________________________________________________________________
## **ngSwitch**:
es igual al switch case de js
```html
<div [ngSwitch]="condicionante">
    <p *ngSwitchCase="'juan'"> este es juan</p>
    <p *ngSwitchCase="'gabriel'"> este es gabriel</p>
    <p *ngSwitchCase="'nicolai'"> este es nicolai </p>
  </div>
```
______________________________________________________________________
## **ngFor** iteraciones:
es generar un ciclo en una etiqueta html, example:
```ts
items = ['Charles', 'Tonkano', 'Bocchi', 'Momamone'];
```
```html
<ul>
  <li *ngFor="let index of items; let i = index">
    {{ i }} {{ index }}
  </li>
</ul>
```
_i_     : es el numero de iteracion
_index_ : es el objecto correspondiente a la iteracion

______________________________________________________________________

## **Funcion en un boton**:
para generar una funcion al presionar un boton debemos
```html
<button (click)="addItem()">Add item</button>
```
```ts
addItem(){
    -code-
}
```
donde `(click)` es el evento "click", y "addItem()" es la funcion que se ejecutara

______________________________________________________________________

## **interface** con _product.model.ts_:
para typar nuestos objetos con typescript debemos de crear un nuevo archivo llamado _product.model.ts_, en el donde crearemos el interfacel, este luego debemos exportarlo a nuestro _app.component.ts_:

product.model.ts:
```ts
export interface Product {
    id: string;
    image: string;
    title: string;
    price: number;
    description: string;
}
```
app.component.ts:
```ts
import { Product } from './product.model';
```
______________________________________________________________________

## Componentes y decoradores:
Componentes son una forma de modular la aplicacion, para esto en nuestra carpeta _./src/app_ (de nuestra aplicacion en angular), crearemos una nueva carpeta llamada _components_, en la cual dentro de ella crearemos dos nuevos archivos, los cuales en este caso llamaremos _product.component.html_ y _product.component.ts_.
Los _decorator_ (o decorator) define el roll del _component_

Dentro del archivo _product.component.ts_ agregaremos:
```ts
import { Component } from '@angular/core'     //es el decorator que define el roll de component
import { Product } from '../product.model'
@Component({                                  //componentes tienen metadata
    selector: 'app-product',                  //sera la etiquera que podremos usar en el html
    templateUrl: './product.component.html'   //sera nuestro archivo HTML que estara enlazado a este componente
})
export class ProductComponent {
    product: Product;
}
```

Tambien agregamos en nuestro _app.module.ts_
```ts
import {} from './components/product.component'

@NgModule({
  declarations: [
    ProductComponent  //<------- esta linea de codigo agremos en @NgModule({})
  ]
})
```

Ahora podemos ir a nuestro _product.component.html_ y agregar lo que necesitemos, por ejemplo:
```html
<h3>Hola soy componente bien cute</h3>
```

Finalmente volveremos a nuestro html padre (_app.component.html_) y haremos uso de nuestro nuevo componente como si fuera una etiqueta mas de html, notar que el nombre de esta etiqueta sera el que le dimos en el _selector_ (esto en _product.component.ts_), ejemplo:
```html
<app-product></app-product>
```

______________________________________________________________________

## **Inputs y Outputs**:
Estos son  propiedades las cuales nos permite, mandar y recibir datos de un componente, a los cuales en **angular** se le conoce como ***property binding*** y ***event binding***.

- **Input**: Para comunicar el componente padre (_app.component.html_) a un componente hijo.
debemos **set**ear la data en el componente hijo (_product.component_), agregandole el **"Input"**:
```ts
import { Component, Input } from '@angular/core'  //<--- agregamos el Input
```
en el ejemplo de la clase debemos de asignar que nuestro objeto `"product"` sera del tipo `"@Input"`, ejemplo completo:
```ts
import { Component, Input } from '@angular/core'
import { Product } from '../product.model'
@Component({
    selector: 'app-product',
    templateUrl: './product.component.html'
})
export class ProductComponent {
    @Input() product: Product;    //<---- asignamos el tipo @Input
}
```
Haciendo esto, nuestro componente lo podremos ver en nuestro componente padre (_app.component.html_), ejemplo:
```html
<div *ngFor="let product of products">
  <app-product [product]="product"></app-product>
</div>
```

- **Output**: Para comunicar a un componente hijo al componente Padre, en nuestro caso haremos que en nuestro componente hijo tenga un boton, el cual comunicara un dato al componente padre.
En nuestro componente hijo (_product.component.html_), agregaremos el boton:
```html
<button (click)="addCart()">Agregar al carrito</button>
```
en _product.component.ts_ agregamos:
```ts
import { Component, Input, Output, EventEmitter } from '@angular/core'  //<--- importamos el Output y EventEmitter

export class ProductComponent {
    @Output() productClicked: EventEmitter <any>;   //usamos <> para typar el EventEmitter
    addCart(){
        console.log('añadir al carrito');
        this.productClicked.emit(this.product.id)  //Del  typado en <> del EventEmitter, dependera el typado del parametro
    }
}
```
Para "rescatar" el componente emitter en el componente padre debemos de, poner un Output (en _app.component.html_), ejemplo:
```html
<!-- nuestro output es lo que esta entre paratensis: (productClicked)="" -->
<app-product (productClicked)="" [product]="product"></app-product>
```
Este output tiene debe de tener un metodo que leera en nuestro componente padre, en el caso enterior este sera  `="clickProduct($event)"` 
El cual crearemos en nuestro archivo _app.component.ts_:
```ts
clickProduct(id: number){
  console.log(`id product: ${id}`);
}
```
Y ahora si podemos agregar nuestro metodo en _app.component.html_:
```html
<app-product (productClicked)="clickProduct($event)" [product]="product"></app-product>
```

Finalmente debemos inicializar el output en _product.component.ts_, de la siguente manera:
```ts
@Output() productClicked: EventEmitter <any> = new EventEmitter();
```
______________________________________________________________________

## **Ciclo de vida de los componentes**:

>1. ### **constructor**:
> Utilizado crear y darle podopiedades a nuestro componente, para luego ponerlo en nuestro interfaz.
>```ts
>constructor() { }
>```

>2. ### **ngOnChanges**:
> Se ejecuta al detectar un cambio cada vez se envia data atraves de un imput, con la posibilidad de ver el estado anterior y el nuevo estado.
>```ts
>import { OnChanges } from '@angular/core';
>export class ClassName implements OnChanges {
>   ngOnChanges(changes: SimpleChange) {
>       console.log('2. ngOnChanges', changes)
>   }
>}
>```

>3. ### **ngOnInit**:
> Se ejecuta solo una vez, cuando el componente esta listo en la interfaz grafica.
>```ts
>export class ClassName implements OnInit {
>   ngOnInit() {
>       console.log('3. InInit');
>   }
>}
>```

>4. ### **ngDoCheck**:
> Detecta cuando los componentes hijos de este componente son creados y puesto en el interfaz.
>```ts
>import { OnChanges } from '@angular/core';
>export class ClassName implements OnChanges {
>   ngOnChanges() {}
>}
>```

>5. ### **ngOnDestroy**: 
> Detecta cuando el elemento es quitado (destroy) de la interfaz.
>```ts
>import { OnDestroy } from '@angular/core';
>export class ClassName implements OnDestroy {
>   ngOnDestroy() {
>  console.log('OnDestroy');
>   }
>}
>```

______________________________________________________________________

## Estilos (SCSS):

Para cada uno de nuestro **componentes** (incruyendo a nuestro componente padre _```app.component.ts```_), tenemos la opcion agregar una hoja de estilos (esta en formato .sccs), para ubicar esta hoja de estilo debemos de fijarnos en:
>ejemplo de _```app.component.ts```_:
>```ts
>@Component({
>  selector: 'app-root',
>  templateUrl: './app.component.html',
>  styleUrls: ['./app.component.scss']     //<--- aqui van ubicado los scss
>})
>```
Estos al ir en un array, pueden se varios, sin embargo es **buena practica** dejar solo uno.
El caso que vemos en el ejemplo corresponde a nuestra hoja de estilo de caracter global, es decir esta afectara a todas nuestra app.
A diferencia si fuera un componente hijo, su hoja de estilo solo afectaria a su propio componente.

>Trabajando en un componente hijo crearemos nuestra propia etiqueta, la cual haremos uso en nuestro componente padre, sin embargo si en >nuestra hoja de estilo de nuestro componente queremos referirnos a nuestra propia etiqueta, debemos de hacer uso de **:host**
>```scss
>:host { }
>```

>- ### **auto-fill**:
>Algo particular que debemos saber sobre **display: grid;**, es el uso de la propiedad **auto-fill**.
>Esta nos permite _adaptar_ nuestro estilo la pantalla de forma mas dinamica, ejemplo
>```scss
>.product-grid{
>    display: grid;
>    grid-template-columns: repeat(4, 1fr));                        //<--- sin auto-fill
>    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));  //<--- con autofill
>}
>```
>en nuestro ejemplo sin auto-fill le decimos que repita la columna 4 veces, sin importar la resolución de la pantalla, en cambio en nuestro ejemplo con auto-fill, debemos indicar con ```minmax(numPixeles px, fracciónes fr)```.
>- **numPixeles**: cada numero de pixeles que agregara cantidad de **fracciónes**.
>- **fracciónes**: numero de framento que se agregaran cada vez que la resolucion aumente **numPixeles**.

______________________________________________________________________

## **ng generate y ng lint:**

### **ng generate**:
Este es muy importante, es un comando que nos permite generar componentes, pipe, servicios, ect. en este primer caso lo usaremos para generar un componente, como los que hicimos manualmente con anterioridad. El comando es
```
ng g c nombre_del_componente
```
- ng: se refiere que es un comando para angular
- g:  generate
- c:  component

Este nos generar los archivos: ```.html .scss .ts .spec.ts```
Y nos agrega todo esto al _```app.module.ts```_

### **ng lint**: 
Este comando es bastante util a la hora del desarrollo en equipos de trabajo, nos permite identificar los errores de typado o uso de mala practica que tengamos en nuestro codigo.

Los comandos son:

- analizar el codigo
```
    ng lint
```

- obtiene el analizis en formato **```json```**

```
    ng lint -format json
```

- Corriege automaticamente el codigo dentro de lo posible
```
    ng lint -fix
```

______________________________________________________________________

## **Pipe**:
  Los **pipe** son metodos que trasnforman los datos
  syntax:
```html
  <p>{{ "var" | pipe }}</p>
```
> - **var**: es una variable de un tipo de archivo desplegable en html.
> - **pipe**: es el pipe.

Ejemplos:  
> El "string" se trasnformara a mayusculas
>```html
><h3>{{ "string" | uppercase }}</h3>
>```

> Pasar number a valor monetario usando _currency_
>```html
><strong>Price: {{ product.price | currency}}</strong>
>```

> Crear una variable **Date**, la cual nos entrega una fecha, y transformarla con un **pipe**, en nuestro archivo _.ts_ agregamos:
>```ts
>today = new Date();
>```
>utilizarewmos esta variable en nuestro _.html_ : 
>```html
><p>{{ today | date }}</p>
>```
>
>Podemos seguir modificando esto, agregandole su estilo propio, ejemplo: 
>[Documentation](https://angular.io/api/common/DatePipe#pre-defined-format-options)
>```html
><p>{{ today | date:'short' }}</p> //<--- agregamos el 'short'
>```