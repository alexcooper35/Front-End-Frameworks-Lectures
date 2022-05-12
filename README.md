# Front End Frameworks Day 7
###### tags: `react`

1. [Separating Product Data](#1)
2. [Mapping Data](#2)
3. [React State](#3)
4. [Handling State with Functional Components](#4)
5. [Hooks](#5)
6. [A React Component's Lifecycle](#6)
7. [Working with External APIs](#7) 


## Separating Product Data 
Last class we managed to pass our product item component some data that we stored as properties inside our product component.   

Let's take this one step further by storing our product data in a separate file.  

Create a new folder in your src directory called "data" and add a new file inside called productData.js.  

In this file we will store the product data as objects inside of an array.  

productData.js

    const productData = [
        {
            name: "Geode Puzzle",
            imgUrl: "images/geode-puzzle.jpg",
            price: "69.99"
        },
        {
            name:"Math Shot Glasses",
            imgUrl:"images/math-shots.jpg",
            price:"69.99"
        },
       {
            name:"Smartphone Sanitizer",
            imgUrl: "images/phone-san.jpg",
            price:"49.99"
        },
        {
            name: "Cat Mugs",
            imgUrl: "images/cat-mugs.jpg",
            price: "29.99"
        }

    ]

    export default productData;

Next pass the data by the array name and index to the product components inside product.js:

Product.js

    import React, { Component } from 'react'
    import Productitem from './Product-item'
    import productData from '../productData'

    export default class Products extends Component {
        render() {
            return (
                <section className="products-section py-4">
                <div className="container">
                    <header className="section-heading">
                        <a href="index.html" className="btn btn-lg btn-outline-success mt-3 float-right">See all</a>
                        <h1 className="display-4">Popular Products</h1>
                    </header>
                    <div className="row my-4">
                        <Productitem
                        name={productData[0].name}
                        img={productData[0].imgUrl}
                        price={productData[0].price} />
                        <Productitem
                        name={productData[1].name}
                        img={productData[1].imgUrl}
                        price={productData[1].price} />
                        <Productitem
                        name={productData[2].name}
                        img={productData[2].imgUrl}
                        price={productData[2].price} />
                        <Productitem
                        name={productData[3].name}
                        img={productData[3].imgUrl}
                        price={productData[3].price} />
                    </div>
                  </div>
                </section>
            )
        }
    }

## Mapping Data
https://reactjs.org/docs/lists-and-keys.html  
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map  

The map function allows us to map through items inside an array by calling a function for each iteration.  

Inside product.js first import the productData file.  

Next, create a function and pass it "product" as a parameter. Set this function to return the Productitem component structure with the use of prop definitions. 

Product.js

        import React, { Component } from 'react'
        import Productitem from './Product-item'
        import productData from '../productData'

        function createProduct(product){
            return <Productitem
                    name = {product.name}
                    img = {product.imgUrl}
                    price = {product.price}
                    />;
        }


Using the map function to map through the productData items and call the createProduct function.

        export default class Products extends Component {
            render() {
                return (
                    <section className="products-section py-4">
                    <div className="container">
                        <header className="section-heading">
                            <a href="index.html" className="btn btn-lg btn-outline-success mt-3 float-right">See all</a>
                            <h1 className="display-4">Popular Products</h1>
                        </header>
                        <div className="row my-4">
                            {productData.map(createProduct)}
                        </div>
                      </div>
                    </section>
                )
            }
        }

When using the map function we must include a unique key. This is essentially our item id. Inside the createProduct function set key to equal `product.id`.

            import React, { Component } from 'react'
            import Productitem from './Product-item'
            import productData from '../productData'

            function createProduct(product){
                return <Productitem
                        key = {product.id}
                        name = {product.name}
                        img = {product.imgUrl}
                        price = {product.price}
                        />;
            }


Next, set id as a property in the productData array in productData.js. Give a value for each object:

productData.js

        const productData = [
            {   
                id:1,
                name: "Geode Puzzle",
                imgUrl: "images/geode-puzzle.jpg",
                price: "69.99"
            },
            {
                id:2,
                name:"Math Shot Glasses",
                imgUrl:"images/math-shots.jpg",
                price:"69.99"
            },
           {
                id:3,
                name:"Smartphone Sanitizer",
                imgUrl: "images/phone-san.jpg",
                price:"79.99"
            },
            {
                id:4,
                name: "Cat Mugs",
                imgUrl: "images/cat-mugs.jpg",
                price: "29.99"
            }

        ]

        export default productData;


## React State
https://reactjs.org/docs/faq-state.html

React keeps track of a component's state through an object set on the class with a name of "state". This object typically contains some data that is relevant to a component.  

State can only be used with class based components. Functional components require hooks to keep track of state. We will discuss this further later on.  

This class property is special and must be called "state" for React to keep track of it.  

State should be initialized when we create a component as state will always contain information that determines the initial "state" of our component.   

Updating properties inside the state object allows our component to instantly re-render.   

To change your components state (update the state) use the "setState()" method.   

We NEVER update the value of state in it's initialization. We only ever update it inside the setState method.  

### Setting up a component's initial state:

As we've learned, constructor functions are the first function to be called when a component is created. This is typically a good place to initialize our component's state.  

Let's see an example:

In a new project demo folder I've created a new react app that will show us how to implement the state object in a component and update it using setState.

Inside App.js I've modified the default template to be a class component:

    import React, { Component } from 'react'

    export default class App extends Component {
      render() {
        return (
          <div>
              <h1>Count:</h1>
              <button>+</button>
          </div>
        )
      }
    }

We know that one of the best places to initialize state is inside a constructor function. Let's set that up:


    export default class App extends Component {

      constructor(props){
        super(props);

        this.state = {count:0}
      }
      render() {
        return (
          <div>
              <h1>Count:</h1>
              <button>+</button>
          </div>
        )
      }
    }
    
In the above code we set up our constructor function and passed it the param "props". Adding "props" here will become apparent when we take a closer look at the next line.

### super();
`super(props);` is a reference to the parent class constructor. Our app component is extending the react base class that has a constructor function of its own. This constructor function sets up our react component for us.

When we create our own constructor function inside our class we're essentially overriding that built-in react component class constructor.

In order to ensure that the react.component constructor function still gets executed, we call super().

When creating a constructor function inside our class component we **always** have to set super(), otherwise we get an error.

Passing props down to super is necessary so that the base React.Component constructor can initialize this.props. 

Consider the following line:

    this.state = {count:0}
    
Here, we define the state object with the property count and give it an initial state value of 0. We then assign it to the this.state property.

We can now reference this state object and the properties inside from other functions or JSX in our component.

    render() {
        return (
          <div>
              <h1>Count: {this.state.count}</h1>
              <button>+</button>
          </div>
        )
      }
    }

Next we need to add a click event to our button and assign it a property that handles what should happen when the user clicks the button:

    handleClick = () => {
        this.setState((prevState) => {
          return {
            count: prevState.count +1
          };
        })
      }

      render() {
        return (
          <div>
              <h1>Count: {this.state.count}</h1>
              <button onClick={this.handleClick}>+</button>
          </div>
        )
      }
    }

Inside handleClick we've used "setState". This is a special function that is used to update the state of our component. This method gets the current state of the component passed in with the use of "prevState".   

As a rule, never update the state object directly.  

Instead, update the value of the "prevState" object. React will then take care of updating the state of your component. 

The entire App.js file should look like the following:

    import React, { Component } from 'react'

    export default class App extends Component {

      constructor(props){
        super(props);

        this.state = {count:0}
      }

      handleClick = () => {
        this.setState((prevState) => {
          return {
            count: prevState.count +1
          };
        })
      }

      render() {
        return (
          <div>
              <h1>Count: {this.state.count}</h1>
              <button onClick={this.handleClick}>+</button>
          </div>
        )
      }
    }
    
## Handling State with Functional Components

Previously known as "Stateless Components", functional components have only recently evolved to handle a component's state through the use of hooks.  

Hooks are functions that allow us to "hook" into the state of a component, read it and modify it. Hooks are not usable in classes.  

## Hooks
https://reactjs.org/docs/hooks-intro.html

The useState() hook allows us to use state variables inside functional components.  

useState is a named export in React and must be imported from the react module. We can set up our App component as the following:

    import React, { useState } from 'react'
   
    function App() {
      const state = useState();

      function handleClick(){

      }

      return (
        <div>
          <h1>Count:</h1>
          <button onClick={handleClick}>+</button>
        </div>
      )
    }

    export default App;

Notice how we've assigned the return value of useState() to a constant called state. The value that we place between the useState brackets is the initial state value.  

By default useState outputs an array with a value and a function. In ES6 we have a concept called destructuring which allows us to destructure complex structures like arrays and objects.  

Through destructuring we can use square brackets to declare and deconstruct an array and then create a name for each element in the array.  

A classic example:

    const [red, green, blue] = [9, 142, 237];
    console.log(red); // returns 9

We can implement this when dealing with state in the following way.  

Let's first change the variable name from "state" to "count" and use destructuring to set it as the initial state value in our array.

    const [count] = useState(0);
    
Whenever we want to access the value that's stored inside the state we can use {count} inside our JSX template. 

    <h1>Count: {count}</h1>

If you run this in the browser you'll see:

![](https://i.imgur.com/RBT7JZl.png)

Set the second value in the deconstructed array to a function that we can use to set a new state for our count.

    const [count, setCount] = useState(0);

We can now call setCount() inside our handleClick function and give it any value. Set the value to the current value of count plus 1:

    setCount(count + 1);

Summary: When the app loads up we call useState and give it an initial value (or state). This value is stored inside the variable count and then rendered in our template. When the user clicks the button, handleClick function is triggered and we then call setCount to update the state of the count variable. 

Complete code:

    import React, {useState} from 'react'
    
    function App() {
      const [count, setCount] = useState(0);

      function handleClick(){
        setCount(count + 1);
      }

      return (
        <div>
          <h1>Count: {count}</h1>
          <button onClick={handleClick}>+</button>
        </div>
      )
    }

    export default App;

## A React Component's Lifecycle
https://reactjs.org/docs/react-component.html#the-component-lifecycle  
https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/  

A react component has 3 stages in its life:  
1. Mounting:  
    - The component is created and rendered in the DOM  
2. Updating:   
    - If a component's state changes, it updates  
3. Unmounting:   
    - The component is removed from the DOM  

Each of these three stages in a component's lifecycle can fire built-in lifecycle methods:  

When the component has been rendered to the page during the mounting phase, React will call the **"componentDidMount()"** method if one has been defined.  
The component will then wait for any possible updates. If an update occurs, the **"componentDidUpdate()"** method will be called. The component will then wait in its updated state until another change happens or it is "unmounted" from the DOM and no longer rendered. When this happens the **"componentWillUnmount()"** method is run.  

The code that you wish to run when a lifecycle method gets called is placed inside these lifecycle method definitions.  
You must name these lifecycle methods exactly as named in the React docs for these lifecycle methods to work correctly.  

## Working with External APIs

For the HWS I'm using a product API from https://fakestoreapi.com  

The first step will be setting up our constructor function with super(props) and then defining the state object with a few properties.   

Inside product.js remove the productData.js file from the imports and add the following:  

    import React, { Component } from 'react'
    import Productitem from './Product-item'
 
    export default class Products extends Component {
        constructor(props){
            super(props);
            this.state= { 
                products: [],
                isLoaded: false,
            }
        }

In the above constructor function we assigned an empty array to a property called "products". We also set a property "isLoaded" to false. We can use this to conditionally test whether items are loading or have loaded from the API and give the user information on the status.   

Our component will now be rendered and at this point we can set specific details about how it should be rendered through the use of the componentDidMount() lifecycle method. This is where we can fetch the data from our API and have it rendered in the view.  


            componentDidMount(){
            fetch('https://fakestoreapi.com/products')
                .then(res=>res.json())
                .then(json=> {
                    this.setState({
                        isLoaded: true,
                        products: json,
                    })
                });
        }
        
In the above code we use the fetch() method that takes one mandatory argument, the path to the resource you want to fetch. https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API#:~:text=The%20fetch()%20method%20takes,second%20argument%20(see%20Request%20).   

The fetch method returns a promise that results in a response. In our case, we fetch the data and return a response that is converted to JSON. We then save the data inside our component's state and use setState to notify the user that the products have loaded and pass the data to the products property we defined when we initialized the state object.  
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise  

The componentDidMount() method will run after the render method and then update the render method to output the resulting data.

Inside the render method access the isLoaded and products properties from the state object with destructuring:

    const {isLoaded, products } = this.state;

Next, using the isLoaded boolean, test whether the data has loaded, if not return a div that displays some content to inform the user that the products are loading. If the product data has loaded, return the product component content:

        render() {
            
            const { isLoaded, products } = this.state;

            if(!isLoaded){
                return <div>Loading...</div>
            }
            else {
                return (
                    <section className="products-section py-4">
                    <div className="container">
                        <header className="section-heading">
                        <a href="index.html" className="btn btn-lg btn-outline-success mt-3 float-right">See all</a>
                            <h1 className="display-4">Popular Products</h1>
                        </header>
                        <div className="row my-4">
                            {products.map(createProduct)}
                        </div>
                      </div>
                    </section>
                );
            }
        }
    }

Notice that we're mapping through the products array and assigning a function to be run for each iteration. We can define this function just like we did in our initial mapping data demo:

    function createProduct(product){
        return <Productitem
                key = {product.id}
                name = {product.name}
                img = {product.image}
                price = {product.price}
                />;
    }

Complete code:

    import React, { Component } from 'react'
    import Productitem from './Product-item'

    function createProduct(product){
        return <Productitem
                key = {product.id}
                name = {product.name}
                img = {product.image}
                price = {product.price}
                />;
    }

    export default class Products extends Component {
        constructor(props){
            super(props);
            this.state= { 
                products: [],
                isLoaded: false,
            }
        }

        componentDidMount(){
            fetch('https://fakestoreapi.com/products')
                .then(res=>res.json())
                .then(json=> {
                    this.setState({
                        isLoaded: true,
                        products: json,
                    })
                });
        }

       
        render() {
           
            const { isLoaded, products } = this.state;

            if(!isLoaded){
                return <div>Loading...</div>
            }
            else {
                return (
                    <section className="products-section py-4">
                    <div className="container">
                        <header className="section-heading">
                        <a href="index.html" className="btn btn-lg btn-outline-success mt-3 float-right">See all</a>
                            <h1 className="display-4">Popular Products</h1>
                        </header>
                        <div className="row my-4">
                            {products.map(createProduct)}
                        </div>
                      </div>
                    </section>
                );
            }
        }
    }
