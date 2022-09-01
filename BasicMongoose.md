# Get Started with Mongoose



## Importing mongoose 

```js
const mongoose = require('mongoose');
```


## Connecting to localhost mongodb server
```js
mongoose.connect('mongodb://localhost:27017/mydb')
```


## Creating a Schema
```js
const productSchema = new mongoose.Schema({
   name: {
    type:String,
   },
   price:Number
})

```

## Creating a new model from product Schema
- The `mongoose.model()` function has 2 required parameters:
- The 1st param is the model's name, a string
- The 2nd param is the schema
 
```js

const Product =  mongoose.model('product',productSchema)

```

## creating a new prodcut
```js
const product = new Product({
    name:'I Phone',
    price:'800',
}) 

```
```js 
// printing the values of products
console.log(product.name);
console.log(product.price);
```


## Storing and Querying Documents


```js 
   await product.save()

   const product = await Product.find();
   console.log(product)
```



   

