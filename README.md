# React Unit Test

## Requirements
* node `^4.2.2`
* npm `^3.0.0`

## Installation

```bash
$ git clone https://github.com/Dankoda/react-unit-test <my-project-name>
$ cd <my-project-name>
$ node -v # should be at least v4.2.2 if not nvm use v4.2.2 or higher
```

```bash
$ npm install
$ npm start
```
## 使うLibrary
**React**: javascript library by Facebook. component, virtual dom etc.  
**Redux**: データの流れを一方向にする  
**Enzyme**: React専用のテストツール  
  - reactで対象のコンポーネントをテスト  
  - render, shallow, and mount  

**Mocha**: テストランナー。describe() and it()  
**Chai**: アサーションライブラリ。expect, etc.  
**Sinon**:テストダブル. テスト対象外の機能などを擬似的に再現  
**Karma**:テスト実行環境。on node.js  
**Phantom**:コマンド（terminal）でブラウザを実行できる環境のこと。  

## Test

```bash
$ npm run test

$ (Component) Counter
    ✔ count component test
    ✖ renders as a <div> (skipped)
    ✖ renders with an <h2> that includes Counter label. (skipped)
    ✖ renders {props.counter} at the end of the sample counter <h2>. (skipped)
    ✖ renders exactly two buttons. (skipped)
    increment
      ✖ exists (skipped)
      ✖ is a primary button (skipped)
      ✖ Calls props.increment when clicked (skipped)
    Double Async Button
      ✖ exists (skipped)
      ✖ is a secondary button (skipped)
      ✖ Calls props.doubleAsync when clicked (skipped)
  (Componnet) Home
    ✔ home test
    ✖ Renders a welcome page (skipped)
    ✖ Renders an duck image (skipped)
```

## Test HomeView.js  

open HomeView.js (src/routes/Home/components/HomeView.js)  
open HomeView.spec.js (tests/routes/Home/components/HomeView.js)  

enyzmeの`render`メソッドを使ってcomponent のrenderテスト  

**必要なものをimport**  
```js
  //HomeView.spec.js

  import React from 'react'
  import { HomeView } from 'routes/Home/components/HomeView'
  import { render } from 'enzyme'
```

**フック**  
beforeEach: 毎回、1つ1つのテストが実行される前に呼ばれる。
```js
  describe('(Componnet) Home', () => {
    let _wrapper
    beforeEach(() => {
      _wrapper = render(<HomeView  />)
    })
    ...
  })
```

**レンダーテスト**  
elementのtagとtextを使って、renderされたかをチェック  
```js
  describe('(Componnet) Home', () => {
    ...

    it('Renders a welcome page', () => {
      const welcome = _wrapper.find('h4')
      expect(welcome).to.exist()
      expect(welcome.text()).to.equal('Welcome!')
    })
    it('Renders an duck image', () => {
      const duck = _wrapper.find('img')
      expect(duck).to.exist()
      expect(duck.attr('alt')).to.equal('This is a duck, because Redux!')
    })

  })
```

```bash
$ npm run test

$ (Component) Counter
    ✔ count component test
    ✖ renders as a <div> (skipped)
    ✖ renders with an <h2> that includes Counter label. (skipped)
    ✖ renders {props.counter} at the end of the sample counter <h2>. (skipped)
    ✖ renders exactly two buttons. (skipped)
    increment
      ✖ exists (skipped)
      ✖ is a primary button (skipped)
      ✖ Calls props.increment when clicked (skipped)
    Double Async Button
      ✖ exists (skipped)
      ✖ is a secondary button (skipped)
      ✖ Calls props.doubleAsync when clicked (skipped)
  (Componnet) Home
    ✔ home test
    ✔ Renders a welcome page
    ✔ Renders an duck image
```

## Test Counter.js

open Counter.js (src/routes/Home/components/Counter.js)  
open Counter.spec.js (tests/routes/Home/components/Counter.js)  

enzymeのshallowメソッドを使って、componentのレンダーとイベントのテスト  

**必要なものをimport**
```js
  //Counter.spec.js

  import React from 'react'
  import { Counter } from 'routes/Home/components/Counter'
  import { shallow } from 'enzyme'
```

**フック**   
必要なprops(親から渡ってくるデータ) を設定して、componentに渡す。  
```js
  describe('(Componnet) Counter', () => {
    let _props, _wrapper
    beforeEach(() => {
      _props = {
        doubleAsync: sinon.spy(),
        increment: sinon.spy(),
        counter: 5
      }
      _wrapper = shallow(<Counter {..._props}/>)
    })

    ...
  })
```

**レンダーテスト**  
ここでもelementのタグなどを使ってrenderされたかをチェック。  
上で渡したpropsもチェック。  

```js
  describe('(Componnet) Counter', () => {
    ...

    it('count component test', () => {
      expect(true).to.equal(true)
    })
    it('renders as a <div>', () => {
      expect(_wrapper.is('div')).to.equal(true)
    })
    it('renders with an <h2> that includes Counter label.', () => {
      expect(_wrapper.find('h2').text()).to.match(/Counter:/)
    })
    it('renders {_props.counter} at the end of the sample counter <h2>.', () => {
      expect(_wrapper.find('h2').text()).to.match(/5$/)
      _wrapper.setProps({ counter: 8 })
      expect(_wrapper.find('h2').text()).to.match(/8$/)
    })
    it('renders exactly two buttons.', () => {
      expect(_wrapper.find('button')).to.have.length(2)
    })
  })
```

**イベントテスト**  
実際にクリックイベントをシミュレートして、呼ばれるべき関数が呼ばれてるかテスト。  
increment
```js
  describe('increment', () => {
    let _button
    beforeEach(() => {
      _button = _wrapper.find('button').filterWhere(a => a.text() === 'Increment')
    })
    it('exists', () => {
      expect(_button).to.exist()
    })
    it('is a primary button', () => {
      expect(_button.hasClass('btn btn-primary')).to.be.true()
    })
    it('Calls props.increment when clicked', () => {
      _button.simulate('click')
      _props.increment.should.have.been.called()
    })
  })
```

doubleAsync
```js
  describe('Double Async Button', () => {
    let _button
    beforeEach(() => {
      _button = _wrapper.find('button').filterWhere(a => a.text() === 'Double (Async)')
    })
    it('exists', () => {
      expect(_button).to.exist()
    })
    it('is a secondary button', () => {
      expect(_button.hasClass('btn btn-secondary')).to.be.true()
    })
    it('Calls props.doubleAsync when clicked', () => {
      _button.simulate('click')
      _props.doubleAsync.should.have.been.called()
    })
  })
```
## 最終確認  
```bash
$ npm run test

$ (Component) Counter
    ✔ count component test
    ✔ renders as a <div> (skipped)
    ✔ renders with an <h2> that includes Counter label. (skipped)
    ✔ renders {props.counter} at the end of the sample counter <h2>. (skipped)
    ✔ renders exactly two buttons. (skipped)
    increment
      ✔ exists (skipped)
      ✔ is a primary button (skipped)
      ✔ Calls props.increment when clicked (skipped)
    Double Async Button
      ✔ exists (skipped)
      ✔ is a secondary button (skipped)
      ✔ Calls props.doubleAsync when clicked (skipped)
  (Componnet) Home
    ✔ home test
    ✔ Renders a welcome page
    ✔ Renders an duck image
```
