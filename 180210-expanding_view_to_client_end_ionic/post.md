# Expanding view to client end: Ionic - A framwork for mobile apps

Did you get a little confused with the title? Yeah, it's a little biased. I'm a backend developer and I am considering the potential readers to be backend developers. :) Okay, enough of this title thingy, let's get started.

Application frontend is a part of the application where I always wanted to spend the least of my time. I always wondered, why do I have to learn a different UI framework each time I start working on a different platform or framework. Each one of them endorse their own preference. C/C++ developers usually prefer QT, Tk is default for Python and Perl, XML is being used by several frameworks including Android. Switching platform becomes a living hell for developers working in one of these framworks (considering the fact that s/he doesn't have the previous experience in the destined framework). I am not saying that there is no other alternative, and user of those platform are forced to use the new frameworks, but it's difficult considering the resources readily available at developers disposal. Cross platform compatibility in the UI layer has always been a hidden itch for me. Ionic is certainly not the ultimate solution to it, but it really covers a large chunk of client market.

### Ionic, huh? what's about it

#### Short answer

It's a cross-platform app development framework that let's you develop client for your app in HTML, CSS and JS.

#### The long answer

According to Wikipedia,
> Ionic is a complete open-source SDK for hybrid mobile app development. Built on top of AngularJS and Apache Cordova, Ionic provides tools and services for developing hybrid mobile apps using Web technologies like CSS, HTML5, and Sass. Apps can be built with these Web technologies and then distributed through native app stores to be installed on devices by leveraging Cordova.

Ionic homepage describes it as follows,
> Free and open source, Ionic offers a library of mobile-optimized HTML, CSS and JS components and tools for building highly interactive native and progressive web apps. Built with Sass, optimized for AngularJS.

I believe that leaves nothing else to say about what it is anymore. It's popular in the wild and just like what you would assume, the following reasons are considered to be behind it's success

- Gradual learning curve
- RAD friendliness
- AngularJS support ( ;) it's built on Angular to be precise)
- Ease of deployment
- And, finally but surely open-source

### Potential usage scenarios

 - Native client development using server APIs
 - Fast client development for backend developers (RAD)
 - Native-like app development

### Benefits

The best thing about Ionic is that, it doesn't require another new domain to cover, thus reducing the learning curve effectively. It only uses web fundamentals (HTTP, CSS, JS) for developing the app.

### Wanna try it?

Just do the followings

To install,

```bash
$ sudo apt-get install npm
$ npm install -g ionic cordova
```

To fetch the template and run it,

```bash
$ ionic start app_name
$ cd app_name
$ ionic serve
```

(I didn't include the dependencies in the above steps. Just don't blame me if you get error for any unsatisfied dependency :) )

That's it!

Watch the template app run in the browser. That's all it takes to configure the core Ionic ecosystem for the first time. Next time when you'll need to create another template, you'll just run `ionic start app_name`.

In order to test the app on different platforms, you'll do

```bash
ionic upload
```

which will upload the app to *Ionic Cloud*. *Ionic Cloud* provides support for *prototyping*, *preview* and *development*.
In the process, you'll need to create an [ionic.io](http://ionic.io) account.

There are different tools available that aids in the development process. Significantly,

[Ionic Creator](http://ionic.io/products/creator?utm_source=framework&utm_medium=whatsNext&utm_campaign=Creator%20CTA) - For drag and drop prototyping

[Ionic lab](http://lab.ionic.io/) - For building and testing apps

I believe by now you understand why ionic is popular, yeah, it's because of it's simplicity. If you are searching for the downsides, maybe if you don't know Angular yet, it takes some time to get a hang of it. And, frankly speaking, Ionic is just a wrapper around [*Apache Cordova*](https://www.google.co.uk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwjM06a7g7LOAhXMqY8KHSaxCDEQFggeMAA&url=https%3A%2F%2Fcordova.apache.org%2F&usg=AFQjCNF3M_8XHFKa6nMMEUQ48OKqMoKZCQ). And it's very native like, customizable and got an active community. Not so bad after all. right?

