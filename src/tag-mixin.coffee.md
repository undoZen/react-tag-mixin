#ReactTagMixin

This mixin let you write React Virtual DOM DSL in a more compact way with (Iced)CoffeeScript.
```coffeescript
React = require('react')
TabMixin = require('react-tag-mixin')
module.exports = React.createClass
  mixins: [TagMixin]
  render: ->
    @div '#el-id.class1.class2',
      @p null,
        @span 'Hello, World!'
    #equals
    React.DOM.div id: 'el-id', class: 'class1 class2',
      React.DOM.p null,
        React.DOM.span null, 'Hello, World!'
```

    React = require('react')
    exports = module.exports = TagMixin = {}
    Object.keys(React.DOM).forEach (tag) ->
      TagMixin[tag] = (props, children...) ->
        if typeof props is 'string' and props[0] in ['.', '#']
          if (props.indexOf('#') > -1)
            props = (->
              id = ''
              className = ''
              props.split('.').reverse().forEach (c) ->
                [_c, _id] = c.split('#')
                return if not _c and not _id
                className = _c + ' ' + className
                id = _id if _id
              className = className.trim()
              return {id, className}
            )()
          else
            props = className: props.replace(/\.+/g, ' ').trim()
        if typeof props isnt 'object' or React.isValidComponent(props)
          children.unshift(props)
          props = null
        return React.DOM[tag].apply(React.DOM, [props].concat(children))
