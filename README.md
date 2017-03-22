# Threepenny-gui Flexbox

[![CircleCI](https://circleci.com/gh/barischj/threepenny-gui-flexbox.svg?style=shield)](https://circleci.com/gh/barischj/threepenny-gui-flexbox) [![Hackage](https://img.shields.io/hackage/v/threepenny-gui-flexbox.svg)](http://hackage.haskell.org/package/threepenny-gui-flexbox) [![Stackage Nightly](https://www.stackage.org/package/threepenny-gui-flexbox/badge/nightly?.jpg)](http://stackage.org/nightly/package/threepenny-gui-flexbox) [![Stackage LTS](https://www.stackage.org/package/threepenny-gui-flexbox/badge/lts?.jpg)](http://stackage.org/lts/package/threepenny-gui-flexbox)

Flexbox layouts for Threepenny-gui.

This library was written following the
wonderful
[A Complete Guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox) and
using the equally wonderful [Clay](https://hackage.haskell.org/package/clay)
library as a CSS domain specific language.

![](./example.png)

# Usage

## Properties

Ultimately we just want to set Flexbox properties on elements, both parent and
child elements. In CSS these properties would look like `flex-grow: 1;`.

We collect Flexbox properties that apply to the parent element, things like
`flex-direction`, in a `ParentProps` data type. Flexbox properties that apply to
child elements, things like `flex-grow`, are collected in a `ChildProps` data
type.
  
If you want `ChildProps` with `flex-grow: 1;` you can just do:

``` Haskell
flexGrow 1
```

You can define multiple properties using record syntax:

``` Haskell
order 1 { cflexGrow = 1, cFlexShrink = 2 }
```

Note that in the examples above we used `flexGrow` and `order` to return
`ChildProps` with given values set but also with default values set for all
other Flexbox properties, unless record syntax is used to override a property.

Some properties like `flexGrow` simply take an `Int` but others take a value
from the `Clay` library. Here's an example for `ParentProps`:

``` Haskell
display Clay.Display.inlineFlex { pFlexWrap = Clay.Flexbox.nowrap }
```

If you just want `ParentProps` or `ChildProps` with default values:

``` Haskell
parentProps :: ParentProps
childProps  :: ChildProps
```
  
## Setting Properties

Once you have your properties defined you'll want to apply them to elements. For
this you can use `setProps` which can be used with Threepenny's reverse function
application operator `#`:

``` Haskell
UI.div # set UI.text "foo" # setProps (order 1)
```

You can also convert `ParentProps` or `ChildProps` to a `[(String, String)]`
which
is
[how Threepenny expects CSS](http://hackage.haskell.org/package/threepenny-gui/docs/src/Graphics-UI-Threepenny-Core.html#style).
This can be done using `toStyle` which is defined in the typeclass `ToStyle`:

``` Haskell
UI.div # set UI.style (toStyle $ order 1)
```

### 'flex'

We provide a utility function `flex` which takes both parent and child elements
and their respective `ParentProps` and `ChildProps`, applies the respective
properties and then returns the parent element with children attached. We also
provide variants of this function which use default properties for parent or
children. Below is a full example demonstrating this, which produced the image
above.
  
``` Haskell
-- |Example of three divs using a flex-grow ratio of 1:2:1.
example :: Window -> UI ()
example w = void $
  flex_p (getBody w) $ [grow 1, grow 2, grow 1]

-- |Example "foo" div and given flex-grow value.
grow :: Int -> (UI Element, ChildProps)
grow n = (foo, flexGrow n)
  where foo = UI.div # set UI.text "foo"
                     # set UI.style [("background-color", "#F89406"),
                                     ("margin", "8px")]
```