---
id: tutorial-react
title: Tutorial – React
layout: docs
category: Quick Start
permalink: docs/tutorial-react.html
next: common-js-testing
---

At Facebook, we use Jest to test [React](http://facebook.github.io/react/) applications. Let's implement a simple checkbox which swaps between two labels:

```javascript
// CheckboxWithLabel.js

/** @jsx React.DOM */
var React = require('react/addons');
var CheckboxWithLabel = React.createClass({
  getInitialState: function() {
    return { isChecked: false };
  },
  onChange: function() {
    this.setState({isChecked: !this.state.isChecked});
  },
  render: function() {
    return (
      <label>
        <input
          type="checkbox"
          checked={this.state.isChecked}
          onChange={this.onChange}
        />
        {this.state.isChecked ? this.props.labelOn : this.props.labelOff}
      </label>
    );
  }
});
module.exports = CheckboxWithLabel;
```

The test code is pretty straightforward; we use React's [TestUtils](http://facebook.github.io/react/docs/test-utils.html) in order to manipulate React components.

```javascript
// __tests__/CheckboxWithLabel-test.js

/** @jsx React.DOM */
jest.dontMock('../CheckboxWithLabel.js');
describe('CheckboxWithLabel', function() {
  it('changes the text after click', function() {
    var React = require('react/addons');
    var CheckboxWithLabel = require('../CheckboxWithLabel.js');
    var TestUtils = React.addons.TestUtils;

    // Render a checkbox with label in the document
    var checkbox = <CheckboxWithLabel labelOn="On" labelOff="Off" />;
    TestUtils.renderIntoDocument(checkbox);

    // Verify that it's Off by default
    var label = TestUtils.findRenderedDOMComponentWithTag(
      checkbox, 'label');
    expect(label.getDOMNode().textContent).toEqual('Off');

    // Simulate a click and verify that it is now On
    var input = TestUtils.findRenderedDOMComponentWithTag(
      checkbox, 'input');
    TestUtils.Simulate.change(input);
    expect(label.getDOMNode().textContent).toEqual('On');
  });
});
```

## Setup

Since we are writing code using JSX, a bit of one-time setup is required to make the test working:

```javascript
// package.json
  "dependencies": {
    "react": "*",
    "react-tools": "*"
  },
  "jest": {
    "scriptPreprocessor": "<rootDir>/preprocessor.js",
    "unmockedModulePathPatterns": ["<rootDir>/node_modules/react"],
    "testPathIgnorePatterns": ["<rootDir>/node_modules"]
  }
```

To enable the JSX transforms, we need to add a simple preprocessor file to run JSX over our source and test files using `react-tools` when they're required:

```javascript
// preprocessor.js
var ReactTools = require('react-tools');
module.exports = {
  process: function(src) {
    return ReactTools.transform(src);
  }
};
```

React is designed to be tested without being mocked and ships with `TestUtils` to help. Therefore, we use `unmockedModulePathPatterns` to prevent React from being mocked.

Finally, React has a lot of tests written in Jest. Since you probably don't want to run React tests in your application, you can blacklist all the tests from `node_modules` using `testPathIgnorePatterns`.

The code for this example is available at [examples/react/](https://github.com/facebook/jest/tree/master/examples/react).
