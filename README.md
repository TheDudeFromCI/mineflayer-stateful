<h1 align="center">mineflayer-stateful</h1>
<p align="center"><em>A copy-on-write wrapper for <a href="https://github.com/PrismarineJS/mineflayer">Mineflayer</a> bots to allow testing and AI mental simulations.</em></p>

***This plugin is not ready for release.***

---

### Purpose

The purpose of this plugin is to allow developers to wrap bot actions into a simple container that allows for a low-overhead approach to testing or simulating various actions within the game. This plugin does not aim to provide an accurate world simulation (for that, you should probably try hosting an embedded [server](https://github.com/PrismarineJS/flying-squid)) but instead to help aid developers to get a rough idea of what kind of a set of actions would have within a world.

In terms of AI development, this plugin can allow a bot to preform a set of actions and what kind of effects it would have on the world to aid in making decisions about the correct course of action to take. This plugin uses a copy-on-write approach to saving game data in order to minimize the resource overhead and allow for instant feedback.

In terms of testing, this plugin is useful for running a small set of functions against the bot in order to see what the final outcome is. The plugin aims to cleanly wrap the stable Mineflayer API, allowing simulations to be seemless between the state object and the real bot.

### Example

The statful plugin adds a new object type which wraps the Mineflayer bot cleanly in order to intercept functions being sent to the bot and redirect them to a fake state object for reading.

```js
// Our normal bot logic code.
function runSomeLogic(bot, offset) {
  const pos = bot.player.entity.position.offset(offset, 0, 0)
  const block = bot.getBlockAt(pos)
  
  bot.dig(block, err => {
    console.log(bot.getBlockAt(pos).type) // Prints air
  })
}

// Lets create a normal Mineflayer bot
const bot = mineflayer.createBot({ username: "Stateful_Bot" })

// Running the bot normally
runSomeLogic(bot, 3)
// The bot mines the block as expected, and "air" is printed to the console.

// Running the bot in a fake world
const state = new WorldState(bot)
runSomeLogic(state, 10)
// The bot preforms no action, yet "air" is still printed to the console.

// By sampling the state, it appears that the world has been modified.
// state.getBlockAt(...)

// But run the same function again on the actual bot, and the world remains unchanged.
// bot.getBlockAt(...)
```
