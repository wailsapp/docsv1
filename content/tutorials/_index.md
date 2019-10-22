+++
title = "Tutorials"
date = 2019-08-29T04:56:56+10:00
weight = 15
chapter = true
+++

# Tutorials

A collection of tutorials to help understand how to use Wails.

## Template Overview

In this tutorial we go through the default Vue template to gain an understanding of how Wails works. It is a simple introduction to some of the core concepts of the framework.

[Start the tutorial](./template)

## Quote Generator

Building on the default template, we create a Quotes Generator. This introduces the concepts of binding a struct to your application as well as interacting with the Wails runtime in Go.

This is recommended for those who are proficient in Javascript and Go and are looking to quickly create an app. It builds on the Template Tutorial so it is recommended to complete that first.

[Start the tutorial](./quotes)

## Todo

What's a framework without a Todo app? We revisit the classic app, basing it on the [Todo MVC](http://todomvc.com/examples/vue/) version.

This is a comprehensive and advanced tutorial that only requires a basic knowledge of Javascript and Go. It covers all aspects of Wails and is recommended for people of all skill levels.

[Start the tutorial](./todo)

## CPU Usage App

In Package Main Episode 16, [Alex Pliutau](https://twitter.com/pliutau) builds a CPU Usage app with Wails. He covers struct binding, the events system and how to use 3rd party Javascript packages in your app. It's a really great tutorial!
<br/><br/>

<div class="videocontainer" style="width:525px; height:310px">
  <iframe width="525" height="310"
    src="https://www.youtube.com/embed/Dg9rUXxNV-c?ecver=1" frameborder="0"
    allow="encrypted-media"
    allowfullscreen>
  </iframe>
</div>

<br/>

The tutorial is also available in text format at Package Main's [episode 16 github repo](https://github.com/plutov/packagemain/tree/master/16-wails-desktop-app) or as a [Medium article](https://medium.com/js-dojo/building-a-desktop-app-in-go-using-wails-b7f5825f986a).

::: tip
This tutorial uses an older version of the Runtime which has had some slight changes since v0.18.0. Please read the [Reference Guide](../reference/#wails-runtime) for more information.
:::
