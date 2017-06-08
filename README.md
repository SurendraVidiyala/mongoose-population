# mongoose-population
* Referencing one document from another in MongoDBcan be acheived by using Mongoose population   to populate information in a document from a referenced document. 
  - Use Mongoose population to cross-reference users within a comment
  - Populate the comments with the users’ information upon querying

````
var User = new Schema({
    username: String,
    password: String,
    firstname: {
      type: String,
        default: ''
    },
    lastname: {
      type: String,
        default: ''
    },
    admin:   {
        type: Boolean,
        default: false
    }
});
User.methods.getName = function() {
    return (this.firstname + ' ' + this.lastname);
};
````
- In the models do the changes to utilize the population
* Comment schema
````
var commentSchema = new Schema({
    rating:  {
        type: Number,
        min: 1,
        max: 5,
        required: true
    },
    comment:  {
        type: String,
        required: true
    },
    postedBy: {
        type: mongoose.Schema.Types.ObjectId,
        ref: 'User'
    }
}, {
    timestamps: true
});
````
# Updating users.js route
- Open users.js and update the register function as follows:
````
router.post('/register', function(req, res) {
    User.register(new User({ username : req.body.username }),
        req.body.password, function(err, user) {
        if (err) {
            return res.status(500).json({err: err});
        }
                if(req.body.firstname) {
            user.firstname = req.body.firstname;
        }
        if(req.body.lastname) {
            user.lastname = req.body.lastname;
        }
                user.save(function(err,user) {
            passport.authenticate('local')(req, res, function () {
                return res.status(200).json({status: 'Registration Successful!'}
                  );
            });
        });
    });
});
````
Save all the changes and start the server and test.