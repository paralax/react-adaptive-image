# React Adaptive Image

[![Build Status](https://travis-ci.org/peterlazzarino/react-adaptive-image.svg?branch=master)](https://travis-ci.org/peterlazzarino/react-adaptive-image)

Lazy image loading with optional dynamic resizing based on viewport and screen pixel density. Deliver the right content for the right screen sizes.

This react component look at its own dom node and ask for a properly sized image based on its own width. The image request will not happen until it should be lazy loaded into the dom.

This library was created to balance image load time / quality and above the fold performance on a content heavy website.

# Requirements

For dynamic resizing, you must have an server or CMS that allows for resizing based on URL parameters. 
  
Some open source libraries that do this out of the box.
  
 - [GraphicsMagick for node.js](http://aheckmann.github.io/gm/)
 - [Image Steam](https://github.com/asilvas/node-image-steam)

# What does it do?

If you have an image that needs to be shown on multiple browser sizes (mobile, tablet, desktop, 5k TV) but don't want to sacrifice quality or performance based on the users device, this library will allow you to serve the correct image size at any resolution.

This component, in conjuction with an image server that allows resizing allows you to do that easily. I am a big fan of node-image-steam but that is up to you, and there is an easy way to configure URL generation shown below.

## How this works now

Here's one way you might solve this problem now. It requires the creation of multiple files and maintenance of multiple source tags and srcset attributes. It also doesn't allow for much flexibility if you want to add another set of targeted resolutions across your application.

```html
<picture>
  <source 
    media="(min-width: 650px)" 
    srcset="images/cat-small.png,
            images/cat-small@1.5x.png 1.5x,  
            images/cat-small@2x.png 2x">
  <source 
    media="(min-width: 465px)" 
    srcset="images/cat-smallest.png,
            images/cat-smallest@1.5x.png 1.5x
            images/cat-smallest@2x.png 2x">
  <img 
    src="images/cat.png" 
    srcset="images/cat@1.5x.png 1.5x,
            images/cat@2x.png 2x">
</picture>
```

with this library, you can solve the problem like this.

### Step 1

Configure your url generation to point at your image server. This is a basic setup that works with a local instance of node-image-steam.

```javascript

import React from 'react';
import ReactDOM from 'react-dom';
import { initImages } from 'react-adaptive-image';
import App from './App.jsx';

initImages({
    imageResolver: function(image){
        const imageServerURL = 'http://localhost:13337';
        return `${imageServerURL}/${image.fileName}/:/rs=w:${image.width}`
    }
})

ReactDOM.render(<App />, document.getElementById('react-root'));

```

### Step 2 

Pass the file name to the react component.

```javascript

import React, { Component } from 'react';
import AdaptiveImage from 'react-adaptive-image';

class HeaderImage extends Component {
  render() {
    return (
      <AdaptiveImage fileName={'header.jpg'} />
    );
  }
}

export default HeaderImage;

```

### Step 3

Ensure your images will take up some space in the DOM by default. Something like this can provide a base for images to scale to their containers but this obviously varies by image and be customized to fit your needs.

```css

img{
    display:block;
    width:100%;
}

```

# Props

### fileName

Type: string  Default: undefined  IsRequired: Yes

The name of the file you want to load.

### width

Type: int  Default: (Will be generated by the component)  IsRequired: No

Passing a value will override the dynamic width generation of the adaptive image script.

### height

Type: int  Default: (Will be generated by the component)  IsRequired: No

Passing a value will override the dynamic height generation of the adaptive image script. (Generated height will be 0 to retain aspect ratio).

### className

Type string  Default: undefined  IsRequired: No

Class name to pass to img element

### quality

Type int  Default: undefined  IsRequired: No

Quality parameter that you can pass if you would like to pass this through to the image server.

### altText

Type string  Default: undefined  IsRequired: No

Alt text for src attribute.

### itemProp

Type string  Default: undefined  IsRequired: No

ItemProp for [image schema microdata](http://schema.org/image)

### preLoad

Type boolean  Default: false  IsRequired: No

Setting this to true will skip lazyloading checks and load the src immediately on mount.

### src

Type boolean  Default: undefined  IsRequired: No

This will override the auto generating src and skip the imageResolver function so you can use the lazy loading functionality only.


# Usage

The initImages function takes an object for intialization that, in addition to the example above, can take some configuration for how the component will behave.

```

{
    imageResolver: function(image){},
    imageSettings = {
        noWidthReplacementSize: 200,
        maxWidth: 1366,
        lazyScrollThreshold: 300
    }
}

```
## imageResolver

### Receives an Image parameter with the following fields

**width** - the calculated width of the image in the DOM  
  
**height** - will be 0, unless passed into component. All dynamic resizes at this point will be by width with aspect ratio kept intact.  
  
**fileName** - the name of the file passed to the component.  
  
**quality** - the qualtiy of the file passed to the component.  
  
**altText** - alt text of the image.  

## imageSettings

**noWidthReplacementSize** - If an image is smaller than this size (200px defualt) the image will automatically be rendered at this width.  
  
**maxWidth** - The max width than an image can be rendered as. 
  
**lazyScrollThreshold** - Defines how far below the fold images should be loaded. By default an image 300px or less below the fold will have its src loaded with the result of the image renderer.  




