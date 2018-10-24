# NiftyQuest

![Cover](https://github.com/gkapkowski/niftyquest/raw/master/cover.png)

Fork of [Phaser Quest](https://github.com/Jerenaux/phaserquest) using NFT as charaters and other tokens inside game play.
- The [Phaser](https://phaser.io/) framework for the client 
- [Socket.io](http://socket.io/) and [Node.js](https://nodejs.org/en/) for the server and client-server communication

## Quick tour of the code

### Client

The game canvas and the game states are created in `js/client/main.js`. The `Home` state is started first, and will display the home page
of the game. The `Game` state is started upon calling `startGame()` from the `Home` state. 

`js/client/game.js` contains the  `Game` object, which corresponds to the `Game` state and contains the bulk of the client code. 
`Game.init()` is automatically called first by Phaser, to initialize a few variables. `Game.preload()` is then called, to load the
assets that haven't been loaded in the `Home` state. When all assets are loaded, Phaser calls `Game.create()` where the basics of the game
are set up. At the end of `Game.create()`, a call is made to `Client.requestData()` (from `js/client/client.js`) to request initialization
data from the server. Upon reception of this data, `Game.initWorld()` is called, which finishes starting the game. The main update loop of the client is `Game.update()`. 

### Server and updates

`server.js` is the Node.js server that supports the game. Most of the server-side game logic however is located in `js/server/GameServer.js`. Every 200ms, `GameServer.updatePlayers()` is called, and sends updates to all clients (if there are updates to send, as determined by the custom interest management system). Client-side, these updates are processed by `Game.updateWorld()` and `Game.updateSelf()`. 

The code used for the custom binary protocol for the exchange of update packets can be found in `js/client/Decoder.js`, `js/server/Encoder.js` and `js/CODec.js`.

## Installing and running the game

For the client, everything is included in the code (`phaser.js`, `easystar.min.js`, ...). You will need [npm](https://www.npmjs.com/) to install the Node.js packages required for the server. To run the server, you'll need to have Node.js installed, as well as [MongoDB](https://www.mongodb.com/).

Clone the repository. Inside the newly created directory, run `npm install` to install the Node.js packages listed in `package.json`. Make sure that you have MongoDB running, then run `node server.js` to start the game server. 
By default, it'll listen to connections on port `8081`; you can change that behaviour by using the `-p` flag (e.g. `node server.js -p 80`). 
By default, it'll attempt to connect to MongoDB on port `27017`; you can change that behaviour by using the `--mongoPort` flag (e.g. `node server.js --mongoPort 25000`).

### Using Docker

Alternatively, you can use the Dockerfile to create a container with all the necessary components already installed (thanks to Martin kramer for the corresponding pull request). You need to have [Docker](https://www.docker.com) installed. Then, in the directory where you clones the project, run:

```
docker-compose build
```
```
docker-compose up -d
```

The default por when using the Docker way is `80`, so you need to navigate to `<IP_of_your_Docker_machine>:80` to be able to access the game (e.g. 192.168.99.100:80). 

## Modifying the map

In `assets/maps/`, you can find `phaserquest_map.tmx`, which is the Tiled file of the map of the game, to be edited with the [Tiled Map Editor](http://www.mapeditor.org/). One you have made modifications in the Tiled file, you need to export it as a JSON file. But that file will contain a lot of layers, a legacy from how the original Browserquest map was designed. A lot of layers will translate to a very poor performance with Phaser, which is a shame since most of these layers contain only a few tiles. The solution is to "flatten" them to cram as many tiles as possible in the same layers. You can do so by running `formatMap()` from `js/server/format.js`. It will look for a `map.json` file in `assets/maps` and output two new files, the flattened map files for the client and the server.

## Donate

You can support this initiative by supporting Rassmus character on [Tok'n'talk](https://tokntalk.club/ethereum:0x71c118b00759b0851785642541ceb0f4ceea0bd5:1732/supporters)
