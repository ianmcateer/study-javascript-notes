WHy introduciton of arrow funcions? they are more precise syntax and they share the lexical ‘this’ with the parent scope

```js
// function declaration 
function add(x, y){
    return x + y
}

// fn expression
var add = function(x, y){
    return x + y
}

var add = (x, y) => {
    return x + y
}

```

can see the benefit here...

```js
users.map(function(user) {
    
})

users.map(() => )
```

```js
var FriendsList = React.createClass({
  getInitialState () {
    return {
      friends: [
        {id: 0, name: 'Mikenzi'},
        {id: 1, name: 'Ryan'},
        {id: 2, name: 'Jake'},
      ]
    }
  },
  onAddFriend (friend) {
    this.setState({
      friends: this.state.friends.concat([friend])
    })
  }
  render () {
    return (
      <ul>
        {this.state.friends.map(function (friend) {
          return <FriendItem key={friend.id} handleAddFriend={this.onAddFriend}>{friend.name}</FriendItem>
        })}
      </ul>        
    )
  }
});
```

can you spot the error? inside the this.state.friends.map invocation were handing the FrendItem component this.onAddFriend/ The problem here is that ‘this’ in this context, doesnt have onAddFriend method- because we have created another function we are in a different context. One way to fix it is to bind the function. Another more elegant way is to use an arrow function instead- the reason for this is because arrow functions do not create a new context, so “this” keyword inside map will be the same as “this” keyword outside of map which solves our problem