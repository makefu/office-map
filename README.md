# MLS Workadventure Office Map
Welcome to the workadventure map of TTS MOM.

## Structure

- `*.json` tiled map files
- `entry.json` - the default map which will be loaded when a user connects via [work.search4ce.app.de.corp.thales](https://work.search4ce.app.de.corp.thales)
- `*.png` - the sprites used for the maps we have

## Documentation
See https://workadventu.re/create-map.html on how to create and edit maps

In order to edit this map for WorkAdventure, you need:

- the [Tiled editor](https://www.mapeditor.org/) software
- "tiles" (i.e. images) to create your map (this starter kit provides a good default tileset for offices)


## Release workflow

The Jenkinsfile is configured to provide a Continuous Delivery Environment.
All changes to the map should be performed via Pull Requests to avoid breakage
of the map.

In the pipeline the original map and tiles will be `optimized` with the
`map-optimizer` tool for workadventure.

### master branch

Commits towards the `master` branch will be delivered to the workadventure
instance as the new map everyone will be seeing when the (re-)connect to the
server.  The map can be found under [https://work.search4ce.app.de.corp.thales/maps/](https://work.search4ce.app.de.corp.thales/maps/)
and `entry.json` will serve as the default map for the server. For everyone to
use the map, they have to refresh the connection.

### Not master branch

Every commit `not` to master will also be deployed to the workadventure
instance, however it will be copied to [work.search4ce.app.de.corp.thales/maps/snapshot/](https://work.search4ce.app.de.corp.thales/maps/snapshot/)
and will not be used by default. However, you can test these maps starting the
workadventure instance via [work.search4ce.app.de.corp.thales/maps/snapshot/entry.json](https://work.search4ce.app.de.corp.thales/maps/snapshot/entry.json)
to test it before pushing the change to master.
