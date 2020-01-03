# Storybook Visual Regression boilerplate

<p align="center">
  <img width="500" alt="Storybook Visual Regression boilerplate" src="https://user-images.githubusercontent.com/6290720/71733711-8c758580-2e8d-11ea-95b2-b7f967c9816e.png" />
  <br />
  <br />
</p>

The storybook boilerplate which combines ReactJS, Typescript and Visual Regression test using [WebDriverIO](https://webdriver.io/)

[![Build Status](https://travis-ci.com/davidnguyen179/storybook-wdio.svg?branch=master)](https://travis-ci.com/davidnguyen179/storybook-wdio) [![CircleCI](https://circleci.com/gh/davidnguyen179/storybook-wdio.svg?style=svg)](https://circleci.com/gh/davidnguyen179/storybook-wdio) [![GithubCI](https://github.com/davidnguyen179/storybook-visual-regression-testing-boilerplate/workflows/CI/badge.svg)](https://github.com/davidnguyen179/storybook-visual-regression-testing-boilerplate/actions)

<hr />

* [Motivation](#motivation)
* [Getting started](#getting-started)
* [How to create React component & Story](#how-to-create-react-component---story)
  + [Structure](#structure)
  + [Component template](#component-template)
  + [Creating story](#creating-story)
* [Visual regression testing](#visual-regression-testing)
  + [Run selenium Docker image](#run-selenium-docker-image)
  + [Create entry test file](#create-entry-test-file)
  + [Run the visual regression test](#run-the-visual-regression-test)
  + [Debug visual regression test](#debug-visual-regression-test)
    - [Mac](#mac)
* [Contributors](#contributors)

## Motivation

The modern web development heavily depends on the ReactJS to build web components combines with Storybook which helps to create the Design System and the component base that can be reused between products. Especially, when working in a large team where the single component which is used by other products can be modified by a bunch of developers. Even though the changes are small which cannot be seen at the review stage, but it can affect other features or products. For this reason, this boilerplate is built to minimize the risk of the component modification that affects the product by using visual regression testing.

## Getting started

To run this project, you need to follow some steps:

1. Make sure you have [Docker]([https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)) & [NodeJS]([https://nodejs.org/en/](https://nodejs.org/en/)) installed on your machine
2. Pull the [standalone-chrome-debug]([https://hub.docker.com/r/selenium/standalone-chrome-debug](https://hub.docker.com/r/selenium/standalone-chrome-debug)) or [standalone-firefox-debug]([https://hub.docker.com/r/selenium/standalone-firefox-debug/](https://hub.docker.com/r/selenium/standalone-firefox-debug/)) docker image
3. Clone this repo
4. Install dependencies

```bash
npm install
```
5. Start storybook with development mode

```bash
npm run storybook
```

## How to create React component & Story

### Structure

```bash
my-react-component
|-- tests/
     |-- data/
         |-- story-1.spec.tsx
         |-- story-2.spec.tsx
         |-- story-3.spec.tsx
     |-- index.spec.ts
|-- index.tsx
|-- style.less
```

### Component template

**src/components/button/index.tsx**

```tsx
import * as React from 'react';
import * as style from './style.less';

export class Button extends React.Component<Props> {
  public render(): JSX.Element {
    const className = [style.container, style[this.props.size || '']];

    return (
      <button onClick={this.props.onClick} id="button" className={className.join(' ')}>
        {this.props.children}
      </button>
    );
  }
}

export type Props = DataProps & EventProps;

interface DataProps {
  /** Children node */
  children: string | React.ReactNode;
  /** Size of button */
  size?: 'small' | 'medium' | 'large';
}

interface EventProps {
  /** Click event */
  onClick?: (e: React.MouseEvent<HTMLElement>) => void;
}
```

**src/components/button/style.less**

```css
.container {
  background: blue;
  width: 100%;
}

.small {
  background: yellow;
}

.medium {
  background: green;
}

.large {
  background: red;
}
```

### Creating story

File name pattern: `src/components/<component-name>/tests/data/<test-case-name>.spec.tsx`

Example:

**src/components/button/tests/data/large.spec.tsx**

```tsx
import { Props } from '../..'; // import the Props from the component

export const test: Props = {
  children: 'large size',
  size: 'large',
};
```

**src/components/button/tests/data/small.spec.tsx**

```tsx
import { Props } from '../..'; // import the Props from the component

export const test: Props = {
  children: 'small size',
  size: 'small',
};
```

## Visual regression testing

This type of test produces the snapshots of the component as the `*.png` files.

*For example:*

Here is the visual regression test for `button` component

**button with large size**

<p>
  <img alt="button large" src="https://raw.githubusercontent.com/davidnguyen179/storybook-wdio/master/src/components/button/tests/expected/pc-chrome-button-large.png" />
</p>

**button with medium size**

<p>
  <img alt="button medium" src="https://raw.githubusercontent.com/davidnguyen179/storybook-wdio/master/src/components/button/tests/expected/pc-chrome-button-medium.png" />
</p>

**button with small size**

<p>
  <img alt="button small" src="https://raw.githubusercontent.com/davidnguyen179/storybook-wdio/master/src/components/button/tests/expected/pc-chrome-button-small.png" />
</p>

Here is the visual regression test for `text` component

**text with black background**

<p>
  <img alt="button medium" src="https://raw.githubusercontent.com/davidnguyen179/storybook-wdio/master/src/components/text/tests/expected/pc-chrome-text-with-black-background.png" />
</p>

**text with red background**

<p>
  <img alt="button small" src="https://raw.githubusercontent.com/davidnguyen179/storybook-wdio/master/src/components/text/tests/expected/pc-chrome-text-with-red-background.png" />
</p>

After completing the React component, to run the visual regression test, you need to do some set up
### Run selenium Docker image

> Make sure you started the Docker

To run the selenium of web drivers, you can choose either running Docker commands

```bash
docker run -d -p 4444:4444 -p 5900:5900 selenium/standalone-chrome-debug
```

or using `docker-compose.yml`

```bash
docker-compose up
```

**Advance:** You can customize export ports by arguments if default ports are already allocated

| Port | Default | Description |
|--|--|--|
| CHROME_MAIN_PORT | 4444 | port of selenium chrome |
| CHROME_DEBUG_PORT | 5900 | port of selenium chrome debug - for screen sharing |
| FIREFOX_MAIN_PORT | 5555 | port of selenium firefox |
| FIREFOX_DEBUG_PORT | 5901 | port of selenium firefox debug - for screen sharing |

Example
```bash
CHROME_MAIN_PORT=6666 CHROME_DEBUG_PORT=5909 docker-compose up
```

### Create entry test file

Create the file `src/components/<component-name>/tests/index.spec.ts` with code below

```ts
import { VisualRegressionTest } from '../../../../lib/test/visual-regression-test';
import * as style from '../style.less';

new VisualRegressionTest(__dirname, style.container).run();
```

> `style.container` is the className wrapped around the component

### Run the visual regression test

> To run the visual regression test, make sure your `storybook` started.

**PC**

```bash
npm run test:chrome src/components/<component-name>/tests/index.spec.ts
```

**Smartphone**

```bash
npm run test:chrome:sp src/components/<component-name>/tests/index.spec.ts
```

**Run all tests**

```bash
npm test
```

### Debug visual regression test

#### Mac

**Screen Sharing**

To debug visual regression testing

1. Open the `Screen Sharing`
```bash
chrome

Hostname: `YOUR_MACHINE_IP`:5900
Password: secret
```
2. Run test
3. Navigate to `Screen Sharing` to see the step by step running the test

## Contributors

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore-start -->
<!-- markdownlint-disable -->
<table>
  <tr>
    <td align="center"><a href="https://www.dzungnguyen.dev/about"><img src="https://avatars3.githubusercontent.com/u/6290720?s=460&v=4" width="100px;" alt=""/><br /><sub><b>Dzung Nguyen</b></sub></a><br /><a href="https://github.com/davidnguyen179/storybook-wdio/commits?author=davidnguyen179" title="Documentation">📖</a> <a href="https://github.com/davidnguyen179/storybook-wdio/commits?author=davidnguyen179" title="Code">💻</a> <a href="#ideas-davidnguyen179" title="Ideas, Planning, & Feedback">🤔</a> <a href="https://github.com/davidnguyen179/storybook-wdio/pulls?q=is%3Apr+reviewed-by%3Adavidnguyen179" title="Reviewed Pull Requests">👀</a> <a href="https://github.com/davidnguyen179/storybook-wdio/issues?q=author%3Adavidnguyen179" title="Bug reports">🐛</a></td>
    <td align="center"><a href="https://github.com/nvhoc"><img src="https://avatars3.githubusercontent.com/u/7335034?s=460&v=4" width="100px;" alt=""/><br /><sub><b>Hoc Nguyen</b></sub></a><br /><a href="https://github.com/davidnguyen179/storybook-wdio/commits?author=nvhoc" title="Code">💻</a> <a href="#ideas-nvhoc" title="Ideas, Planning, & Feedback">🤔</a> <a href="https://github.com/davidnguyen179/storybook-wdio/pulls?q=is%3Apr+reviewed-by%3Anvhoc" title="Reviewed Pull Requests">👀</a> <a href="https://github.com/davidnguyen179/storybook-wdio/issues?q=author%3Anvhoc" title="Bug reports">🐛</a></td>
    <td align="center"><a href="https://github.com/conandk"><img src="https://avatars3.githubusercontent.com/u/12934183?s=460&v=4" width="100px;" alt=""/><br /><sub><b>Khoa Do</b></sub></a><br /><a href="https://github.com/davidnguyen179/storybook-wdio/commits?author=conandk" title="Code">💻</a> <a href="https://github.com/davidnguyen179/storybook-wdio/pulls?q=is%3Apr+reviewed-by%3Aconandk" title="Reviewed Pull Requests">👀</a> <a href="https://github.com/davidnguyen179/storybook-wdio/issues?q=author%3Aconandk" title="Bug reports">🐛</a></td>
    <td align="center"><a href="http://haoict.github.io"><img src="https://avatars1.githubusercontent.com/u/7247750?v=4" width="100px;" alt=""/><br /><sub><b>Nguyen Van Hao</b></sub></a><br /><a href="https://github.com/davidnguyen179/storybook-wdio/commits?author=haoict" title="Code">💻</a></td>
  </tr>
</table>

<!-- markdownlint-enable -->
<!-- prettier-ignore-end -->
<!-- ALL-CONTRIBUTORS-LIST:END -->
