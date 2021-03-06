React Flickity Component
=======================

[![Greenkeeper badge](https://badges.greenkeeper.io/theolampert/react-flickity-component.svg)](https://greenkeeper.io/)
[![build status](https://travis-ci.org/theolampert/react-flickity-component.svg?branch=master)](https://travis-ci.org/theolampert/react-flickity-component/)
[![dependencies](https://david-dm.org/theolampert/react-flickity-component.svg)](https://david-dm.org/theolampert/react-flickity-component)
[![styled with prettier](https://img.shields.io/badge/styled_with-prettier-ff69b4.svg)](https://github.com/prettier/prettier)

# Introduction:
A React.js Flickity component.

# Install:

```shell
npm install react-flickity-component --save
// Or
yarn add react-flickity-component
```

# Usage:

```javascript
// Commonjs
const Flickity = require('flickity');
// Or for ES2015 module
import Flickity from 'react-flickity-component'

const flickityOptions = {
    initialIndex: 2
}

function Carousel() {
  return (
    <Flickity
      className={'carousel'} // default ''
      elementType={'div'} // default 'div'
      options={flickityOptions} // takes flickity options {}
      disableImagesLoaded={false} // default false
      reloadOnUpdate // default false
      static // default false
    >
      <img src="/images/placeholder.png"/>
      <img src="/images/placeholder.png"/>
      <img src="/images/placeholder.png"/>
    </Flickity>
  )
}

```
### Example Usage:
See a codesandbox example here:
https://codesandbox.io/s/qlz12m4oj6

See an example in production with server side rendering [here](https://github.com/artsy/reaction/blob/master/src/Components/v2/CarouselV3.tsx#L160-L171)


### Props:

| Property             | Type       | Default | Description                                                   |
| -------------------- | -----------| --------|---------------------------------------------------------------|
| `className`          | `String`   | `''`    | Applied to top level wrapper                                  |
| `elementType`        | `String`   | `'div'` | Wrapper's element type                                        |
| `options`            | `Object`   | `{}`    | Flickity initialization opions                                |
| `disableImagesLoaded`| `Boolean`  | `false` | Disable call `reloadCells` images are loaded                  |
| `flickityRef`        | `Function` |         | Like `ref` function, get Flickity instance in parent component|
| `reloadOnUpdate`     | `Boolean`  | `false` | **Read next section before you set this prop.** Run `reloadCells` and `resize` on `componentDidUpdate`        |                      
| `static`             | `Boolean`  | `false` | **Read next section before you set this prop.** Carousel contents are static and not updated at runtime. Useful for smoother server-side rendering however the carousel contents cannot be updated dynamically.   |  

### How Does It Work

Under the hood, react-flickity-component uses [portal](https://reactjs.org/docs/portals.html) to render children slides inside Flickity instance. The need for portal is because after Flickity is initialized, new DOM nodes(mostly Flickity wrapper elements) would be created, this changes the DOM hierarchy of the parent component, thus any future update, whether it's originated from Flickity, like adding/removing slides, or from parent, like a prop changes, will make React fail to reconcile for the DOM snapshot is out of sync. 

#64 introduced a new prop to change the underlying render method: instead of portal, react-flickity-component will directly render children. This is create a smoother server-side rendering experience, but **please be aware using `static` prop possibly will cause all your future update to fail,** which means adding/removing slides will definitely fail to render, so use with caution.

However there is a fail-safe option `reloadOnUpdate`. It means every time there is a update, we tear down and set up Flickity. This will ensure that Flickity is always rendered correctly, but it's a rather costly operation and it will cause a flicker since DOM nodes are destroyed and recreated.


### Use Flickity's API and events

You can access Flickity instance with `flickityRef` prop just like `ref`, and use this instance to register events and use API.

```javascript


class Carousel extends React.Component {

  componentDidMount = () => {
    // You can register events in componentDidMount hook
    this.flkty.on('settle', () => {
      console.log(`current index is ${this.flkty.selectedIndex}`)
    })
  }

  myCustomNext = () => {
    // You can use Flickity API
    this.flkty.next()
  }

  render() {
    return (
      <Flickity flickityRef={c => this.flkty = c}>
        <img src="/images/placeholder.png"/>
        <img src="/images/placeholder.png"/>
        <img src="/images/placeholder.png"/>
      </Flickity>
      <Button onClick={myCustomNext}>My custom next button</Button>
    )
  }
}

```


# License Information:
[GPLv3](https://www.gnu.org/licenses/gpl-3.0.html)

Flickity may be used in commercial projects and applications with the one-time purchase of a commercial license.
http://flickity.metafizzy.co/license.html
