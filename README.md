# voxel-touch-hello-world

This is a BB10ified version of the [voxel-hello-world](/maxogden/voxel-hello-world) repo.  You can learn all about voxel at: <http://voxeljs.com>.

## Compiling for the BB10

OK, strap yourself in.  At this stage, this repo uses [jake](/mde/jake) to make working with the BB10 development tools a little more streamlined.  Eventually this will be made into a separate tool, but for the moment it works.

### Step 1: Install the BB10 WebWorks Framework

At this stage, this uses the BB10 Webworks Framework but eventually a [cordova](http://cordova.apache.org/) port will also be provided.

So, get on and install Webworks:

<http://developer.blackberry.com/html5/documentation/blackberry10_webworkssdk_release_notes.html>

There's a few steps in getting going with webworks, and the documentation isn't too bad but it does take a little work.

## Step 2: Install Deps

As with any node project, you are going to need to have your dependencies covered.  So:

1. Install local deps using `npm install`
2. Make sure you have browserify available globally as it's used to compile the app: `[sudo] npm install -g browserify`
3. Also have jake installed globally: `[sudo] npm install -g jake`

## Step 3: Update Configuration

The jake build script makes use of a `config.json` file which you should tweak to match your environment.  This is mine, which should give you an idea of where to start and what to change:

```json
{
    "webworksPath": "/development/tools/bb-devtools/webworks-1.0.4.5",
    "devicePass": "password",
    "deviceIP": "169.254.0.1"
}
```

## Step 4: Browserify, Build and Push

If the BB10 dev tools have correctly been configured, and you have all the required node dependencies installed, then you should now be able to browserify, build and push the application to a BB10 device.

First, browserify `index.js` to `game.js`:

```
browserify index.js > game.js
```

Then run jake to automate the `bbwp` build process:

```
jake build
```

If successful, you can then run the push script to push it to a device:

```
jake push
```

From a workflow perspective, the build and push can be combined into a single step by calling `jake build push` also.


## license

BSD
