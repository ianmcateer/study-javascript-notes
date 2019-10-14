Destructuring gives us a convenient way to extract values from data stored in objects and arrays.

Lets say we had a Stateless Functional Component that looked like this:

```js
function register (props) {
  return (
    <div>
      <span>Email:</span>
      <input type='text' onChange={props.onChangeEmail} value={props.email} />
      <span>Password:</span>
      <input type='text' onChange={props.onChangePassword} value={props.password} />
      <button onClick={props.submit}>Submit</button>
    </div>
  )
}
```

props is an object, and just by looking at it we are not sure whats on it- a nice way to write functions is to have them be as obvious as possible- meanig when i look at my componenti want to know exactly what values it needs in order to work properly- one way to solve this problem (not just propTypes) would be...

```js
function register (props) {
  var { onChangeEmail, email, onChangePassword, password, submit }  = props;
  return (
    <div>
      <span>Email:</span>
      <input type='text' onChange={onChangeEmail} value={email} />
      <span>Password:</span>
      <input type='text' onChange={onChangePassword} value={password} />
      <button onClick={submit}>Submit</button>
    </div>
  )
}
```

Here we are basically plucking those properties off and setting them to local variables with the same name as the specific property on the object

If you want to add properties to an oject do it on the right hand side of the = sign

If you want to extract properties of an object you do it on the left hand side of the = sign

### Array Destructuring

```js
var user = ['ian', 'ianmcateer', 'eden']
var name = user[0]
var handle = user[1]
var location = user[2]
// can do this...
var [name, handle, location] = user
```

Advanced Features..

what if we wanted the variable name to be different fform the proeprty name

```js
var user = {
 n: 'ian mcateer',
 h: '@ianmcateer'
}

// destructuring and naming 
var {n: name, h: handle} = user;

```

another example...

```js
const {component: Component}
return {<Component/>}
```

another example...

```js
function fetchRepos (language, minStars, maxStars){
    
}

// have to look at documentation to know the order and what arguments are needed
fetchRepos('javascript', 100, null )
```

what if instead we just pass in an object as the argument. before we even have to read the funciton defenition of fetchRepos we know exactly what it needs and more improtant the orde rno longer batters

```js
function fetchRepos({language, minStars,maxStars}){
    // old way of setting defult values
    language = language || 'All'
    minStars = minStars || 0
}

// but can do this now 
function fetchRepos({language= 'All', minStars= 0, maxStars= ''}){

}


// dont even need the null values actually
fetchRepos({
  language: 'js',
  maxStars: null, 
  minStars: 100
})
```

Same thing can be done with arrays- best example is Promise.all

```js
function getUserData(player){
    return Promise.all([getProfile(player),getRepos(player)].then(function(data){
        // var profile = data[0]
        // var repos = data[1]
        var [profile, repos] = data
        return {
            profile: profile,
            repos: repos
        }
    }
    )
}
```

if keys are same as values can just delete the keys

with methods...

```js
{
    save: function(){
        // save message
    }
    // can now look liek this...
    save (){
        
    }
}
```

