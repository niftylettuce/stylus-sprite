Stylus-Sprite
=============

**Stylus-Sprite** is an extension for [Stylus](https://github.com/LearnBoost/stylus) which makes sprite images from Stylus tags. 
Actually it takes a image file (currently only PNG's are supported), places it to a sprite image and replaces the original
pointer in the CSS file with position coordinates according to the sprite image.

Installation
------------

    npm install stylus-sprite

Usage
-----

Consider the following Stylus CSS

    .block-elm
        background: url(sprite.png) no-repeat sprite("star.png");
        width: 25px;
        height: 25px;
        
After running Stylus-Sprite the resulting CSS would be something like

    .block_elm{
        background: url(sprite.png) no-repeat -25px -78px;
        width: 25px;
        height: 25px;
    }

And the image *sprite.png* would have *star.png* placed on position 25x78 px.

See test folder for complete example.

Dependencies
------------

  * [stylus](https://github.com/LearnBoost/stylus)
  * [node-gd](/andris9/node-gd) - GD bindings for Node.JS

JavaScript API
--------------

Creating the sprite consists of two phases - preparation and rendering.

The first step is to define *StylusSprite* object with required params

    var sprite = new StylusSprite({
        image_root: "./images",
        output_file:"sprite.png"
    });

Second steo would be hooking to the Stylus parsing phase with *Stylus.define*

    stylus...define('sprite', function(filename, option_val){
            // preparation phase
            return sprite.spritefunc(filename, option_val);
        });

A more sane version would be using bound function 

    sprite.spritefunc.bind(sprite, filename, option_val)
    
but as Stylus checks for function parameters proxying anonymous function is needed.

Finally when Stylus is finished rendering the CSS *sprite.build* must be run with it. 

    sprite.build(rendered_css, function(err, final_css){
        console.log(final_css);
    });

Somewhat complete example:

    var stylus = require("stylus"),
        StylusSprite = require("stylus-sprite")
        sprite = new StylusSprite({output_file:"sprite.png"});
    
    var css = "body.....";
    
    stylus(css).
        set('filename', 'test.css').
        define('sprite', function(filename, option_val){
            // preparation phase
            return sprite.spritefunc(filename, option_val);
        }).
        render(function(err, css){
            if (err) throw err;
            
            // rendering phase
            sprite.build(css, function(err, css){
                if (err) throw err;
                console.log(css);
            });
        });
