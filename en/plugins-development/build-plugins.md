# Summary
1. Define logic for plugins in [fontware](../api-reference/PluginsMeta.md#var-fontware), [backware](../api-reference/PluginsMeta.md#var-backware)
2. Declare it in [json-build-file/plugins](../buildIn-features/appLoader.core.md) or [ModuleManager.enablePlugins](../api-reference/ModuleManager.md#function-enableplugins-2-override)
3. Declare in [requestConfig/~/plugins](../api-reference/HyronService.md#function-requestconfig) to turn on or off plugins for this router

# Build in step-by-step

## Step 1 : Create a new plugins directory

create a new forder as bellow (or difference if you want - skip this step)

```
plugins
  └── plugins-name
        ├── src
        ├── lib
        ├── index.js
        ├── appcfg.yaml
        ├── plugins-name_fw.js
        └── plugins-bane_bw.js
```

## Step 2 : Create plugins structure

in plugins-name_fw.js & plugins-name_fw.js ( it's also called middleware )

```js
module.export = {
    handle(req, res, prev, cfg) {
        // do something useful when router is trigger
        return prev;
    },

    onCreate(cfg) {
        // prepare value and environment
    },

    checkout(done, cfg) {
        // check if have any change to revoke onCreate
        return false;
    },

    // filter typeof 'prev' that handle by this middleware
    typeFilter: ["object", "string"],

    // true if this middleware will be call on every router
    global: false
};
```

You don't need to implement all of method abort, you only need to implement the required methods. To see detail about Middleware, please see [API Reference](api-reference/PluginsMeta.md)

After that, you need to write logic for middleware. What do you want to do with this

➜ **Fact** : Hyron supports a mechanism that allows plugins to communicate with each other, and with that router itself, to increase the flexibility of plugins.

➜ **Fact** : By having access to the 'this' variable, on that range is the entire router

With this variable, you can access some of the default variables loaded by Hyron. These special variables start with a '\$' character

```js
function handle(req, res, prev, cfg) {
    this.$config // get module config
    this.$eventName // name to trigger this router
    this.$executer // get main-executer of this router
    ...
}
```

## Step 3 : Wrap middleware in index.js

index.js

```js
module.exports = {
    fontware: require("./plugin-name_fw.js"),
    backware: require("./plugin-name_bw.js")
};
```

If you as a member of Hyron organization, you could make your plugins **registered automatic when user install** it by this way :

**Step 1 : change your package name into : @hyron/plugins-name**
or, simple is init your package with Hyron organization from begin

package.json

```json
{
  "name": "@hyron/package-name",
  ...
}
```

**Step 2 : declare middleware in appcfg.yaml**

everything you need to make sure is plugins-name is a unique name

And as user, you also avoid named you modules with name is the same with this

```yaml
fontware:
    plugins-name: "@hyron/package-name/plugins-name_fw.js"

backware:
    plugins-name: "@hyron/package-name/plugins-name_bw.js"
```

## Step 4 : publish you plugins

In the future, we plan to build a separate hub so that users can share their modules, with stronger support for Hyron and users.

But now, you can used follow way to deploy your plugins

**npm** : check it out at [npm document](https://docs.npmjs.com/cli/publish)

```
npm publish --access public
```

**github** : check it out at [github document](https://help.github.com/articles/adding-an-existing-project-to-github-using-the-command-line/)

**bitsrc** : check it out at [bitsrc document](https://docs.bitsrc.io/docs/quick-start.html)

# Using Plugins

plugins can be plugged into your application very easily, we can use them in the following way

## Step 1 : Declare it in json-build-file/~/plugins

**Note** : If the plugins you want to install are from the hyron organization, it may already support auto import, you can skip this step.

You can declare plugins according to the form below used [appLoader](../buildIn-features/appLoader.md)

```json
// your instance
{
    "base_url": ...,
    "plugins" : {
        // point to plugins from root
        "plugins-name" : "plugins-path",
        // name of installed plugins package name
        "plugins-name" : "plugins-package-name",
        // url of plugins from git or tar file
        "plugins-name" : "plugins-url",

    }
}

```

In addition, you can also use [ModuleManager](../api-reference/ModuleManager.md) to declare it (**if you write in javascript** instead of json build file)

```js
const hyron = require('hyron');

var myApp = hyron.getInstance();

myApp.enablePlugins({
    "plugins-name" : "plugins-path",
});

...
```

## Step 2 : turn on or off it in requestConfig/~/plugins

You can control the status of plugins right on your service, by declaring their names in plugins properties in requestConfig.

```js
requestConfig(){
    return {
        method_name : {
            method : "get",
            plugins : [
                // turn on a plugins by name
                "plugins-name",
                // turn off global plugins by name
                "!global-plugins-name",
            ],
            
            // You can specify to enable or disable only the fontware or backware of a plugins used fontware & backware properties
            fontware : [
                "plugins-name",
            ],
            backware : [
                "plugins-name",
            ]
        }
    }
}
```

As you can see, you can specify to enable, disable plugins because its name is declared above

You can turn off a global plugins by adding '!' in front of that plugins name

You can specify the exact fontware or backware of a plugins to be run

You can set all routers with the [$all attribute](../api-reference/HyronService.md#function-requestConfig)

# Note

-   You should name plugins handle with format : name_fw.js if it is fontware, or name_bw.js if it is backware
-   Plugins should be identical to the declared name
-   The name of the plugins should be unique
-   Development plugins should also adhere to the principles of application design and development, to facilitate maintenance and expansion for later.

Next step : [View example](example.md)