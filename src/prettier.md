# [第 16 周] Prettier your project

## 简介

> Prettier is an opinionated code formatter.

从官网的介绍中我们可以看到，首先 Prettier 的定位是一个**代码格式化**工具，并且比较任性（opinionated）。它移除了**几乎所有[1]**原始格式，来确保所有输出的代码符合一致。

就最初的设计目标来说，Prettier 面向的范围主要还是 Web 端的一些开发语言，如：
* [JSX](https://facebook.github.io/jsx/) 
* CSS, [Less](http://lesscss.org/) , and [SCSS](http://sass-lang.com/) 
* [GraphQL](http://graphql.org/) 
* 等...
不过作为一款代码格式化工具，Prettier 通过开放了插件（Beta）的能力，来对不同语言提供支持。

## 为什么要用
对于代码风格的圣战，没有一天真正停止过：
* Tab V.S. space
* single V.S. double quote
* Semicolons
* ...
就个人角度来说，这些争论有意义吗？我觉得是有的，因为不仅体现了一个程序员对细节的追求，还锻炼了探索、深挖、求真的能力。但是就团队的角度来说，我觉得这些争论是没有意义的，个人代码的风格大相径庭，从 ESlint 的规则数量就可以明显地看出这一点。如果从个人代码风格的角度出发，制定一套适用于团队层面的规范，讨论到最后，很难有一个统一的结果。
所以，为了统一团队代码风格，确实需要任性（opinionated），才能真正落地。

### 对新人友好
当有新人进入一个团队时，他不仅需要熟悉业务流程，兼顾遗留代码，如果在开发时还被一系列的**格式化**代码规则磕磕绊绊，是非常不友好的一件事。而通过配置 Prettier，按下一个按键就可以直接将代码格式化，不需要为了适配各种规则手动修改，能将更多精力放在代码质量层面。

### 易于适应
除了一些非常有争议的风格作为配置项，Prettier 内置的规则经过了多次修改讨论，对于大多数开发者来说，是比较容易接受的。

### 关于未来
我们引入一个新工具，肯定希望它是稳步上升的，如果用到一半就被废弃，后续的解耦成本就会成为一个新的问题。
所以一个工具的未来如何一个非常重要的考量点：
* 作为一个 Facebook 出品的工具，其雄厚的技术实力肯定是我们不容小觑的。
* 在 GitHub 上的 star 数，是老大哥 ESlint 的三倍之多，说明关注这个项目的人非常多。
* 再看项目的更新进度，基本每 1 - 2 天就会有新的提交，这个迭代速度说明更新以及问题解决的响应非常快。
* 还有 npm 上的下载量，达到了 400 万次 / 周，说明真正在使用的人也非常多。
从以上种种看出，这个项目的未来是充满光明的。

## 与 Eslint 
### 比较
每当提到 Prettier，ESlint 一定是一个绕不过去坎。甚至有人觉得两者是冲突的，是包含与被包含的关系。但是在我看来，他们只是存在某些功能上的重合，硬要从这个角度来说，顶多也是存在交集的关系。

要搞清楚两者的关系，可以先从定义上解读它们之间的区别：
* Prettier 的定位是一个代码格式化工具（code formatter），其侧重点在于格式化（format），其目的是让你的代码变得更好看（prettier），可读（readable）。
* ESlint 的定位一个校验工具（linting utility），其侧重点在于校验，其目的是让代码更一致和避免 bug。
> ESLint is a tool for identifying and reporting on patterns found in ECMAScript/JavaScript code, with the goal of making code more consistent and avoiding bugs.

简单来看，它们相同的地方主要有以下几点：
1. 都期望能够减少甚至避免产生 bug。
2. 都期望统一代码风格。
3. 都具备格式化代码的功能。

#### 统一代码风格
ESlint 规则成千上万，主要可以分为两大类：
1. 格式化代码规则（Formatting rules）：如  [max-len](http://eslint.org/docs/rules/max-len) , [no-mixed-spaces-and-tabs](http://eslint.org/docs/rules/no-mixed-spaces-and-tabs) , [keyword-spacing](http://eslint.org/docs/rules/keyword-spacing) , [comma-style](http://eslint.org/docs/rules/comma-style) ...
2. 代码质量规则（Code-quality rules）：如  [no-unused-vars](http://eslint.org/docs/rules/no-unused-vars) , [no-extra-bind](http://eslint.org/docs/rules/no-extra-bind) , [no-implicit-globals](http://eslint.org/docs/rules/no-implicit-globals) , [prefer-promise-reject-errors](http://eslint.org/docs/rules/prefer-promise-reject-errors) ...

理想情况下，如果 ESlint 规则设置的足够细致，基本能保证两个不同的开发者——采用相同的技术方案——在代码风格上 90% 的一致性。
但是对于 Prettier 来说，它工作的领域仅限于第一类——格式化代码，也就是说即使存在严重影响代码质量（Code-quality）的问题——比如[死循环](https://eslint.org/docs/rules/for-direction)——Prettier 不会做出任何反应。原因将会在下文中做出（Rationale）说明。

#### 格式化代码
ESlint 本身是一个代码校验工具，但是也提供了格式化代码的功能，通过添加 `--fix` 参数，可以将代码格式成符合自定义规则的样子。
Prettier 作为专业的格式化工具，内部自建了一套关于代码风格的规则，且这些规则无法被修改，除了暴露出的 19 个的配置项。这么做的目的，就是为了停止对各种个性化风格优劣的争论，从而将讨论的重点转移到这 19 个配置项中。

### 集成
在我们的日常开发过程中，已经习惯了使用 ESlint 来保障代码质量与统一风格。如果再加上 Prettier 作为代码格式化的工具，就可以起到如虎添翼的效果。但是因为 Prettier 内建的规则可能和 ESlint 的规则产生冲突，所以需要通过配置插件来解决这些问题。
- Eslint 检验自定义规则的同时，还能支持 Prettier 的规则；
- 我们希望 Prettier 格式化后的代码是符合 Eslint 规则的，但是因为 Prettier 内建规则是无法修改的，所以当两种的规则冲突时，需要以 Prettier 的规则为主。

通过下面的步骤可以达到上述条件所描述的：
1. 安装依赖
```bash
yarn add --dev eslint-config-prettier eslint-plugin-prettier
```
2. 修改 ESlint 配置
```json
// .eslintrc.json
{
  "extends": ["plugin:prettier/recommended"]
}
```

如果在项目中使用 Prettier，我们当然不希望每次都要手动去执行格式化的命令，通过定制 `pre-commit` 钩子，可以达到自动格式化的效果，详见[文档](https://prettier.io/docs/en/precommit.html)。
我个人最常用的是 `pretty-quick`，开箱即用。配置好后，每次将文件提交到 git 暂存区（stage）之前，都会自动对所有文件做格式化。这样一来，只要开发者按照规范 [2] 的流程走，就避免了代码格式不一致问题。
```bash
yarn add pretty-quick husky --dev
```
```json
// package.json
{
  "husky": {
    "hooks": {
      "pre-commit": "pretty-quick --staged"
    }
  }
}
```

除了通过使用 git hooks 的方式来格式化代码，也可以在编写代码的过程中，直接对其进行格式化。以 VSCode 为例：
1. 需要安装  [prettier-vscode](https://github.com/prettier/prettier-vscode) 插件。
2. 自定义格式化配置
```json
{
  "editor.formatOnSave": true,
  "editor.formatOnType": true,
  "editor.formatOnPaste": true
}
```
这一步也可以通过 [Formatting Toggle](https://marketplace.visualstudio.com/items?itemName=tombonnike.vscode-status-bar-format-toggle) 插件来实现。

## 设计原则
### 正确性（Correctness）
第一原则。作为一个代码格式化工具，如果想要让别人用的放心，在保证格式化代码后不会出现 bug 的同时，也必不能对原代码做任何入侵。这就是上文中提到的，即使出现了 bug，Prettier 也不会做出反应，**仅**关注如何更好地代码的原因。`eslint --fix` 则不同，它会去格式化那些违反规则的代码：
```js
return '' + value;
// after eslint --fix
return `${value}`;

{ color: color } 
// after eslint --fix
{ color }

let a = {}
// after eslint --fix
const a = {}

```

保证正确性的另一个方面，就是能够提前预知格式化后可能会产生的 bug。看下面一个例子：
```js
// semi.js
if (shouldAddLines) {
  [-1, 1].forEach(delta => addLine(delta * 20))
}
```

通过 `prettier semi.js --no-semi`命令对上述代码格式化，最后得到的结果是这样的：
```js
if (shouldAddLines) {
  ;[-1, 1].forEach(delta => addLine(delta * 20))
}
```

看上去似乎不符合规则，其实这么做事为了避免下述情况：
```js
 if (shouldAddLines) {
+  console.log('Do we even get here??')
   [-1, 1].forEach(delta => addLine(delta * 20))
 }

// after fomate 
if (shouldAddLines) {
  console.log('Do we even get here??')[-1, 1].forEach(delta => addLine(delta * 20))
}
```

通过上面的例子，说明了 Prettier 并非通过一刀切的方式对代码进行格式化，还会考虑格式化后的代码，在具体的应用场景中会如何。

### 易读性
在我们日常开发的过程中，经常会遇到一些涉及性能的问题。解法很多，但是我一般会选择更加容易让人看懂的那种。也就是说在一般情况下，为了代码的可维护性是可以牺牲一部分性能的。
**格式化代码不是目的，目的是为了让代码更加易读**。可以通过以下几个列子看出 Prettier 是如何遵守这一规则的：
1. 多行对象
对象的书写方式一般由以下两种：短对象可以写在一行里，而一些长对象或者类 CSS 对象我们习惯多行书写。
```js
// single-line
const user = { name: "John Doe", age: 18 };

// multi-line
const css = {
	fontSize: 12,
	color: "red",
	padding: 100
};
```
但是如果仅仅通过长短来格式化，就无法针对不同类型的对象做格式化处理，Prettier 也考虑到了这一点，可以通过下面的方式来自己选想要的格式化结果。
```js
// before
const use = { name: "John Doe",
	age: 18
};
// after
const user = { name: "John Doe", age: 18 };

// ---

// before
const use = {
	name: "John Doe",
	age: 18 };
// after
const user = {
  name: "John Doe",
  age: 18
};

```

2. 测试方法
在一些测试方法中，可能必有比较长的描述说明，导致长度（Print Width）超过了既定值。但是 Prettier 并不会去做强制换行处理，因为这样做是没有意义的，只会降低可读性。

3. 易加难减
到目前位置，Prettier 的配置规则仅仅只有 19 条，虽然添加一个规则不是什么难事，但是如果要将规则删除，就存在很大的问题了。你可以试想下如果将来某一天 Prettier 的一条规则被删除了，会发生什么？以 [bracket-spacing](https://prettier.io/docs/en/options.html#bracket-spacing)为例：
在删除之前，无论我的配置是 true or false，格式化话后的对象都是遵循一定规则的：
	- `true`   - Example: `{ foo: bar }`.
	- `false` - Example: `{foo: bar}`.
但是如果规则被删除，不复存在了，你的代码中可能会存在 `{foo: bar, }` 这样的结果，这是大家都不愿意看到的。就拿 PR 来说，我在代码比对的过程中看到的这个属于格式上的不统一，在我引入了 Prettier 之后，这个问题理应是不会存在的，渐渐大家就会失去对这个工具的信任。

为什么要举 bracket-spacing 这个例子哪，因为这是一个[连 Prettier 的作者也不知道为什么会存在的配置规则](https://github.com/prettier/prettier/issues/715#issuecomment-281096495)，但是它现在依旧存在着。

## 尾声
最后，借用我偶像 Dan 对 Prettier 的一段[描述](https://github.com/prettier/prettier/issues/40)来结尾：
> I just wanted to take a moment to say there are some of us who actually appreciate tools with limited scope that don’t attempt to solve everybody’s use case, and instead do a specific thing well.

## 说明
- [1]  不格式化的某些情况下的 empty lines 以及 multi-line object

* [2] 之所以强调规范一词，是因为笔者曾经遇到过，有开发者因为代码校验无法通过，通过直接删除 node_modules，使 git 钩子无效化的方式强制推送代码。