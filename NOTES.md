## 1. Geospatial React

This talk is about "geospatial React", which is a fancy way of saying "how to
make maps with React and Leaflet". I'm Kelly Innes and I work for Azavea in
Callowhill

## 2. Sandbox app

Just so I don't forget, I made a sandbox app to go along with this talk. It's
available on GitHub at github/kellyi/react-leaflet-sandbox, and I'll share the
link to these slides after the talk in case anybody would like to use them for
reference.

## 3.1 Azavea

I work for a company called Azavea in Callowhill that makes geospatial software
for civic and social good.

## 3.2 Civic Apps

At Azavea I work on the "Civic Applications" team, which builds GIS web
applications for clients, including lots of apps to map water.

We're project-centered and that has mean we've used a lot of different tech
stacks over the years. For the last few years we've used React + Redux.

## 4.1 Some projects

I wanted to show off a few of our projects.

## 4.2 Model My Watershed

Model My Watershed is a pretty feaure-rich app used by hydrologists and in
education. It uses Backbone + Marionette on the frontend.

## 4.3 The Nature Conservancy Geosite Framework

The Geosite Framework is a framework used by The Nature Conservancy to build
a network of region-specific mapping applications. It uses a library called
Dojo on the frontend.

## 4.4 Philadelphia Water Department Parcel Viewer

The parcel viewer is a React-Redux appl for the Philadelphia Water department
that enables users to see granular details about parcel stormwater billing. It
has not yet been released, but it will replace an existing Parcel Viewer we
originally build in Dot Net.

## 4.5 Greened Acre Retrofit Program

This is a still-unnamed app to support PWD's Greened Acre Retrofit Program,
which provides incentives for property owners to retrofit properties to help
mitigate stormwater runoff. It's also built in React + Redux.

## 5.1 Basic GIS Terminology

Before talking about React & Mapping I wanted to define some basic GIS
terminology.

## 5.2 Raster + Vector

Specifically, I wanted to distinguish between Rasters and Vectors.

## 5.3 Rasters / Tiles

For "rasters" think "tiles". For example, basemaps and overlays. If you open
the developer tools network tab in Chrome on a site that uses Leaflet, you can
see the app requesting tiles to assemble in the viewport into a map. These tiles
are little squares and they're typically represented with URLs which encode X,
Y, and Z coordinates.

## 5.4 Vectors / Shapes

For "vectors" think "shapes", like markers, lines, and polygons. These shapes
are drawn on top of a basemap and they have latitude / longitude coordinates
representing each of their points.

## 5.5 Mapping Libraries combine rasters + vectors

One thing mapping libraries do is combine rasters and vectors into maps.
Specifically this means that these libraries know how to request the correct
set of rasters for the viewport and they know how to draw vectors in the
correct places so that the shapes are related properly to the tiles.

## 6.1 JavaScript Mapping Libraries

There are a bunch of different JavaScript mapping libraries.

## 6.2 TLDR start with Leaflet

I recommend starting with Leaflet, which is the one I'll discuss using here.

## 6.3 Other JavaScript Mapping Libraries

There are some other alternatives, but each of them is either a little more
difficult or a little more costly to use.

- Google Maps API has a JavaScript interface, but it's now a paid API and
requires a credit card even for the free tier
- MapBox is really great, but it's also paid
- Esri ArcGIS JS API is a little more difficult to use and is tied specifically
to using Esri map servers
- OpenLayers is pretty good but it has a very verbose API

## 6.4 Leaflet has...

By contrast, Leaflet has

- a very nice API
- really great documentation
- lots of free basemaps
- numerous third-party plugins

## 7.1 Leaflet also has its own distinct way of managing state

## 7.2 React state overview

To highlight the contrast, I thought I'd give the most brief overview
of how React state works.

## 7.3 React state overview image

Essentially it's a loop that moves in one direction:

1. an event happens, such as the user clicking a button or an API request
returning a response

2. this causes application state to change, either via component state or in
Redux or whatever state management library

