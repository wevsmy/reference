# geting-started

## Roadmap to lean Hyron

-   [Basic about javascript](https://www.w3schools.com/js/)
-   [A Hyron app example](app-example.md)
-   [Service development](service-development/README.md)
-   [Plugins development](plugins-development/README.md)
-   [api reference](api-reference/ModuleManager.md)
-   [Addons development](addons-development/README.md)

## Create app step-by-step

## _Step 1 : Create a app instance_

```javascript
const hyron = require("hyron");

var appInstance = Hyron.getInstance(3000, "localhost", "api");
```

instance is used to setup host, post server listen inside. If you have different app, run in same folder. getInstance() can make it more easy to management by declaring the prefix variable. In this case is 'api'

result of this step, sever will create a watcher at [http://localhost:3000/api](http://localhost:3000/api)

## _Step 2 : declare plugins to Hyron_

```javascript
appInstance.enablePlugins({
    plugin_name : require(plugins_path),
    ...
})
```

Plugins are functions that are used to handle work-related data input and output. In addition, it also extends your application, and makes your coding process simpler and more convenient.

You can found more of plugins in here : [npm registry](https://www.npmjs.com/search?q=Hyron)

Or, you also develop your own plugin extremely simply. See more at here : [how to develop a plugins ?](https://github.com/Hyron-group/reference/tree/3437eeb47ffad09baf95272f73ae3e71764436ce/plugins-developent/README.md)

If your app very simple, and don't request any plugins, please skip this step

## _step 3 : create own router_

create other class contain handle function

```javascript
class demo {
    ...
    showMyName(myName){
        return "hello "+myName;
    }
}
```

## step 4 : declare metadata for handle class ( module )

add method static **requestConfig()** to description about router

```javascript
class demo {
    static requestConfig() {
        return {
            showMyName : "get"
        }
    }

    showMyName(myName) {...}
}
```

So, after this step, you just registered a listener on showMyName() method. It also called is 'main handle'. every plugins will focused to this method.

![](https://imgur.com/K4OhtaE.png)

## _Step 5 : declare created module to Hyron & start server_

```javascript
appInstance.enableServices({
    demo: require(path_to_demo_class)
});

appInstance.startServer();
```

After this step, a router registered on http://localhost:3000/api/demo/show-my-name?myName={var}

That it, this is 5 step to create a simple Hyron app. It's pretty simple right?

With Hyron, you do not need to pay much attention to configuring the router, processing input variables, output, and no need to care about how an application runs.

Hyron will do it for you, with powerful plugins.

Hyron helps you save significant time developing your application, by focusing on processing logic, high packaging between plugins and services, allowing you to reuse them for many different projects. and among members of the community

Not only that, Hyron offers many other useful features, check it out at : [API Reference](./api-reference/README.md)

Next step : [How to develop own service ?](https://github.com/Hyron-group/reference/tree/3437eeb47ffad09baf95272f73ae3e71764436ce/service-developemnt/README.md)