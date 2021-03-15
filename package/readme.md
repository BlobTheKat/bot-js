# What it does
It's a discord and firebase boilerplate (the base code) combined with an elaborate command handler and a few extra features
# How to setup

```js
let bot = require("bot-js")(TOKEN, FIREBASE_TOKEN)
//no need for bot.on("message", ...)
bot.set({
  prefix: "p!",
  pingHint: "Message to say when bot gets pinged",
  errorHint: "Message to say when error occurs"
})
```

# Basic Usage
```js
bot.cmd("ping", function(msg){ //Use bot.cmd for commands with prefix
  msg.reply("Pong!")
}
bot.cmd("say :str", function(msg, string){ //parameter
  msg.channel.send(string)
})
bot.cmd("xp :user", function(msg, userObject){
  //automatically converts to a discord user object
  msg.channel.send("Level of user: "+this.user.level)
  //"this" is an object containing useful data such as user data
  // and guild data
})
bot.cmd("total ::int",function(msg, nums){
  //:: means a spread parameter (the rest of parameters)
  //so "p!total 1 2 3" will make "nums" be [1,2,3]
  msg.reply(`The total is ${nums.reduce( (a,b)=>a+b )}`)
}
bot.cmd(":", function(msg, a){ //: is wildcard: it means any type
  //note that wildcards work like the ::
  //you can't have anything else after it

  //we can use this as a fallback for an invalid command
  msg.reply("Invalid command lol")
})
bot.msg(":int", function(msg, num){ //bot.msg = prefix-less command
  //counting command, I'll let you code it yourself!
  return true; //returning true will allow other commands to run
  //(if there are any other matching commands)
})
/*
Note: commands run top-to-bottom,
by default, only 1 command may run per message (the first declared)
and you can add your own types using this function:
bot.type(/regexToMatch/, (string, msg) => parseType(string))
*/
```
# this
`this` refers to an object which contains `user`, the userdata (from firebase) and `guild` (the guilddata from firebase)
any modifications to `this` from a command will be spread to all other commands for the current message, so it can be useful to calculate data before a command is ran, instead of calculating it in every command. Example:
```js
bot.msg(":", function(msg){ //for every command used
  this.userLevel = this.user.xp / 1000
  this.authorIsAdmin = (msg.author.id == "123456789123456789")
  return true //Important: allow the other command to run after
})
...
bot.cmd("ban :user", function(msg){ //example command
  if(this.authorIsAdmin){
    //ban the user
  }
})
```
# Supported types
`:int` - an integer (series of digits)
`:num` - any number, including decimals
`:str` - string
Note: to include spaces in a string when *using* the command, put the string around quotes (for example, `p!say "hello world"`)
`:bool` - a boolean, supports yes/no, true/false, 1/0, but we convert it to a regular boolean for you
`:user` - a discord user, or `null` if it's invalid
`:member` - same as user, except they must be in the guild
`:channel` - a discord channel, or `null` if it's invalid
`:role` - a role that's in the server

# Issues
Please contact me on discord because i really really cba to put this on github