# Virtuals

-  A Virtual is a property that is not stored in MongoDB
- Virtuals can have getters and setters just like normal properties
- Virtuals have some neat properties that make them ideal for expressing the notion of a property that is a function of other properties.

```js
const userSchema = mongoose.Schema({
    email:String
})

userSchema.virtual('domain').get(function() {
    return this.email.slice(this.email.indexOf("@") +1);
})

const User = mongoose.model('User',userSchema);

(async() => {
    let doc = await User.create({
        email:"test@gmail.com"
    })
    console.log(doc.domain);// gmail

// Mongoose ignores setting virtuals that don't have a setter

    doc.set({email:"test@test.com",domain:'foo'})
    console.log(doc.domain); // test
})();

```