# react-select-element

## React Select Element

`react-select-element` implements standard `HTML` `<select />` behaviour, without using any `<form />` elements whatsoever. (It can, of course, be composed into other components which implement them.)

You can use it as-is, or you can use `extend` your own components from it, modifying its behaviour to suit your needs.

[An example implementation is available on GitHub.](https://github.com/sequencemedia/React.Select.Element.IO)

While the component appends some `className` attributes to its elements, the package does not contain any `CSS` stylesheets. The example implementation contains [a simple stylesheet](https://github.com/sequencemedia/React.Select.Element.IO/blob/master/public/assets/css/react-select-element.css) which can help you start your own.

### Using ES

```javascript
import Select from 'react-select-element'
```

### Using JS

```javascript
const Select = require('react-select-element')
```

### Implementing in React

Either:

```jsx
  <Select
    index={this.state.index}
    onChange={(index) => {
      this.setState({ index })
    }}
    tabIndex={0}
    accessKey='S'
    options={[
      { text: 'Letter A' },
      { text: 'Letter B' },
      { text: 'Letter C' },
      { text: 'Letter D' },
      { text: 'Letter E' }
    ]}
  />
```

Or:

```jsx
  <Select
    defaultIndex={0}
    tabIndex={0}
    accessKey='S'
    options={[
      { text: 'Letter A' },
      { text: 'Letter B' },
      { text: 'Letter C' },
      { text: 'Letter D' },
      { text: 'Letter E' }
    ]}
  />
```

Otherwise:

```jsx
  <Select
    disabled
  />
```

Finally:

```jsx
  <Select
    readOnly
  />
```

### Extending the component

#### 1. `<InfiniteSelect />`

In standard behaviour, when the options are visible, the user can move up and down through the options list by pressing the "arrow up" and "arrow down" keys on their keyboard. Movement will stop at the first item or the last item.

You want to modify that behaviour.

By pressing the "arrow up" key, the user should move through each item to the _first item_ in the list; then, by pressing again, they should move to the _last item_.

Similarly, by pressing the "arrow down" key, the user should move through each item to the _last item_ in the list; then, pressing again, they should move to the _first item_.

To achieve this, you can `extend` the component and override two of its methods.

```javascript
class InfiniteSelect extends Select {
  incrementActiveIndex () {
    const { activeIndex } = this.state
    const incremented = activeIndex + 1

    this.activeIndex(
      (incremented > this.upperBound) ? this.lowerBound : incremented
    )
  }

  decrementActiveIndex () {
    const { activeIndex } = this.state
    const decremented = activeIndex - 1

    this.activeIndex(
      (decremented < this.lowerBound) ? this.upperBound : decremented
    )
  }
}
```

[An example implementation is available on GitHub.](https://github.com/sequencemedia/React.Select.Element.IO) Clone that repository, install and start the package, then look for the example titled `Infinite Select Component`.

#### 2. `<SelectSelect />`

In standard behaviour, controlling components are only notified of a change to the selected `index` on `click` or keyboard `enter` events.

You want to modify that behaviour.

You want controlling components to be notified of a change whenever the the "arrow up" or "arrow down" keys are pressed. (In effect, each option is selected when the user moves through the list.)

To achieve this, you can extend the component and modify the same two methods as before.

```javascript
class SelectSelect extends Select {
  incrementActiveIndex () {
    super.incrementActiveIndex()

    const { activeIndex } = this.state

    this.selectIndex(
      Math.min(activeIndex + 1, this.upperBound)
    )
  }

  decrementActiveIndex () {
    super.decrementActiveIndex()

    const { activeIndex } = this.state

    this.selectIndex(
      Math.max(activeIndex - 1, this.lowerBound)
    )
  }
}
```

Invoking `super.incrementActiveIndex()` or `super.decrementActiveIndex()` in the overriding method ensures that existing behaviour remains unchanged, while the additional statements modify the behaviour of the component.

[An example implementation is available on GitHub.](https://github.com/sequencemedia/React.Select.Element.IO) Clone that repository, install and start the package, then look for the example titled `Select Select Component`.

#### 3. `<HiddenSelect />`

`react-select-element` does not use any `<form />` elements whatsoever.

You want to compose it into a `<form />`.

In this case, you've chosen to compose the `<Select />` into a controlling component which renders the `value` of the selected option into the `value` attribute of an `<input type='hidden' />` element.

```jsx
class HiddenSelect extends Component {
  state = {}

  handleIndexChange = (index) => {
    const { options, onChange } = this.props
    const { value } = options[index]

    this.setState({ value })
    onChange(index)
  }

  render () {
    const { value } = this.state

    return (
      <div className='hidden-select'>
        <Select
          {...this.props}
          onChange={this.handleIndexChange}
        />
        <input name='hidden-select' type='hidden' value={value} />
      </div>
    )
  }
}

HiddenSelect.propTypes = {
  ...Select.propTypes,
  onChange: PropTypes.func
}

HiddenSelect.defaultProps = {
  ...Select.defaultProps,
  onChange: () => {}
}

```

[Again, an example implementation is available on GitHub.](https://github.com/sequencemedia/React.Select.Element.IO) Clone that repository, install and start the package, then look for the example titled `Hidden Select Component`.