3. this new state gets changed into props which are passed into a component's
rendering function

4. React sees the change and rerenders the DOM, then waits for another event

## 7.4 State updates have to happen through circumscribed channels

Most importantly, in React state updates have to happen through circumscribed
channels so that React will know when and how to update the DOM

## 7.5 Leaflet state overview

Let's contrast that with Leaflet state. (This is an example of creating a
Leaflet map.)

## 7.6 Leaflet state overview

1. the map is an object

2. map state is embodied in properties of the map object

3. Leaflet manages how the DOM changes when the map's properties change

4. you call mutating methods to change the map's properties

## 7.7 this.leafletMap.setView

So for example: to change the map view, you can call a method called `setView`
which will update the map to give it a new center and new zoom level.

## 7.8 this.bikeNetworkLayer.addTo

To add a raster overlay you can call `addTo` on the layer object and give it
the Leaflet map as an argument.

## 7.9 Leaflet's programming paradigm

To sum up, Leaflet's programming paradigm is

- very object oriented: the map, layers, etc, are all instance of objects with methods
- not really declarative
- not at all functional
- very performant

## 8.1 Use React for most of the UI, but Leaflet for maps

In practice this means that we use React for most of the UI but use Leaflet for
maps.

## 8.2 Since React and Leaflet handle app state and DOM updates differently

However, since React and Leaflet handle application state and DOM updates in
different ways, there's some work to be done in order to stitch them together.

## 8.3 Using React component lifecycle methods in atypical ways

This work specifically takes the form of using React component lifecycle
methods to stitch React and Leaflet together. Sometimes these means using
lifecycle methods in atypical ways.

## 8.4 render() { return null; }

For example, sometimes you might see `render() { return null; }` in a component
which really does output elements on the DOM!

## 9.1 react-leaflet

The easiest way to get started doing this is by using a library called
react-leaflet, which adapts much of Leaflet's interface into React components.

## 9.2 please do not send pull requests

Interestingly, it intentionally only covers Leaflet's own interface -- and not
interfaces for Leaflet plugins! Apparently this decision is enough of a sore
subject that the library author added this note to the CONTRIBUTING.md guide:

> please do not send pull requests to add features that are not supported by Leaflet

## 9.3 Basic React-Leaflet Map component

This is what a basic React-Leaflet Map component looks like.

First, we have to import `Map` and `TileLayer` components from react-leaflet.

Then we can use these as regular JSX with the set of required props and
react-leaflet will create a Leaflet map for us -- which we can then update
using props as usual.

## 9.4 React-Leaflet interfaces

React-Leaflet also has interfaces for a bunch of different Leaflet elements:

- controls
- raster layers
- vectors
- geojson
- and event handlers to which you can pass your own functions

## 9.5 onMoveEnd / onZoomEnd

For instance, you can use `onMoveEnd` or `onZoomEnd` to set up your
component to do something whenever those events get fired by the Leaflet map.

## 9.6 React Leaflet works really well...

In general React Leaflet works really well if you're doing things which are
part of Leaflet's main API.

If also affords the possibility of using plain Leaflet methods when you need to
do so.

## 9.7 When would plain Leaflet methods be necessary?

When would plain Leaflet methods be necessary? Essentially: any time you're
doing something that is not covered by the React-Leaflet interface, like:

- adding a geocoder control
- adding an Esri ArcGIS layer
- using a third party Leaflet plugin
- getting or setting the basemap's bounding box

## 9.8 map Ref

To make it possible to call Leaflet methods on the map, you can set up a `ref`
to assign the map to a class variable.

## 9.9 ...

...then you can call Leaflet methods in React Component lifecycle methods.

## 9.10 ...

To do things like toggle an Esri ArcGIS layer on the map.

## 9.11 Bike network overlay

Here's an example of an ArcGIS raster overlay being shown on a react-leaflet map

## 10.1 React-Leaflet Source Code

Since React-Leaflet provides an abstraction of Leaflet's interface, it's interesting
to read its source code to see how it works.

