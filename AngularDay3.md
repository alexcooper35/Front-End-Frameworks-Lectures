# Front End Frameworks Day 3

1. [ Encapsulation ](#1) 
2. [ Services ](#2) 
3. [ Dependency Injection ](#3) 
4. [ @Input ](#4) 
5. [ Setting up the Cart Component and Add to Cart Functionality ](#5) 
6. [ Observables and Subject ](#6) 
7. [ Setting up the addToCart Handler ](#7)



## Encapsulation
https://angular.io/api/core/ViewEncapsulation
As we know, CSS aims to style selectors whether specified with an id, class or element name. Whatever styles are applied to a selector will be applied to any element with that name, id or class in any area of your project. 

In Angular styles work a little differently. Instead of applying global styles based only on the selector name, Angular aims to allow styles to be applied ONLY to the specific component in which they are being written for. 

If you take a look at one of your Angular examples in the dev tools and inspect a few elements you'll see that each one has an interesting attribute applied to it that looks something like: 
    
        _ng-content-ejo-0 

Angular gives each component's set of elements this unique attribute name. You'll notice that the attribute names vary based on the number at the end. This allows Angular to override CSS default behaviour and apply exclusive component styling.

We can actually change this behaviour by using the encapsulation property inside our component decorator and giving it one of three modes: emulate (the default), none (makes sure no new attributes are created and styles are global) and Native.

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.css'],
      Encapsulation: ViewEncapsulation.None;
    })


## Services
Angular services allow you to modularize your code so it can be re-used throughout the application.  Angular services are defined in simple classes.  We just call the function in the class and the code can be re-used by any component.

With services we can separate data handling code from a component's business logic and then simply call the service in our component when we need it. This promotes DRY principles and leads to better organization of our projects.

Last class we created an array of objects that defined our product data. Let's take that data and move it into a separate service file:

First, create a new folder called services with a product service file inside:

    ng generate service services/product


 In the new service file, add the following code:

     import { Injectable } from '@angular/core';
    import { Product } from '../products/product.model'

    @Injectable({
      providedIn: 'root'
    })
    export class ProductService {
      products: Product[] = [
        new Product(1, 'Geode Puzzle', 79.00, './assets/images/geode-puzzle.jpg'),
        new Product(2, 'Math Equation Glasses', 40.00, './assets/images/math-shots.jpg'),
        new Product(3, 'Smartphone Sanitizer', 179.00, './assets/images/phone-san.jpg'),
        new Product(4, 'Kitty Cat Mugs', 45.00, './assets/images/cat-mugs.jpg'),
        new Product(5, 'Unicorn Cake Set', 69.00, './assets/images/cake-set.jpg'),
        new Product(6,'Hourglass', 80.00, './assets/images/hour-glass.jpg'),
        new Product(7, 'Mountain Pen Holder', 39.00, './assets/images/pen-holder.jpg'),
        new Product(8, 'Retro Image Reel', 79.00, './assets/images/reel.jpg'),
      ]
      constructor() { }

      getProducts(): Product[]{
        return this.products
      }
    }

   
Inside the service file we have the @Injectable() decorator which defines this service as being injectable into other componenents. 

Before we go any further, modify the product model file to include an id that takes a number type and change the price property type to number as well. The entire file should look like the following:
        
    export class Product {
    public id: number;
    public name: string;
    public price: number;
    public imagePath: string;

    constructor(id: number, name: string, price: number, image: string) {
        this.id = id;
        this.name = name;
        this.price = price;
        this.imagePath = image;
    }
    }

### Dependency Injection
In Angular we have a module called an "injector" that works to maintain our services through dependency injection. This means that our components are dependent on a service for data injection. Instead of creating the service inside the component, we can instead request it in the component's contructor function. 

The constructor is a special method that gets called whenever we create new objects. 

Inside the product.component.ts file import the ProductService and the Product model:

    import { Component, OnInit } from '@angular/core';
    import { ProductService } from 'src/app/services/product.service';
    import { Product } from '../products/product.model';

Create an empty array in which you import the Product[] array from the Product model:

    export class ProductsComponent implements OnInit {
  
      productList: Product[] = []

Request the service inside the constructor function:

      constructor(private productService: ProductService) { }

In this line of code, we've added in a private productService parameter of type ProductService to the constructor. ProductService is the injection site. 
With this, Angular's dependency injection system sets the productService parameter to an instance of the service.

Next we want to call a method to retrieve the product from the service:

in product.service.ts:

    getProducts(): Product[]{
    return this.products
    }

Next we will call this method inside the product component's ngOnInit() lifecycle hook. The ngOnInit gets fired when a component is loaded. Whatever code is inside of this method will not be executed until the component is fully loaded. 

in product.component.ts:

    ngOnInit(): void {
        this.productList = this.productService.getProducts()
      }


Next modify the product.component.html with the following HTML code:

    <section class="products-section py-4">
        <div class="container">
            <header class="section-heading">
                <a href="#" class="btn btn-lg btn-outline-success mt-3 float-right">See all</a>
                <h3 class="display-4 text-muted">Popular Products</h3>
            </header>

            <div>
                <div class="row my-4">
                <div  *ngFor = "let product of productList" class="product-card px-1 my-3">
                <app-product-item></app-product-item>
                </div>
            </div>
            </div>
        </div>
    </section>

We need the product component to send the product data to the product-item component...

## @Input
https://angular.io/guide/inputs-outputs

Properties set inside a component are, by default, only accessible to those components. We can work around this by using the decorators (which are not limited to component classes) @Input and @Output. 

### @Input
https://www.bennadel.com/blog/3055-public-properties-component-inputs-and-the-change-detection-contract-in-angular-2.htm

@Input() is a built in decorator that is executed like a function before the property name:

    @Input() products: string;

You must import this decorator from @angular/core at the top of your component.ts file:

    import { Input } from "@angular/core";
    
By setting @Input() before the property name we are exposing this property to any parent component and enabling it for use within those components.

In our product.component.html add property binding in the <app-product-item> opening tag like so:

          <app-product-item [productItem]="product"></app-product-item>

This gives us a product object as the value for a future property called productItem. Let's add that property now into our product-item.component.ts file. Remember to add the @Input decorator and import it from angular/core.

         @Input() productItem: Product 
         
Modify the product-item.component.html to the following:



            <div class="col-lg-3 col-md-6">
    <div href="#" class="card card-product-grid" style="width: 19rem;">
            <a href="#" class="img-wrap"> 
                <img [src]="productItem.imagePath" class="card-img-top image" alt="product"> 
            </a>
            <div class="overlay">
                 <button type="button" class="btn btn-dark py-2 text">See Product</button>
            </div>
            <div class="card-body border-top text-center">
            <div class="card-text">
                <a href="#" class="card-link title"><h5 class="card-title text-muted">{{ productItem.name }}</h5></a>
                <div class="price mt-1">${{ productItem.price }}</div>
            </div>
        </div>
    </div>
</div>


## Setting up the Cart Component and Add to Cart Functionality

Create two new components. The first one should be the cart component, the second one should be a nested cart-item component.

The html for the cart component:


                <div class="container">
        <div class="alert alert-info">Cart is Empty</div>

        <ul class="list-group my-3">
            <li class="list-group-item">
                <app-cart-item></app-cart-item>
            </li>

            <li class="list-group-item bg-dark text-white">
                <strong>Total</strong>

            </li>
        </ul>
        </div>

The HTML for the cart item component:

                <div class="list-item">
            <span><strong>productName: </strong>
            $price
            <br>
            <strong>Qty:</strong>
            qty
            <br>
            <strong> Total:</strong>
            </span>
        </div>

Start by setting an empty array called cartItems in the cart component typeScript:

         
      cartItems = []   
      
In the cart component HTML, using the ngFor directive set it up to loop through the cartItems array. Then (like we did in the product component) use property binding to bind the cart item to a property called cartItem in the <app-cart-item></app-cart-item> selector:

        <div class="container">
    <div *ngIf = "cartItems.length === 0" class="alert alert-info">Cart is Empty</div>


    <ul *ngIf = "cartItems.length > 0" class="list-group my-3">
        <li class="list-group-item" *ngFor = "let item of cartItems">
            <app-cart-item [cartItem] = "item"></app-cart-item>
        </li>

        <li class="list-group-item bg-dark text-white">
            <strong>Total </strong>
            
        </li>
    </ul>
    </div>
    
Notice the optional use of ngIf. By using ngIf we can specify that we only want the cart div to show if there are items inside it, otherwise hide it and display a message that says the cart is empty.

Add the cartItem property inside the cart-item.component.ts file. Use the @Input() to allow this property to be used and returned with a value from the parent component.

        
         @Input() cartItem: any



Modify the cart-item HTML with interpolation:

    <div class="list-item">
        <span><strong>{{ cartItem.productName }}: </strong>
        ${{ cartItem.price }}
        <br>
        <strong>Qty:</strong>
        {{ cartItem.qty }}
        <br>
        <strong> Total:</strong>
        {{ (cartItem.qty * cartItem.price) }} </span>
    </div>

## Observables and Subject
https://angular.io/guide/observables-in-angular
https://rxjs.dev/api/index/class/Subject

In order to get the data from the product item component when it's clicked to the cart component and then rendered in the cart item component. we need to create another service that will serve as a pathway for communicating that data between these components.

This service will call two methods: one to get the data from the product items that are clicked and one to send the data to the cart component. 

Create a new service in the services folder:

    ng g service services/cartItem

### Observables
Observables in Angular are used for passing messages between different parts of our app. 
The service we just created is the observable as it will be observed by the cart component and the data collected will be rendered in the cart item component. 

In the service file, create those two methods:

        export class CartItemService {

          sendData(){

          }

          getData(){

          }



Before continuing, we need to implement a concept from RXJS-a library that helps in managing sequences of events.

https://rxjs-dev.firebaseapp.com/guide/overview
By implementing RXJS we can utilize one of the core practices- subjects. 

Subjects are much like observables and allow us to subscribe our components to them. The difference is that they are "multicast" meaning they can be subscribed to multiple times.

Once subscribed, the observer will then begin receiving values.

We can use specific methods to handle how and when values should be passed to the subscriber.

https://rxjs-dev.firebaseapp.com/guide/subject  
https://medium.com/@luukgruijs/understanding-rxjs-subjects-339428a1815b

In our cartItem service file import Subject from rxjs:

        import { Subject } from 'rxjs';

Next, we need to create an instance of the subject and set two methods to return some data from a source and allow another component to subscribe to the data flow. 

        import { Injectable } from '@angular/core';
        import { Subject } from 'rxjs';
        @Injectable({
          providedIn: 'root'
        })
        export class CartItemService {

          subject = new Subject()

          sendData(product){
            this.subject.next(product)
          }

          getData(){
            return this.subject.asObservable()
          }
          constructor() { }
        }

The getData() method sets the data source as an Observable so that any component can subscribe to it.

The sendData() method above will be called by the product item and then the product data will be sent to whatever component subscribes to this subject.

Summary: When the button is clicked on our product, the event is triggered, the data provided by that event is returned and the source is set as an Observable so that it can be subscribed to by a component.

This service now needs to be injected for use in the product item component. 

        import { CartItemService } from 'src/app/services/cart-item.service';


In the product item constructor we will implement our cartItem service which is a global object, and assign it to a private variable:

         constructor(private data: CartItemService) {
   }


Finally, create an event handler function that will call the sendData method and pass the product item data:

        addToCart(){
        this.data.sendData(this.productItem)
      }


Your product-item.component.ts file should look like the following:

    

        import { Component, OnInit, Input } from '@angular/core';
        import { Product } from 'src/app/products/product.model';
        import { CartItemService } from 'src/app/services/cart-item.service';

        @Component({
          selector: 'app-product-item',
          templateUrl: './product-item.component.html',
          styleUrls: ['./product-item.component.css']
        })
        export class ProductItemComponent implements OnInit {

          @Input() productItem: Product  

          constructor(private data: CartItemService) {
           }

          ngOnInit(): void {
          }

          addToCart(){
            this.data.sendData(this.productItem)
          }

    }


In order to get the data and use it we need to set up the cart component to subscribe to the Observable (which we set up in the getData method).

In the cart.component.ts file, subscribe to the Observable by first importing the cartItem service:

     import { CartItemService } from 'src/app/services/cart-item.service';


Just like we did in the product item typeScript, assign the CartItemService to a private variable called data.

        constructor(private data: CartItemService) {}

As our cart component is loaded we want it to immediately subscribe to the getData observable by using the subscribe() method provided by subject rxjs.


    ngOnInit(): void {

    this.data.getData().subscribe(product)

Subscribe() listens in preparation to receive some data and then executes the instructions that we set inside it:
    
     this.data.getData().subscribe(product: Product) => {
        this.addProduct(product)
        })
     }

## Setting up the addToCart Handler
The addProduct function will be executed when we click the product add to cart button. We do not have that set up in the product-item.component.html yet so let's modify that code to the following:

        <div class="col-lg-3 col-md-6">
        <div href="#" class="card card-product-grid" style="width: 19rem;">
                <a href="#" class="img-wrap"> 
                    <img [src]="productItem.imagePath" class="card-img-top image" alt="product"> 
                </a>
                <div class="overlay">
                     <button type="button" (click)="addToCart()" class="btn btn-dark py-2 btn-block">Add to Cart</button>
                </div>
                <div class="card-body border-top text-center">
                <div class="card-text">
                    <a href="#" class="card-link title"><h5 class="card-title text-muted">{{ productItem.name }}</h5></a>
                    <div class="price mt-1">${{ productItem.price }}</div>
                </div>
            </div>
        </div>
    </div>
    
Notice that we changed the button text to say "add to cart" and used event binding to trigger addToCart().

Next let's set up the addProduct function that will be executed when the product button is clicked.


    addProduct(product: Product){
    let productExists = false;

    for(let i in this.cartItems){
      if(this.cartItems[i].productId === product.id) {
        this.cartItems[i].qty++
        productExists = true
        break;
      }
    }

    if(!productExists) {
      this.cartItems.push({
        productId: product.id,
        productName: product.name,
        qty: 1,
        price: product.price
      })
     }   
    }

In the above code we check whether a product exists in the cart yet. If it does in fact exist and is clicked again, we want to increment the quantity by 1 using the push method and assign the object properties.

With the following final code we get the cart total by adding the qty value to the price value of the assigned product item:

     this.cartTotal = 0
        this.cartItems.forEach(item => {
          this.cartTotal += (item.qty * item.price)
        })

The entire cart component typeScript file should look like this:

        import { Component, OnInit } from '@angular/core';
        import { CartItemService } from 'src/app/services/cart-item.service';
        import { Product } from 'src/app/products/product.model';


        @Component({
          selector: 'app-cart',
          templateUrl: './cart.component.html',
          styleUrls: ['./cart.component.css']
        })

        export class CartComponent implements OnInit {

          cartItems = [];

          cartTotal = 0

          constructor(private data: CartItemService) {}

          ngOnInit(): void {
              this.data.getData().subscribe((product: Product) => {
              this.addProduct(product)
              }) 
          }

          addProduct(product: Product){
            let productExists = false;

            for(let i in this.cartItems){
              if(this.cartItems[i].productId === product.id) {
                this.cartItems[i].qty++
                productExists = true
                break;
              }
            }

            if(!productExists) {
              this.cartItems.push({
                productId: product.id,
                productName: product.name,
                qty: 1,
                price: product.price
              })
            }
            this.cartTotal = 0
            this.cartItems.forEach(item => {
              this.cartTotal += (item.qty * item.price)
            })
         }
       }

After implementing total cart functionality here, modify the cart.component.html file to pass the calculated value:

        <div class="container">
    <div *ngIf = "cartItems.length === 0" class="alert alert-info">Cart is Empty</div>


    <ul *ngIf = "cartItems.length > 0" class="list-group my-3">
        <li class="list-group-item" *ngFor = "let item of cartItems">
            <app-cart-item [cartItem] = "item"></app-cart-item>
        </li>

        <li class="list-group-item bg-dark text-white">
            <strong>Total </strong>
            ${{ cartTotal }}
        </li>
    </ul>
    </div>
    
