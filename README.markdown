# Superpipes

This is a [Yahoo! Pipes](http://pipes.yahoo.com/pipes/) equivalent built with [Superfeedr](http://superfeedr.com/), hosted on [Heroku](http://www.heroku.com/). We built that just in case Y! decides to "sunset" pipes as well...

Here is what it does:

* Agregate feeds
* Displays the agregate (Atom, Json or HTML)

Also, it's realtime, and can be tweaked in any way you want to fit your needs!

We have deployed a [sample application](http://radiant-window-5539.herokuapp.com/) on heroku, with 2 aggregate feeds:

* a 'tech blog' feed, which combines ([json](http://radiant-window-5539.herokuapp.com/first/json), [atom](http://radiant-window-5539.herokuapp.com/first/atom), [html](http://radiant-window-5539.herokuapp.com/first)):
    * http://push-pub.appspot.com/feed
    * http://techcrunch.com/feed/
    * http://feeds.feedburner.com/ommalik
    * http://feeds2.feedburner.com/thenextweb
    * http://pandodaily.com/feed
    * http://news.ycombinator.com/rss
* An activity feed, which combines ([json](http://radiant-window-5539.herokuapp.com/profiles/json), [atom](http://radiant-window-5539.herokuapp.com/profiles/atom), [html](http://radiant-window-5539.herokuapp.com/profiles)):
    * https://github.com/superfeedr.atom
    * http://blog.msgboy.com/rss
    * http://blog.superfeedr.com/atom
    * http://superfeedr.tumblr.com/rss


## Deploying

You need:

* One [Heroku](http://www.heroku.com/) account
* The [Redis To Go Addon](https://addons.heroku.com/redistogo)
* The [Superfeedr Addon](https://addons.heroku.com/superfeedr). *Only available to beta testers*
* A basic Javascript knowledge to configure the whole thing.

For a simple use, **you won't have to write any code at all**!

### Clone

```bash
$ git clone git://github.com/superfeedr/superpipes.git
Cloning into superpipes...
remote: Counting objects: 412, done.
remote: Compressing objects: 100% (311/311), done.
remote: Total 412 (delta 78), reused 394 (delta 60)
Receiving objects: 100% (412/412), 237.10 KiB | 311 KiB/s, done.
Resolving deltas: 100% (78/78), done.
$ cd superpipes/
```
### Configure 

Open the `config/feeds.js` in your favorite text editor, and fill in the feeds.
Here is an example of configuration.

```javascript
exports.feeds = {
    'first': { // Each feed needs to have an unique key. It's is important as it will be used in the permalink for your feeds.
        secret: 'this is the secret for my first agregate', // Optional but you should put a random sentence here to make things secure.
        name: "My very first agregate feed", // Name of your agregate feed
        sources: [
            "http://push-pub.appspot.com/feed",
            "http://techcrunch.com/feed/",
            "http://feeds.feedburner.com/ommalik",
            "http://feeds2.feedburner.com/thenextweb",
            "http://pandodaily.com/feed",
            "http://news.ycombinator.com/rss"
        ] // this is the list of the feeds you want to agregate in one.
    },
    // ... Put more feeds here
}
```

The exported feeds object contains all the aggregate feeds you want to build with the sources to be used for each of them.

### Deploy

```bash
$ heroku create --stack cedar
$ git push heroku master
$ heroku addons:add redistogo:nano
$ heroku addons:add superfeedr
$ heroku config:add APP_HOST=<app name>.herokuapp.com 
$ heroku ps:scale web=1
```

Make sure you replace <app name> with your app's name.

### Profit

Once deployed, you should be good to start playing! **yay!**. 
*Now, a little warning: this aggregator will only agregate _future_ entries, which means, that right when you're going to try it, there won't be much in there.*

As you've configured your feeds in the first step, you can now access the aggregates in your browser. Each of them has 3 views: atom, json and html.
The base url to access them is `http://APP_HOST/<key>/format` (except for html, where you can just type `http://APP_HOST/<key>/`). The APP_HOST is your app's hostname and the `<key>` is the key you've chosen in the `config/feeds.js` file.

## TODO

* Use https to subscribe
* Implement signatures to verify origin of content (we can use a secret set in the heroku:config for example.)
* Add *filters* that can be applied to a feed, in the form of function called on each entry.
* Make sure the generated feeds are PubSubHubbub enabled as well
* Allow for an XMPP retrieval if an XMPP server is provided to connect to
* Make the HTML view realtime with socket.io for example!