One thing you'll notice is that it does interesting things with lifecycle methods.

## 10.2 Example: Map.jsx

For example, here are some annotations of the code from the Map.jsx component.

- `render` essentially just creates a div for the map
- `createLeafletElement` actually makes the Leaflet map using props
- `updateLeafletElement` compares old props to new props and then calls
Leaflet methods to make the appropriate updates
- `componentDidUpdate` calls `updateLeafletElement`

## 10.3 Example: MapControl.jsx

MapControl.jsx gets slightly weirder.

- `componentDidMount` calls a Leaflet method to add the control to the map
- `componnetDidUpdate` calls a method to make updates to the control
- `componentWillUnmount` has some code to ensure that the control gets removed
from the DOM when the component unmounts
- `render` returns null

## 10.4 Example: react-leaflet-control

react-leaflet-control is a third-party library which tries to simplify creating
custom controls.

- `render` returns null
- `componentDidUpdate` calls a method called `renderContent`
- `renderContent` calls React-DOM's `render` method to attach the React component
sent to the control to a div on the DOM

## 10.5 react-leaflet-control 2

When you use react-leaflet-control, you end up creating a React component that
works with the app's props and state BUT which gets rendered outside of the
app's component tree.

On the left is a layer legend component that uses React-Leaflet-Control; on the
right you can see the React-Dev-Tools depiction of how the layer legend gets
rendered.

## 11.1 at a certain point react-leaflet's abstractions may not be very beneficial

If you end up doing lots of stuff for which React-Leaflet doesn't have an
interface, at some point its abstractions may not be very beneficial

## 11.2 Easy-ish to write your own wrapper

It's easy-ish to write your own wrapper to use a Leaflet map in a React component
without react-leaflet. Link here has a code example which does just that.

## 11.3 render method

In the render method, you can set up a div on which to place the Leaflet map.

## 11.4 componentDidMount

In `componentDidMount` you can create the Leaflet map object on the div, and
then do whatever additional setup you'd like to do, like adding geocoders or
overlays.

## 11.5 `componentWillReceiveProps` or `componentDidUpdate`

Then in one of the lifecycle methods, you can call Leaflet methods directly
to make changes to the map in response to changes in props.

We have often used `componentWillReceiveProps` to do this work, but that method
is currently being deprecated, so a more appropriate place for it might be in
`componentDidUpdate` since that one also has access to old props and new props
for making a comparison.

## 12.1 Make your own Leaflet Controls

One last thing I wanted to show off was that you can also make your own React
Components which extend built-in Leaflet classes. For example, you can make your
own Leaflet controls which extend Leaflet's built-in `Control` class and will
work as React components.

## 12.2 Like a map legend...

Such as.... a map legend React Component extending the `Control` class. The code
here is just the ES6 version of the Leaflet documentation for creating a custom
map control. We're giving it an ID so that we can call the React-DOM `render`
method on it.

## 12.3 LeafletLegend class

Here's what a component using this Control could look like:

- it sets up its own `renderContent` class method
- it also uses `componentWillMount` to create an instance of the control div
and attach it to the LeafletMap

## 12.4 `renderContent`

In `renderContent`, it uses React-DOM render to attach JSX to the control div.

In `render` it just returns null, since the DOM changes are happening elsewhere.

## 12.5 layer legend

Here's what this looks like, in the bottom left corner. This looks and behaves
essentially the same as the React-Leaflet version: clicking the buttons in the
top cause Redux state changes, which then turn into UI and map updates.

## 13.1 Thank you!

## 14.1 Additional React / Leaflet / mapping resources

Wanted to close this with some additional React / Leaflet / Mapping resources

- first link here is to the sandbox app I've been referring to here
- second is to a React Native project which works sort of similarly, albeit
with the react-native-maps library instead of Leaflet
- third link is to a couple blog posts I wrote about using React and Leaflet
- last is a link to this presentation on the off chance you'd like to refer to it
